# Helm Charts to Deploy VOLTHA
This is a WIP

## Currently tested with

Follow the direction at https://github.com/ciena/voltha-access-edge, up to and including the `make helm-etcd-operator` command. This basically follows the standard 1.x install.

From there (after cloning this repo) do a 
```
helm install --atomic --debug --name voltha <path-to-repo>/voltha-helm-charts/voltha
```

## Known issues
- I can create a device, activate it, but can't delete it
- adapter list sometimes has a `adapter_sentinel` in it???
