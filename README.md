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
tar -xvf mirror-registry.tar.gz
./mirror-registry install --quayHostname bastion.sgwlq.sandbox374.opentlc.com --initPassword=redhat123
```

### place your RH pull secret, then add credentials for mirror-registry
```
mkdir ~/.docker
cat my-pull-secret-file > ~/.docker/config.json
podman login --authfile ~/.docker/config.json -u init -p redhat123 --tls-verify=false bastion.sgwlq.sandbox374.opentlc.com:8443
```

### create imageset-config.yaml
```
cat << EOF > imageset-config.yaml
---
kind: ImageSetConfiguration
apiVersion: mirror.openshift.io/v1alpha2
storageConfig:
  local:
    path: ./
mirror:
  platform:
    channels:
    - name: stable-4.14
      type: ocp
      minVersion: 4.14.19
      maxVersion: 4.14.20

  operators:
  - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.14
    packages:
    - name: quay-operator
      channels:
      - name: stable-3.12
    - name: odf-operator
      channels:
      - name: stable-4.14

  additionalImages:
  - name: registry.redhat.io/rhel8/support-tools

  helm: {}
EOF
```

### Launch oc-mirror with imageset-config.yaml (subsitute your fqdn)
```
oc mirror --config imageset-config.yaml docker://bastion.sgwlq.sandbox374.opentlc.com:8443/ocp4 --dest-skip-tls 
```

### clone playbook and role repo and change to mirror-demo directory
```
git clone https://github.com/broskos/mirror-demo.git
cd mirror-demo

```
### Install ansible and public galaxy collection 
```
sudo dnf install ansible -y
ansible-galaxy collection install infra.quay_configuration
```
### Deploy Quay in OpenShift
```
Install the RedHat Quay Operator, creat hub1 & hub2 namespaces, create 2 instances of quay registries, one in hub1 namespace and the other in hub2 namespace.  Launch the quay webUI, create the quayadmin user in each instance.
```

### Cd to mirror-demo directory, run playbook
```
cd ~/mirror-demo
ansible-playbook -i inventory_quay.yml quay_mirror_org.yml
```