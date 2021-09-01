
YCIT020 Linkerd mTLS  demo

1 - Installing  the demo app

   Install emojivoto into the emojivoto namespace by running:

   curl -sL run.linkerd.io/emojivoto.yml | kubectl apply -f - 

2 - Before we mesh it, let’s take a look at the app.

   kubectl -n emojivoto port-forward svc/web-svc 8080:80


   Now visit http://localhost:8080


3 - Add Linkerd to emojivoto app

   kubectl get -n emojivoto deploy -o yaml \  
      | linkerd inject - \
      | kubectl apply -f -

4 - Check if everything worked 

   linkerd -n emojivoto check --proxy



5 - Validating mTLS with tshark

    add the debug sidecar by running:

   curl -sL https://run.linkerd.io/emojivoto.yml \
     | linkerd inject --enable-debug-sidecar - \
     | kubectl apply -f -


6 - establish a remote shell directly in the debug container of a pod in the voting service with:

  kubectl -n emojivoto exec -it \
     $(kubectl -n emojivoto get po -o name | grep voting) \
     -c linkerd-debug -- /bin/bash

    ####Once we’re inside the debug sidecar, the built-in tshark command can be used to inspect the raw packets on the network interface

    tshark -i any -d tcp.port==8080,ssl | grep -v 127.0.0.1


