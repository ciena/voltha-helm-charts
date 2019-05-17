# Helm Charts to Deploy VOLTHA
This is a WIP

## Installing charts

Follow the direction at https://github.com/ciena/voltha-access-edge, up to and including the `make helm-onos` command. This basically follows the standard 1.x install.

From there (after cloning this repo) do a 
```
helm install --atomic --debug --name voltha <path-to-repo>/voltha-helm-charts/voltha
helm install --atomic --debug --name adapters <path-to-repo>/voltha-helm-charts/voltha-adapter-simulated
```

### Configuring the number of R/O and R/W cores
By default the charts will create one (1) R/O and two (2) R/W cores. This can be configured by overriding two values 
- `replicas.ro_core` specifies the number of R/O core replicas, just like normal
- `replicas.rw_core` specifies the number of **pairs** of R/W cores, so a value of one (1) gets you one (1) pair which is two (2) instances. A value of two (2) gets you two (2) pairs which is four (4) instances.

### External dependencies
VOLTHA expects a `kafka` and `etcd` cluster to exist (it actually starts the etcd-cluster). These external dependencies can be configured for a specific deployment by overriding the values below.
```
# Define connectivity to Kafka
kafka:
  adapter:
    service: cord-kafka.default.svc.cluster.local
    port: 9092
  cluster:
    service: cord-kafka.default.svc.cluster.local
    port: 9092

# Define connectivity to etcd
etcd:
  service: etcd-cluster-client.default.svc.cluster.local
  port: 2379

# Define controller to which to connect
controller:
  service: onos-openflow.default.svc.cluster.local
  port: 6653
```

## Delete VOLTHA charts
```
helm delete --purge adapters voltha
```

## Known issues
[JIRA](https://jira.opencord.org/issues/?jql=status%20not%20in%20%28closed%2C%20Done%2CResolved%29%20and%20labels%20in%20%28helm%29%20and%20affectedVersion%20in%20%28%22VOLTHA%20v2.0%22%29)

## Installing and Configuring `voltctl`
```
sudo wget https://github.com/ciena/voltctl/releases/download/0.0.0-dev/voltctl-0.0.0-dev-linux-x86_64 -O /usr/bin/voltctl
voltctl completion bash | sudo tee /etc/bash_completion.d/voltctl > /dev/null
source <(voltctl completion bash)
mkdir $HOME/.volt
voltctl -a v2 -s voltha-api.voltha.svc.cluster.local:55555 config > $HOME/.volt/config
```

## Troubleshooting
### Restart voltha-api-server (f.n.a. afrouter)
if you are getting an error like
```bash
$ voltctl version
rpc error: code = Unknown desc = Unable to route method 'GetVoltha'
panic: rpc error: code = Unknown desc = Unable to route method 'GetVoltha'

goroutine 1 [running]:
main.main()
    /home/ubuntu/voltha-access-edge/voltctl/src/github.com/ciena/voltctl/cmd/voltctl.go:48 +0x56d
```

Then you can restart the API server by scaling it to 0 and then back to 1
```bash
kubectl scale --replicas=0 deployment -n voltha voltha-api-server
kubectl scale --replicas=1 deployment -n voltha voltha-api-server
```

Before scaling back to `1` be sure the current instance is gone using `kubectl get -n voltha pods`
