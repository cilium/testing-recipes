# etcd stability testing

Simple yaml to bring up a 3 node etcd on a single node to be able to test etcd
failure scenarios.

## Instruction

1. Start minikube
2. `sed` replace `10.0.2.15` in `standalone-etcd.yaml` with the IP of the
   minikube VM
3. Create `etcd` named: `kubectl create namespace etcd`
4. Deploy: `kubectl -n etcd create -f standalone-etcd.yaml`
5. Verify etcd pods are up and running

```
$ kubectl -n etcd get pods
NAME      READY     STATUS    RESTARTS   AGE
etcd0-0   1/1       Running   0          4h
etcd1-0   1/1       Running   0          5h
etcd2-0   1/1       Running   0          1h
```

6. Connect cilium to new etcd `kubectl -n kube-system edit cm cilium-config`

   Adjust etcd endpoints and add:
   ```
    endpoints:
    - http://127.0.0.1:32379
    - http://127.0.0.1:33379
    - http://127.0.0.1:34379
   ```

7. Simulate etcd failures by restarting/deleting pods `while true; do kubectl -n etcd delete pod etcd2-0; done`
