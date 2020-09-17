To set up the operator:
1. Install from Operator Hub
2. Create the PVC (metering-nfs.yaml) - this example assumes a StorageClass called gp2 `oc apply -f metering-nfs.yaml`
3. Create the MeteringConfig (operator-metering.yaml) `oc apply -f operator-metering.yaml`
