# kubectl-mytop

This is a simple CLI that provides an overview of the resource requests, limits, and utilization in a Kubernetes cluster. It attempts to combine the best parts of the output from `kubectl top` and `kubectl describe` into an easy to use CLI focused on cluster resources.

## Installation
Go binaries are automatically built with each release by [GoReleaser](https://github.com/goreleaser/goreleaser). These can be accessed on the GitHub [releases page](https://github.com/chlyyangwei/kubectl-mytop) for this project.
```
wget https://raw.githubusercontent.com/chlyyangwei/kubectl-mytop/master/kubectl-mytop -O /usr/local/bin/kubectl-mytop 

chmod u+x /usr/local/bin/kubectl-mytop 
```
## Usage
By default, kubectl-mytop will output a list of nodes with the total CPU and Memory resource requests and limits for all the pods running on them. For clusters with more than one node, the first line will also include cluster wide totals. That output will look something like this:

```
# kubectl mytop 
NODE                              CPU REQUESTS   CPU LIMITS     MEMORY REQUESTS   MEMORY LIMITS
*                                 5344m (0%)     12044m (0%)    6310Mi (0%)       18112Mi (0%)
ap-southeast-5.192.168.0.144      2462m (61%)    7322m (183%)   2888Mi (20%)      10392Mi (72%)
ap-southeast-5.192.168.1.124      2382m (59%)    4722m (118%)   2398Mi (16%)      7720Mi (53%)
virtual-kubelet-ap-southeast-5a   500m (0%)      0Mi (0%)       1024Mi (0%)       0Mi (0%)
```

### Including Pods
For more detailed output, kubectl-mytop can include pods in the output. When `-p` or `--pods` are passed to kubectl-mytop, it will include pod specific output that looks like this:

```
# kubectl mytop  --pods -n default
NODE                              POD                                      CPU REQUESTS   CPU LIMITS    MEMORY REQUESTS   MEMORY LIMITS
*                                 *                                        1500m (0%)     2000m (0%)    1536Mi (0%)       1024Mi (0%)
                                                                                                                          
ap-southeast-5.192.168.0.144      *                                        250m (6%)      1000m (25%)   512Mi (3%)        512Mi (3%)
ap-southeast-5.192.168.0.144      my-nettools-79b68d5d89-c6vzj             250m (6%)      1000m (25%)   512Mi (3%)        512Mi (3%)
ap-southeast-5.192.168.0.144      sample-app-7cfb596f98-zbt7c              0Mi (0%)       0Mi (0%)      0Mi (0%)          0Mi (0%)
                                                                                                                          
ap-southeast-5.192.168.1.124      *                                        1250m (31%)    1000m (25%)   1024Mi (7%)       512Mi (3%)
ap-southeast-5.192.168.1.124      aes-gotools-78f9cbc68c-j6528             250m (6%)      0Mi (0%)      512Mi (3%)        0Mi (0%)
ap-southeast-5.192.168.1.124      nginx-deployment-basic-776544c85-8dp7h   1000m (25%)    1000m (25%)   512Mi (3%)        512Mi (3%)
                                                                                                                          
virtual-kubelet-ap-southeast-5a   *                                        0Mi (0%)       0Mi (0%)      0Mi (0%)          0Mi (0%)
virtual-kubelet-ap-southeast-5a   nginx1                                   0Mi (0%)       0Mi (0%)      0Mi (0%)          0Mi (0%)
```

### Including Utilization
To help understand how resource utilization compares to configured requests and limits, kubectl-mytop can include utilization metrics in the output. It's important to note that this output relies on [metrics-server](https://github.com/kubernetes-incubator/metrics-server) functioning correctly in your cluster. When `-u` or `--util` are passed to kubectl-mytop, it will include resource utilization information that looks like this:

```
# kubectl mytop  --util
NODE                              CPU REQUESTS   CPU LIMITS     CPU UTIL     MEMORY REQUESTS   MEMORY LIMITS   MEMORY UTIL
*                                 5344m (0%)     12044m (0%)    706m (0%)    6310Mi (0%)       18112Mi (0%)    14127Mi (0%)
ap-southeast-5.192.168.0.144      2462m (61%)    7322m (183%)   563m (14%)   2888Mi (20%)      10392Mi (72%)   8542Mi (59%)
ap-southeast-5.192.168.1.124      2382m (59%)    4722m (118%)   144m (3%)    2398Mi (16%)      7720Mi (53%)    5566Mi (38%)
virtual-kubelet-ap-southeast-5a   500m (0%)      0Mi (0%)       0m (0%)      1024Mi (0%)       0Mi (0%)        19Mi (0%)
```

### Displaying Available Resources
To more clearly see the total available resources on the node it is possible to pass the `--available` option
to kubectl-mytop, which will give output in the following format

```
# kubectl mytop  --available
NODE                              CPU REQUESTS            CPU LIMITS              MEMORY REQUESTS           MEMORY LIMITS
*                                 192002656m/192008000m   191995956m/192008000m   671111196Mi/671117506Mi   671099394Mi/671117506Mi
ap-southeast-5.192.168.0.144      1538m/4000m             -3322m/4000m            11495Mi/14383Mi           3991Mi/14383Mi
ap-southeast-5.192.168.1.124      1618m/4000m             -722m/4000m             12085Mi/14483Mi           6763Mi/14483Mi
virtual-kubelet-ap-southeast-5a   191999500m/192000000m   1Mi/1Mi                 671087616Mi/671088640Mi   671088640Mi/671088640Mi
```

### Including Pods and Utilization
For more detailed output, kubectl-mytop can include both pods and resource utilization in the output. When `--util` and `--pods` are passed to kubectl-mytop, it will result in a wide output that looks like this:

```
# kubectl mytop --pods --util -n default
NODE                              POD                                      CPU REQUESTS   CPU LIMITS    CPU UTIL   MEMORY REQUESTS   MEMORY LIMITS   MEMORY UTIL
*                                 *                                        1500m (0%)     2000m (0%)    0Mi (0%)   1536Mi (0%)       1024Mi (0%)     0Mi (0%)
                                                                                                                                                     
ap-southeast-5.192.168.0.144      *                                        250m (6%)      1000m (25%)   1m (0%)    512Mi (3%)        512Mi (3%)      18Mi (0%)
ap-southeast-5.192.168.0.144      my-nettools-79b68d5d89-c6vzj             250m (6%)      1000m (25%)   1m (0%)    512Mi (3%)        512Mi (3%)      6Mi (0%)
ap-southeast-5.192.168.0.144      sample-app-7cfb596f98-zbt7c              0Mi (0%)       0Mi (0%)      1m (0%)    0Mi (0%)          0Mi (0%)        13Mi (0%)
                                                                                                                                                     
ap-southeast-5.192.168.1.124      *                                        1250m (31%)    1000m (25%)   0m (0%)    1024Mi (7%)       512Mi (3%)      5Mi (0%)
ap-southeast-5.192.168.1.124      aes-gotools-78f9cbc68c-j6528             250m (6%)      0Mi (0%)      0m (0%)    512Mi (3%)        0Mi (0%)        5Mi (0%)
ap-southeast-5.192.168.1.124      nginx-deployment-basic-776544c85-8dp7h   1000m (25%)    1000m (25%)   0m (0%)    512Mi (3%)        512Mi (3%)      1Mi (0%)
                                                                                                                                                     
virtual-kubelet-ap-southeast-5a   *                                        0Mi (0%)       0Mi (0%)      0m (0%)    0Mi (0%)          0Mi (0%)        18Mi (0%)
virtual-kubelet-ap-southeast-5a   nginx1                                   0Mi (0%)       0Mi (0%)      0m (0%)    0Mi (0%)          0Mi (0%)        18Mi (0%)
```

It's worth noting that utilization numbers from pods will likely not add up to the total node utilization numbers. Unlike request and limit numbers where node and cluster level numbers represent a sum of pod values, node metrics come directly from metrics-server and will likely include other forms of resource utilization.

### Sorting
To highlight the nodes, pods, and containers with the highest metrics, you can sort by a variety of columns:

```
# kubectl mytop --util --sort cpu.util
NODE                              CPU REQUESTS   CPU LIMITS     CPU UTIL     MEMORY REQUESTS   MEMORY LIMITS   MEMORY UTIL
*                                 5344m (0%)     12044m (0%)    694m (0%)    6310Mi (0%)       18112Mi (0%)    14096Mi (0%)
ap-southeast-5.192.168.0.144      2462m (61%)    7322m (183%)   566m (14%)   2888Mi (20%)      10392Mi (72%)   8511Mi (59%)
ap-southeast-5.192.168.1.124      2382m (59%)    4722m (118%)   128m (3%)    2398Mi (16%)      7720Mi (53%)    5566Mi (38%)
virtual-kubelet-ap-southeast-5a   500m (0%)      0Mi (0%)       0m (0%)      1024Mi (0%)       0Mi (0%)        19Mi (0%)
```

### Displaying Pod Count
To display the pod count of each node and the whole cluster, you can pass **--pod-count** argument:
```shell
# kubectl mytop --pod-count
NODE                              CPU REQUESTS   CPU LIMITS     MEMORY REQUESTS   MEMORY LIMITS   POD COUNT
*                                 5344m (0%)     12044m (0%)    6310Mi (0%)       18112Mi (0%)    34/34646
ap-southeast-5.192.168.0.144      2462m (61%)    7322m (183%)   2888Mi (20%)      10392Mi (72%)   17/2323
ap-southeast-5.192.168.1.124      2382m (59%)    4722m (118%)   2398Mi (16%)      7720Mi (53%)    14/2323
virtual-kubelet-ap-southeast-5a   500m (0%)      0Mi (0%)       1024Mi (0%)       0Mi (0%)        3/30000
```

### Filtering By Labels
For more advanced usage, kubectl-mytop also supports filtering by pod, namespace, and/or node labels. The following examples show how to use these filters:

```
kubectl mytop --pod-labels app=nginx
kubectl mytop --namespace default
kubectl mytop --namespace-labels team=api
kubectl mytop --node-labels kubernetes.io/role=node
```

### JSON and YAML Output
By default, kubectl-mytop will provide output in a table format. To view this data in JSON or YAML format, the output flag can be used. Here are some sample commands:
```
kubectl mytop --pods --output json
kubectl mytop --pods --containers --util --output yaml
```

## Flags Supported
```
  -c, --containers                includes containers in output
      --context string            context to use for Kubernetes config
  -h, --help                      help for kubectl-mytop
  -n, --namespace string          only include pods from this namespace
      --namespace-labels string   labels to filter namespaces with
      --node-labels string        labels to filter nodes with
  -o, --output string             output format for information
                                    (supports: [table json yaml])
                                    (default "table")
  -a, --available                 includes quantity available instead of percentage used
  -l, --pod-labels string         labels to filter pods with
  -p, --pods                      includes pods in output
      --sort string               attribute to sort results be (supports:
                                    [cpu.util cpu.request cpu.limit mem.util mem.request mem.limit name])
                                    (default "name")
  -u, --util                      includes resource utilization in output
      --pod-count                 includes pod counts for each of the nodes and the whole cluster
```

## Prerequisites
Any commands requesting cluster utilization are dependent on [metrics-server](https://github.com/kubernetes-incubator/metrics-server) running on your cluster. If it's not already installed, you can install it with the official [helm chart](https://github.com/helm/charts/tree/master/stable/metrics-server).

## Similar Projects
There are already some great projects out there that have similar goals.

- [kube-resource-report](https://github.com/hjacobs/kube-resource-report): generates HTML/CSS report for resource requests and limits across multiple clusters.
- [kubetop](https://github.com/LeastAuthority/kubetop): a CLI similar to top for Kubernetes, focused on resource utilization (not requests and limits).
- [kube-capacity)](https://github.com/robscott/kube-capacity) : a CLI similar to top for Kubernetes ,It attempts to combine the best parts of the output from kubectl top and kubectl describe into an easy to use CLI focused on cluster resources.

## License
Apache License 2.0
