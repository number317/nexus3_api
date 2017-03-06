# 使用Nexus3作为Docker私有库

我们将创建以下仓库用于构建Docker私有库：
* 一个docker(hosted)类型的仓库用于存放我们自己的镜像 
* 一个docker(proxy)类型的仓库用于指向Docker Hub
* 一个docker(group)类型的仓库用于提供一个单独的URL来访问以上的仓库用以下载镜像

-----
## private repo

按照以下步骤创建一个名为docker-private的blob Store：

1. 创建一个名为blobStore-dockerPrivate.json的文件，内容如下：

    ```
    {
        "name": "blobStore-dockerPrivate",
        "type": "groovy",
        "content": "blobStore.createFileBlobStore('docker-private', '/nexus-data/blobs/docker-private')"
    }
    ```

2. 上传blobStore-dockerPrivate.json文件：
    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @../script/json/blobStore-dockerPrivate.json
    ```

3. 运行blobStore-dockerPrivate：

    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/blobStore-dockerPrivate/run"
    ```
    ```
    {
      "name" : "blobStore-dockerPrivate",
      "result" : "BlobStoreConfiguration{name='blobStore-dockerPrivate', type='File', attributes={file={path=blobStore-dockerPrivate}}}"
    }
    ```

按照以下的步骤创建一个private repo：

1. 创建一个名为docker-private.json的文件，内容如下：

    ```
    {
        "name": "docker-private",
        "type": "groovy",
        "content": "import org.sonatype.nexus.repository.storage.WritePolicy; repository.createDockerHosted('docker-private', 8083, null, 'docker-private', true, true, WritePolicy.ALLOW)"
    }
    ```

2. 上传docker-private.json：

    ```
    curl -H "Content-Type: application/json" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @docker-private.json
    ```
    
3. 运行docker-private：
    ```
    curl -X POST -H "Content-Type: text/plain" "http://localhost:8081/service/siesta/rest/v1/script/docker-private/run"
    ```
    ```
    {
      "name" : "docker-private",
      "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=docker, name='docker-private'}"
    }
    ```

-----
## proxy repo

按照以下步骤创建一个名为docker-hub的blob Store：

1. 创建一个名为blobStore-dockerHub.json的文件，内容如下：
    ```
    {
        "name": "blobStore-dockerHub",
        "type": "groovy",
        "content": "blobStore.createFileBlobStore('docker-hub', '/nexus-data/blobs/docker-hub')"
    }
    ```

2. 上传blobStore-dockerHub.json：
    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @blobStore-dockerHub.json
    ```

3. 运行blobStore-dockerHub：
    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/blobStore-dockerHub/run"
    ```
    ```
    {
      "name" : "blobStore-dockerHub",
      "result" : "BlobStoreConfiguration{name='docker-hub', type='File', attributes={file={path=docker-hub}}}"
    }
    ```

按照如下步骤创建一个proxy repo：

1. 创建一个名为docker-hub.json的文件，内容如下：
    ```
    {
        "name": "docker-hub",
        "type": "groovy",
        "content": "repository.createDockerProxy('docker-hub', 'https://registry-1.docker.io', 'HUB', null, null, null, 'docker-hub', true, true)"
    }
    ```

2. 上传docker-hub.json：
    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @docker-hub.json
    ```

3. 运行docker-hub：
    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/docker-hub/run"
    ```
    ```
    {
      "name" : "docker-hub",
      "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=docker, name='docker-hub'}"
    }
    ```

-----
## group repo
按照以下步骤创建一个名为docker-group的blob Store：

1. 创建一个名为blobStore-dockerGroup.json的文件，内容如下：
    ```
    {
        "name": "blobStore-dockerGroup",
        "type": "groovy",
        "content": "blobStore.createFileBlobStore('docker-group', '/nexus-data/blobs/docker-group')"
    }
    ```

2. 上传blobStore-dockerGroup.json：
    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @blobStore-dockerGroup.json
    ```

3. 运行blobStore-dockerGroup：
    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/blobStore-dockerGroup/run
    ```
    ```
    {
      "name" : "blobStore-dockerGroup",
      "result" : "BlobStoreConfiguration{name='docker-group', type='File', attributes={file={path=docker-group}}}"
    }
    ```

按照以下的步骤创建一个group repo：

1. 创建一个名为docker-group.josn的文件，内容如下：
    ```
    {
        "name": "docker-group",
        "type": "groovy",
        "content": "repository.createDockerGroup('docker-group', 8082, null, ['docker-hub', 'docker-private'], true, 'docker-group')"
    }
    ```

2. 上传docker-group.json：
    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @docker-group.json
    ```

3. 运行docker-group：
    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/docker-group/run"
    ```
    ```
    {
      "name" : "docker-group",
      "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=group, format=docker, name='docker-group'}"
    }
    ```

其实搭建docker-group仓库是可选的，因为我们可以直接pull和push镜像到proxy和hosted仓库。

-----
## 配置本地docker以使用Nexus私有仓库
修改`/usr/lib/systemd/system/docker.service`，在`ExecStart`该行末尾加上如下内容：
```
--insecure-registry registry.com:8082
```
重启docker：
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```
向存储库验证你的机器：
```
sudo docker login -u admin -p admin123 localhost:8082
sudo docker login -u admin -p admin123 localhost:8083
```
以上两条命令会生成一个文件~/.docker/config.json，内容如下：
```
{
        "auths": {
                "localhost:8082": {
                        "auth": "YWRtaW46YWRtaW4xMjM="
                },
                "localhost:8083": {
                        "auth": "YWRtaW46YWRtaW4xMjM="
                }
        }
}
```

本地上传镜像：
```
sudo docker tag swift localhost:8083/swift
sudo docker push localhost:8083/swift
```

拉取本地上传的镜像：
```
sudo docker pull localhost:8083/swift
```

拉取Docker Hub的镜像：
```
sudo docker pull localhost:8082/swift
```
如果hosted仓库中不存在，则会从Docker Hub中拉取。
