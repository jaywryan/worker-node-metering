# Worker node metering

The purposes of this repository is to refine the the `cluster-cpu-capacity` [Report](https://github.com/kube-reporting/metering-operator/blob/master/Documentation/reports.md) and only pull out the worker nodes for purposes of subscription managment.

Guidance from [writing-custom-queries](https://github.com/kube-reporting/metering-operator/blob/master/Documentation/writing-custom-queries.md) in the [metering-operator](https://github.com/kube-reporting/metering-operator) project was used to create this report.

The queries that are used for that report have been duplicated and modified to only pull the worker node data from Prometheus.

The idea is that this would run in a cluster and the endpoint can be queried monthly, quarterly, etc... and used for billing purposes.

The cluster-cpu-capacity report averages the number of CPUs between the last scheduled time and current scheduled time.

# Configuration

These CRD's assume the Metering operator is installed on an OpenShift cluster. For help with that, see the [operator-setup](operator-setup) folder.

The sample Report is `R-test-cluster-worker-cpu-capacity.yml` - this report is currently configured to run hourly, during minute 36. It can be tailored to run on whatever schedule makes sense.

In general:
* Report CRDs are prefixed with R.
* ReportDataSource CRDs are prefixed with RDS.
* ReportQuery CRDs are prefixed with RQ.

# Installation

``` shell
# git clone https://github.com/jaywryan/worker-node-metering
# cd worker-node-metering
# kubectl create -n $METERING_NAMESPACE -f ./reports
```

# Query the report

The reports are exposed via the API endpoint of the metering operator.
``` shell
METERING_ROUTE_HOSTNAME="$(oc -n $METERING_NAMESPACE get routes metering -o jsonpath='{.spec.host}')"

TOKEN="$(oc -n $METERING_NAMESPACE serviceaccounts get-token reporting-operator)"

REPORTNAME=test-cluster-worker-cpu-capacity
curl -H "Authorization: Bearer $TOKEN" -k "https://$METERING_ROUTE_HOSTNAME/api/v1/reports/get?name=$REPORTNAME&namespace=$METERING_NAMESPACE&format=csv"
```
# Sample output
![Sample output](images/sampleOutput.png)

# Data

`period_start` - The begining timestamp for the particular reporting period

`period_end` - The ending timestamp for the reporting period

`total_cluster_capacity_cpu_core_seconds` - The total number vCPU seconds (# of cores per second of the reporting period) for the cluster

`avg_cluster_capacity_cpu_cores` - The average of the raw vCPU data (collected every minute) during the reporting period

`avg_node_count` - The average number of worker nodes available during the reporting period.
