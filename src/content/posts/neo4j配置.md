---

title: neo4j配置

published: 2024-10-24

description: 'rt'

image: ''

tags: [tutorial]

category: 'engineering'

draft: false 

---
# 环境配置

启动docker(可选-v /home/hguan/yago/import:/var/lib/neo4j/import   -v /home/hguan/yago/data:/var/lib/neo4j/data ）

```cpp
docker pull neo4j

docker run --name neo4j -d -p 7474:7474 -p 7687:7687 -e NEO4J_AUTH=none neo4j
（可以尝试将docker中的目录进行挂载，但是挂载后目录可能需要sudo权限进行更改）
```

将服务器的端口隧穿到本机

```cpp
ssh -N -f -L localhost:7474:localhost:7474 id@host

ssh -N -f -L localhost:7687:localhost:7687 id@host
```

浏览器访问localhost:7474

# 导入数据

1. 参考[**README.md**](https://gist.github.com/kuzeko/9b5dbdf52102a59b9d3865c70a8576a0)中的download脚本下载分块数据，将下载的数据拷贝到docker内部的import文件夹下docker exec -it neo4j /bin/bash进入docker。
2. 安装[neosemantics](https://github.com/neo4j-labs/neosemantics)插件：先将插件下载到plugins文件夹下，再在neo4j-conf中添加dbms.unmanaged_extension_classes=n10s.endpoint=/rdf，这里我起先使用apoc插件但是没有成功。
    
    wget https://github.com/neo4j-labs/neosemantics/releases/download/5.20.0/neosemantics-5.20.0.jar
    
    CREATE CONSTRAINT n10s_unique_uri FOR (r:Resource) REQUIRE r.uri IS UNIQUE;
    
    call n10s.graphconfig.init();
    
3. 在docker中运行cypher-shell，输入用户名和密码（一般初始用户名密码均为neo4j）后运行CALL n10s.rdf.import.fetch("file:///var/lib/neo4j/import/part-yago-wd-labels-en.nt00", "N-Triples");
4. 等待数据加载，可能需要重启docker
5. 删除数据：

MATCH (n)
DETACH DELETE n;

## 导入csv数据

docker cp

docker stop

docker run --rm -v /home/hguan/dbdemo/dblp/import:/var/lib/neo4j/import -v /home/hguan/dbdemo/dblp/data:/var/lib/neo4j/data neo4j neo4
j-admin database import full --overwrite-destination --nodes=/var/lib/neo4j/import/nodes.csv --relationships=/var/lib/neo4j/import/relationships.csv

docker start