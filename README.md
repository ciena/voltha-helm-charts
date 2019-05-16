# Helm Charts to Deploy VOLTHA
This is a WIP

## Currently tested with

Follow the direction at https://github.com/ciena/voltha-access-edge, up to and including the `make helm-onos` command. This basically follows the standard 1.x install.

From there (after cloning this repo) do a 
```
helm install --atomic --debug --name voltha <path-to-repo>/voltha-helm-charts/voltha
```

## Known issues
- I can create a device, activate it, but can't delete it
- adapter list sometimes has a `adapter_sentinel` in it???

## Installing and Configuring `voltctl`
```
sudo wget https://github.com/ciena/voltctl/releases/download/0.0.0-dev/voltctl-0.0.0-dev-linux-x86_64 -O /usr/bin/voltctl
voltctl completion bash | sudo tee /etc/bash_completion.d/voltctl > /dev/null
source <(voltctl completion bash)
mkdir $HOME/.volt
voltctl -a v2 -s voltha-api.voltha.svc.cluster.local:55555 config > $HOME/.volt/config
```
