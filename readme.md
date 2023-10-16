1. Install istio service mesh from
refer: https://istio.io/latest/docs/setup/getting-started/

  curl -L https://istio.io/downloadIstio | sh -

  cd istio-1.19.3

  export PATH=$PWD/bin:$PATH

  istioctl install --set profile=demo -y

  kubectl label namespace test istio-injection=enabled


2. Once this is done ensure that you install
refer : https://github.com/kubernetes/kubectl/issues/1338

  gcloud components install gke-gcloud-auth-plugin

  export USE_GKE_GCLOUD_AUTH_PLUGIN=True

  gcloud container clusters get-credentials <YOUR_CLUSTER> --region <YOUR_REGION>

3. If pod is not getting deployed to a namespace when istio is enabled then there might be some reason check events of that namespace

If you are deploying to test namespace then run

  kubectl get events -n test

4. I was getting logs which said it was unable to deploy

admission webhook "warden-validating.common-webhooks.networking.gke.io" denied the request: GKE Warden rejected the request because it violates one or more constraints. Violations details: {"[denied by autogke-default-linux-capabilities]":["linux capability 'NET_ADMIN' on container 'istio-init' not allowed; Autopilot only allows the capabilities: 'AUDIT_WRITE,CHOWN,DAC_OVERRIDE,FOWNER,FSETID,KILL,MKNOD,NET_BIND_SERVICE,NET_RAW,SETFCAP,SETGID,SETPCAP,SETUID,SYS_CHROOT,SYS_PTRACE'."]} Requested by user: 'system:serviceaccount:kube-system:replicaset-controller', groups: 'system:serviceaccounts,system:serviceaccounts:kube-system,system:authenticated'.

To fix this use the below command

  gcloud container clusters update autopilot-cluster-1 --region us-central1 --project angelic-hexagon-307814 \
      --workload-policies=allow-net-admin --location-policy=ANY# istio-service-mesh
