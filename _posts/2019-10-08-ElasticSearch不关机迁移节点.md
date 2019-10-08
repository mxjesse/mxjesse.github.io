## ElasticSearch迁移节点数据

### 1.测试环境
系统：CentOS 7.5  
ES版本：5.3.0  
ES节点：

节点代号 | 节点名称 | 节点端口号
--- | --- | ---
node-1 | Uhmuvzq | 9211
node-2 | RfqEeJD | 9212
node-3 | uCmKKuj | 9213

![测试三个节点分布情况](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570523534104.jpg)

### 2.测试环境初始化
#### 2.1 添加测试索引
![测试索引情况](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570524541415.jpg)

测试索引初始化设置为5个分片，1个副本

#### 2.2 添加测试数据
![查询节点数据](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570525117504.jpg)

查询三个节点数据，都是**71**条如上图  
`http://localhost:9211/test_index/test_type/_search?pretty`  
`http://localhost:9212/test_index/test_type/_search?pretty`  
`http://localhost:9213/test_index/test_type/_search?pretty`

#### 2.3 查看当前各节点信息
![各节点信息](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570524936317.jpg)

可以看到，三个节点的`shards`都有分布，且`disk.indices`都有数据

#### 2.4 查看集群配置信息
![集群配置信息](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570525457989.jpg)

### 3.测试备份移除节点
当前有三个节点，由于从上边索引配置可以看到备份数为1，因此在node-2和node-3上有全量数据，现在计划将node-1移除，测试的结果应该是node-1上的数据被转移到node-2和node-3，node-1中不再保留数据，再关闭node-1节点即可。

#### 3.1 设置node-1节点为exclude节点

执行命令 `curl -XPUT localhost:9211/_cluster/settings -d ‘{"transient" :{"cluster.routing.allocation.exclude._name" :"Uhmuvzq"}}‘`

![执行exclude命令](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570526322713.jpg)

#### 3.2 判断数据迁移结果

`http://localhost:9211/_cat/allocation?v`

![数据迁移结果](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570526509584.jpg)

当shards和disk.indices都为0时表示node-1节点没数据了，数据迁移完成，这时就可以停止node-1节点了。

`http://localhost:9212/_cat/shards?v`

![分片分布情况](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570526671097.jpg)

可以看到没有分片分布在node-1节点上了

#### 3.3 停止node-1节点
此时可以通过linux关闭node-1节点的elasticsearch服务了，数据已经被迁移到node-2和node-3中

![停止node-1节点](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570526823858.jpg)

此时由于node-1的ES实例已经关闭，通过9212端口的node-2节点访问查看集群信息发现已经没有Uhmuvzq节点了，查看数据情况

`http://localhost:9212/test_index/test_type/_search?pretty`

![查询节点数据](https://raw.githubusercontent.com/mxjesse/mxjesse.github.io/master/img_folder/201910/1570526960793.jpg)

查看得数据还是**71**条，分片分布情况，由于只有2个节点，则是一个节点上的数据分片对应另外一个节点的备份分片，各有5个分片

#### 3.4 设置集群配置信息
由于node-1不再使用，则更改集群的配置信息

`curl -XPUT localhost:9212/_cluster/settings -d ‘{"transient" :{"cluster.routing.allocation.exclude._name" :null}}‘`

### 4.小结
至此，已经可以把node-1中的数据完全迁出，如果要替换别的主机，则将新的es实例加入集群中，直接启动新的es实例，es会重新计算分片分布，并转移分片数据至相关节点。