# kubectl-mytop

这是一个方便查看集群资源的命令行工具，可以综合 `kubectl top` 和 `kubectl describe` 展示，如指定node后，可以查看这个node上所有的pod的request，limit，util

## 安装
```
wget https://raw.githubusercontent.com/mYu4N/kubectl-mytop/master/kubectl-mytop -O /usr/local/bin/kubectl-mytop 

chmod u+x /usr/local/bin/kubectl-mytop 
```
## 使用
默认情况下，kubectl mytop将输出一个节点列表，其中包含CPU和内存资源的总请求以及在其上运行的所有pod的限制（类似describe node的资源输出）。对于具有多个节点的集群，第一行还将包括集群范围内的总计（sum）。如下所示：

```
% kubectl mytop                         
NODE                       | CPU REQUESTS  | CPU LIMITS     | MEMORY REQUESTS  | MEMORY LIMITS
*                          | 17790m (56%)  | 92600m (294%)  | 20271Mi (37%)    | 130905Mi (240%)
cn-beijing.192.168.0.17    | 2475m (65%)   | 16000m (421%)  | 3995Mi (61%)     | 25354Mi (391%)
cn-beijing.192.168.0.246   | 2855m (71%)   | 11100m (277%)  | 3307Mi (51%)     | 13532Mi (208%)
cn-beijing.192.168.66.175  | 2755m (68%)   | 22600m (565%)  | 2822Mi (43%)     | 38243Mi (590%)
cn-beijing.192.168.88.154  | 3055m (39%)   | 6600m (84%)    | 1951Mi (13%)     | 7936Mi (55%)
cn-beijing.192.168.88.156  | 4615m (59%)   | 24100m (308%)  | 5586Mi (39%)     | 32399Mi (227%)
cn-beijing.192.168.88.243  | 2035m (50%)   | 12200m (305%)  | 2611Mi (40%)     | 13442Mi (207%)
```

### 展示pod资源情况
可以使用`-p` 或者 `--pods` 参数, 会展示pod级别的request limit，当节点或者pod比较多的时候，输出较多阅读有所不便，如下所示:

```
% kubectl mytop  --pods -n default
NODE                       | POD                                             | CPU REQUESTS  | CPU LIMITS   | MEMORY REQUESTS  | MEMORY LIMITS
*                          | *                                               | 6000m (19%)   | 6500m (20%)  | 8192Mi (15%)     | 6144Mi (11%)
                           |                                                 |               |              |                  | 
cn-beijing.192.168.0.17    | *                                               | 750m (19%)    | 1000m (26%)  | 1536Mi (23%)     | 1024Mi (15%)
cn-beijing.192.168.0.17    | golang-7cdc688b6-2mbrq                          | 250m (6%)     | 0Mi (0%)     | 512Mi (7%)       | 0Mi (0%)
cn-beijing.192.168.0.17    | my-gotools-5bc6dfcd75-j4tmk                     | 250m (6%)     | 0Mi (0%)     | 512Mi (7%)       | 0Mi (0%)
cn-beijing.192.168.0.17    | my-nettools-primary-8584bffdf5-9lwh6            | 250m (6%)     | 1000m (26%)  | 512Mi (7%)       | 1024Mi (15%)
                           |                                                 |               |              |                  | 
cn-beijing.192.168.0.246   | *                                               | 1000m (25%)   | 1500m (37%)  | 1536Mi (23%)     | 2560Mi (39%)
cn-beijing.192.168.0.246   | java-php-perf-arthas-84b5799f67-7hb8t           | 500m (12%)    | 1000m (25%)  | 1024Mi (15%)     | 2048Mi (31%)
cn-beijing.192.168.0.246   | multi-containers-6695d87b75-2vkf4               | 500m (12%)    | 500m (12%)   | 512Mi (7%)       | 512Mi (7%)
                           |                                                 |               |              |                  | 
cn-beijing.192.168.66.175  | *                                               | 250m (6%)     | 1000m (25%)  | 512Mi (7%)       | 1024Mi (15%)
cn-beijing.192.168.66.175  | my-nettools-multicluster-test-75497d67fb-fmskd  | 250m (6%)     | 1000m (25%)  | 512Mi (7%)       | 1024Mi (15%)
                           |                                                 |               |              |                  | 
cn-beijing.192.168.88.154  | *                                               | 2250m (28%)   | 2000m (25%)  | 1024Mi (7%)      | 512Mi (3%)
cn-beijing.192.168.88.154  | aestools-79999865cc-w2rsd                       | 250m (3%)     | 0Mi (0%)     | 512Mi (3%)       | 0Mi (0%)
cn-beijing.192.168.88.154  | cpu-static-5d88d58877-j9jvl                     | 2000m (25%)   | 2000m (25%)  | 512Mi (3%)       | 512Mi (3%)
                           |                                                 |               |              |                  | 
cn-beijing.192.168.88.156  | *                                               | 1500m (19%)   | 1000m (12%)  | 3072Mi (21%)     | 1024Mi (7%)
cn-beijing.192.168.88.156  | aestools-onlyperf-6f779888f7-pqtpr              | 250m (3%)     | 0Mi (0%)     | 512Mi (3%)       | 0Mi (0%)
cn-beijing.192.168.88.156  | alpine-74c4d47df8-rdpq4                         | 250m (3%)     | 0Mi (0%)     | 512Mi (3%)       | 0Mi (0%)
cn-beijing.192.168.88.156  | hello-pod                                       | 0Mi (0%)      | 0Mi (0%)     | 0Mi (0%)         | 0Mi (0%)
cn-beijing.192.168.88.156  | my-nettools-primary-8584bffdf5-b65fr            | 250m (3%)     | 1000m (12%)  | 512Mi (3%)       | 1024Mi (7%)
cn-beijing.192.168.88.156  | my-wordpress-85d86fb874-s9x54                   | 250m (3%)     | 0Mi (0%)     | 512Mi (3%)       | 0Mi (0%)
cn-beijing.192.168.88.156  | php-perf-5487b88c4-ssk2q                        | 250m (3%)     | 0Mi (0%)     | 512Mi (3%)       | 0Mi (0%)
cn-beijing.192.168.88.156  | yibei-echoserver-dc9f9b99c-c8z6r                | 250m (3%)     | 0Mi (0%)     | 512Mi (3%)       | 0Mi (0%)
                           |                                                 |               |              |                  | 
cn-beijing.192.168.88.243  | *                                               | 250m (6%)     | 0Mi (0%)     | 512Mi (7%)       | 0Mi (0%)
cn-beijing.192.168.88.243  | jen-7ffb5fd6fc-d5qcw                            | 250m (6%)     | 0Mi (0%)     | 512Mi (7%)       | 0Mi (0%)
```

### 支持显示利用率
使用参数 `-u` 或者 `--util` 支持显示节点级别的利用率，类似top node的功能, 该功能走的是metrics的指标，如下所示:

```
 % kubectl mytop  --util
NODE                       | CPU REQUESTS  | CPU LIMITS     | CPU UTIL     | MEMORY REQUESTS  | MEMORY LIMITS    | MEMORY UTIL
*                          | 17790m (56%)  | 92600m (294%)  | 3051m (9%)   | 20271Mi (37%)    | 130905Mi (240%)  | 32066Mi (58%)
cn-beijing.192.168.0.17    | 2475m (65%)   | 16000m (421%)  | 390m (10%)   | 3995Mi (61%)     | 25354Mi (391%)   | 4211Mi (64%)
cn-beijing.192.168.0.246   | 2855m (71%)   | 11100m (277%)  | 516m (12%)   | 3307Mi (51%)     | 13532Mi (208%)   | 4375Mi (67%)
cn-beijing.192.168.66.175  | 2755m (68%)   | 22600m (565%)  | 211m (5%)    | 2822Mi (43%)     | 38243Mi (590%)   | 4132Mi (63%)
cn-beijing.192.168.88.154  | 3055m (39%)   | 6600m (84%)    | 207m (2%)    | 1951Mi (13%)     | 7936Mi (55%)     | 5480Mi (38%)
cn-beijing.192.168.88.156  | 4615m (59%)   | 24100m (308%)  | 1379m (17%)  | 5586Mi (39%)     | 32399Mi (227%)   | 8790Mi (61%)
cn-beijing.192.168.88.243  | 2035m (50%)   | 12200m (305%)  | 352m (8%)    | 2611Mi (40%)     | 13442Mi (207%)   | 5082Mi (78%)
```

### 支持先是可用资源
使用  --available 可以查看节点可用资源，有vk节点是可能会不太准确

```
 % kubectl mytop  --available
NODE                       | CPU REQUESTS   | CPU LIMITS      | MEMORY REQUESTS  | MEMORY LIMITS
*                          | 13610m/31400m  | -61200m/31400m  | 34098Mi/54369Mi  | -76536Mi/54369Mi
cn-beijing.192.168.0.17    | 1325m/3800m    | -12200m/3800m   | 2488Mi/6483Mi    | -18871Mi/6483Mi
cn-beijing.192.168.0.246   | 1145m/4000m    | -7100m/4000m    | 3176Mi/6483Mi    | -7049Mi/6483Mi
cn-beijing.192.168.66.175  | 1245m/4000m    | -18600m/4000m   | 3650Mi/6472Mi    | -31771Mi/6472Mi
cn-beijing.192.168.88.154  | 4745m/7800m    | 1200m/7800m     | 12280Mi/14231Mi  | 6295Mi/14231Mi
cn-beijing.192.168.88.156  | 3185m/7800m    | -16300m/7800m   | 8645Mi/14231Mi   | -18168Mi/14231Mi
cn-beijing.192.168.88.243  | 1965m/4000m    | -8200m/4000m    | 3861Mi/6472Mi    | -6970Mi/6472Mi
```

### 支持显示pod的使用率
使用参数 `--util` 和 `--pods` 时, 可以显示pod级别的util，类似top pod:

```
 % kubectl mytop --pods --util -n default
NODE                       | POD                                             | CPU REQUESTS  | CPU LIMITS   | CPU UTIL    | MEMORY REQUESTS  | MEMORY LIMITS  | MEMORY UTIL
*                          | *                                               | 6000m (19%)   | 6500m (20%)  | 0Mi (0%)    | 8192Mi (15%)     | 6144Mi (11%)   | 0Mi (0%)
                           |                                                 |               |              |             |                  |                | 
cn-beijing.192.168.0.17    | *                                               | 750m (19%)    | 1000m (26%)  | 1m (0%)     | 1536Mi (23%)     | 1024Mi (15%)   | 26Mi (0%)
cn-beijing.192.168.0.17    | golang-7cdc688b6-2mbrq                          | 250m (6%)     | 0Mi (0%)     | 0m (0%)     | 512Mi (7%)       | 0Mi (0%)       | 3Mi (0%)
cn-beijing.192.168.0.17    | my-gotools-5bc6dfcd75-j4tmk                     | 250m (6%)     | 0Mi (0%)     | 0m (0%)     | 512Mi (7%)       | 0Mi (0%)       | 3Mi (0%)
cn-beijing.192.168.0.17    | my-nettools-primary-8584bffdf5-9lwh6            | 250m (6%)     | 1000m (26%)  | 1m (0%)     | 512Mi (7%)       | 1024Mi (15%)   | 21Mi (0%)
                           |                                                 |               |              |             |                  |                | 
......省略一万字
                           |                                                 |               |              |             |                  |                | 
cn-beijing.192.168.88.243  | *                                               | 250m (6%)     | 0Mi (0%)     | 1m (0%)     | 512Mi (7%)       | 0Mi (0%)       | 300Mi (4%)
cn-beijing.192.168.88.243  | jen-7ffb5fd6fc-d5qcw                            | 250m (6%)     | 0Mi (0%)     | 1m (0%)     | 512Mi (7%)       | 0Mi (0%)       | 300Mi (4%)
```
PS：节点跟pod的展示有所不同，节点是pod的sum值，过小的值也会导致数据失真

### 排序
可以指定不同的指标做排序，如下为节点 cpu的util的排序（总和值可以不看）:

```
% kubectl mytop --util --sort cpu.util
NODE                       | CPU REQUESTS  | CPU LIMITS     | CPU UTIL     | MEMORY REQUESTS  | MEMORY LIMITS    | MEMORY UTIL
*                          | 17790m (56%)  | 92600m (294%)  | 5825m (18%)  | 20271Mi (37%)    | 130905Mi (240%)  | 32097Mi (59%)
cn-beijing.192.168.66.175  | 2755m (68%)   | 22600m (565%)  | 2852m (71%)  | 2822Mi (43%)     | 38243Mi (590%)   | 4134Mi (63%)
cn-beijing.192.168.88.156  | 4615m (59%)   | 24100m (308%)  | 1429m (18%)  | 5586Mi (39%)     | 32399Mi (227%)   | 8791Mi (61%)
cn-beijing.192.168.0.246   | 2855m (71%)   | 11100m (277%)  | 510m (12%)   | 3307Mi (51%)     | 13532Mi (208%)   | 4374Mi (67%)
cn-beijing.192.168.88.243  | 2035m (50%)   | 12200m (305%)  | 476m (11%)   | 2611Mi (40%)     | 13442Mi (207%)   | 5109Mi (78%)
cn-beijing.192.168.0.17    | 2475m (65%)   | 16000m (421%)  | 347m (9%)    | 3995Mi (61%)     | 25354Mi (391%)   | 4215Mi (65%)
cn-beijing.192.168.88.154  | 3055m (39%)   | 6600m (84%)    | 213m (2%)    | 1951Mi (13%)     | 7936Mi (55%)     | 5476Mi (38%)
```

### 支持显示pod数量
使用参数 `--pod-count` 可以显示节点的pod数量及可用数量，总可用数可不看，这块取不到节点规格的数量限制
```shell
% kubectl mytop --pod-count
NODE                       | CPU REQUESTS  | CPU LIMITS     | MEMORY REQUESTS  | MEMORY LIMITS    | POD COUNT
*                          | 17790m (56%)  | 92600m (294%)  | 20271Mi (37%)    | 130905Mi (240%)  | 138/213
cn-beijing.192.168.0.17    | 2475m (65%)   | 16000m (421%)  | 3995Mi (61%)     | 25354Mi (391%)   | 24/23
cn-beijing.192.168.0.246   | 2855m (71%)   | 11100m (277%)  | 3307Mi (51%)     | 13532Mi (208%)   | 16/23
cn-beijing.192.168.66.175  | 2755m (68%)   | 22600m (565%)  | 2822Mi (43%)     | 38243Mi (590%)   | 19/23
cn-beijing.192.168.88.154  | 3055m (39%)   | 6600m (84%)    | 1951Mi (13%)     | 7936Mi (55%)     | 12/48
cn-beijing.192.168.88.156  | 4615m (59%)   | 24100m (308%)  | 5586Mi (39%)     | 32399Mi (227%)   | 40/48
cn-beijing.192.168.88.243  | 2035m (50%)   | 12200m (305%)  | 2611Mi (40%)     | 13442Mi (207%)   | 27/48
```

### 支持使用label过滤想要的资源
mytop还支持pod，namespace，node的标签来过滤，如下所示:

```
kubectl mytop --pod-labels app=nginx
kubectl mytop --namespace default
kubectl mytop --namespace-labels app=ingress
kubectl mytop --node-labels kubernetes.io/role=node
%  kubectl mytop -p -u  --node-labels kubernetes.io/hostname=cn-beijing.192.168.88.154
NODE                       | NAMESPACE    | POD                                                     | CPU REQUESTS  | CPU LIMITS   | CPU UTIL   | MEMORY REQUESTS  | MEMORY LIMITS  | MEMORY UTIL
                           |              |                                                         |               |              |            |                  |                | 
cn-beijing.192.168.88.154  | *            | *                                                       | 3055m (39%)   | 6600m (84%)  | 226m (2%)  | 1951Mi (13%)     | 7936Mi (55%)   | 5600Mi (39%)
cn-beijing.192.168.88.154  | kube-system  | ack-node-problem-detector-daemonset-nwvxd               | 100m (1%)     | 1000m (12%)  | 59m (0%)   | 200Mi (1%)       | 1024Mi (7%)    | 41Mi (0%)
cn-beijing.192.168.88.154  | monitoring   | ack-prometheus-operator-prometheus-node-exporter-fhzrx  | 0Mi (0%)      | 0Mi (0%)     | 2m (0%)    | 0Mi (0%)         | 0Mi (0%)       | 25Mi (0%)
cn-beijing.192.168.88.154  | default      | aestools-79999865cc-w2rsd                               | 250m (3%)     | 0Mi (0%)     | 0m (0%)    | 512Mi (3%)       | 0Mi (0%)       | 2Mi (0%)
cn-beijing.192.168.88.154  | kube-system  | appcenter-7c7bbb5f76-d7dxf                              | 0Mi (0%)      | 0Mi (0%)     | 1m (0%)    | 0Mi (0%)         | 0Mi (0%)       | 14Mi (0%)
cn-beijing.192.168.88.154  | default      | cpu-static-5d88d58877-j9jvl                             | 2000m (25%)   | 2000m (25%)  | 0m (0%)    | 512Mi (3%)       | 512Mi (3%)     | 2Mi (0%)
cn-beijing.192.168.88.154  | kube-system  | csi-plugin-tznkk                                        | 130m (1%)     | 2000m (25%)  | 1m (0%)    | 176Mi (1%)       | 4096Mi (28%)   | 71Mi (0%)
cn-beijing.192.168.88.154  | kube-system  | kube-proxy-worker-qjm5c                                 | 0Mi (0%)      | 0Mi (0%)     | 6m (0%)    | 0Mi (0%)         | 0Mi (0%)       | 51Mi (0%)
cn-beijing.192.168.88.154  | kube-system  | logtail-ds-6t6sp                                        | 100m (1%)     | 500m (6%)    | 5m (0%)    | 256Mi (1%)       | 1024Mi (7%)    | 92Mi (0%)
cn-beijing.192.168.88.154  | kube-system  | nginx-ingress-controller-57cb45dd78-l5gx2               | 100m (1%)     | 0Mi (0%)     | 4m (0%)    | 90Mi (0%)        | 0Mi (0%)       | 309Mi (2%)
cn-beijing.192.168.88.154  | kube-system  | node-local-dns-nnbjc                                    | 25m (0%)      | 0Mi (0%)     | 1m (0%)    | 5Mi (0%)         | 1024Mi (7%)    | 22Mi (0%)
cn-beijing.192.168.88.154  | kube-system  | terway-eniip-m9877                                      | 350m (4%)     | 1100m (14%)  | 2m (0%)    | 200Mi (1%)       | 256Mi (1%)     | 169Mi (1%)
```

### 支持的输出格式
默认情况下mytop以表格形式输出，也支持json或者yaml形式，参数参考:
```
kubectl mytop --pods --output json
kubectl mytop --pods --containers --util --output yaml
```

## 支持的参数
```
  -c, --containers                输出包含containers
      --context string            使用kubernetes config的上下文
  -h, --help                      kubectl-mytop 帮助参数
  -n, --namespace string          指定namespace
      --namespace-labels string   使用namespace的labels做过滤
      --node-labels string        使用node-labels做过滤
  -o, --output string             指定输出格式，默认table，支持以下三种
                                    (支持: [table json yaml])
                                    (默认: "table")
  -a, --available                 includes quantity available instead of percentage used
  -l, --pod-labels string         使用pod labels过滤
  -p, --pods                      展示pod的使用率
      --sort string               指定指标排序支持 (支持:
                                    [cpu.util cpu.request cpu.limit mem.util mem.request mem.limit name])
                                    (默认: "name")
  -u, --util                      展示使用率
      --pod-count                 展示pod数目及可用数（部分场景下不准）
```

## 依赖
所有指标来源依赖 [metrics-server](https://github.com/kubernetes-incubator/metrics-server) ，需要具备metric-server的部署

## 类似项目
以下项目有类似的功能，具体信息可自行打开观看

- [kube-resource-report](https://github.com/hjacobs/kube-resource-report): generates HTML/CSS report for resource requests and limits across multiple clusters.
- [kubetop](https://github.com/LeastAuthority/kubetop): a CLI similar to top for Kubernetes, focused on resource utilization (not requests and limits).
- [kube-capacity)](https://github.com/robscott/kube-capacity) : a CLI similar to top for Kubernetes ,It attempts to combine the best parts of the output from kubectl top and kubectl describe into an easy to use CLI focused on cluster resources.

## 授权许可
Apache License 2.0
