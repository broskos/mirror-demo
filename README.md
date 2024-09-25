### get and install oc-mirror
```
wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/latest/oc-mirror.rhel9.tar.gz
tar -xvf oc-mirror.rhel9.tar.gz
mkdir -p ~/.local/bin
mv oc-mirror ~/.local/bin
chmod +x ~/.local/bin/oc-mirror
```
### get and install mirror-registry
```
wget https://developers.redhat.com/content-gateway/rest/mirror/pub/openshift-v4/clients/mirror-registry/latest/mirror-registry.tar.gz
tar -xvf oc-mirror.rhel9.tar.gz
./mirror-registry install --quayHostname bastion.sgwlq.sandbox374.opentlc.com --initPassword=redhat123
```

### place your RH pull secret, then add credentials for mirror-registry
```yaml
mkdir ~/.docker
cat my-pull-secret-file > ~/.docker/config.json
podman login --authfile ~/.docker/config.json -u init -p redhat123 bastion.sgwlq.sandbox374.opentlc.com
```