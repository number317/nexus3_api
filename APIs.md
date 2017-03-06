# REST and Integration API

## 脚本管理
### 上传
/service/siesta/rest/v1/script/
上传指定名字的脚本到仓库管理器
请求：POST
```
curl -X POST -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "maven", "type": "groovy", "content": "repository.createMavenHosted(\"private\")" }'
```

响应：成功时没有返回结果

-----
### 查看
/service/siesta/rest/v1/script
查看在仓库管理器中的脚本

请求：GET
```
curl -X GET "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/'
```

响应：
```
[ {
  "name" : "maven",
  "content" : "repository.createMavenHosted(\"private\")",
  "type" : "groovy"
} ]
```

-----
### 运行
service/siesta/rest/v1/script/$scriptName/run
运行在仓库中指定名字的脚本

请求：POST
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/maven/run'
```

响应：
```
{
  "name" : "maven",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=maven2, name='private'}"
}
```

脚本可以通过REST API接受运行时参数：
先上传updateAnonymousAccess.json文件：
```
curl -X POST -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "updateAnonymousAccess", "type": "groovy", "content": "security.setAnonymousAccess(Boolean.valueOf(args))" }'
```
上传成功没有返回结果

查看上传的脚本：
```
curl -X GET "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/'
```
```
[ {
  "name" : "updateAnonymousAccess",
  "content" : "security.setAnonymousAccess(Boolean.valueOf(args))",
  "type" : "groovy"
}, {
  "name" : "maven",
  "content" : "repository.createMavenHosted(\"private\")",
  "type" : "groovy"
} ]
```

运行updateAnonymousAccess.json：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/updateAnonymousAccess/run' -d "true"
```
```
{
  "name" : "updateAnonymousAccess",
  "result" : "AnonymousConfiguration{enabled=true, userId='anonymous', realmName='NexusAuthorizingRealm'}"
}
```

-----
### 更新
/service/siesta/rest/v1/script/maven
更新仓库中指定脚本的内容

请求：PUT
```
curl -X PUT -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/maven' -d '{ "name": "maven", "type": "groovy", "content": "repository.createMavenHosted(\"private-again\")" }'
```

响应：更新成功没有返回内容

### 删除
/service/siesta/rest/v1/script/$scriptName
删除仓库中指定的脚本

请求：DELETE
```
curl -X DELETE -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/maven"
```

响应：删除成功没有返回数据

## core API
`core.baseUrl()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String url | 设置仓库管理器的基准url |

baseUrl.json:
```
{
    "name": "baseUrl",
    "type": "groovy",
    "content": "core.baseUrl('http://repo.example.com')"
}
```

请求：
```
curl -X POST -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "baseUrl", "type": "groovy", "content": "core.baseUrl(\"http://repo.example.com\")" }'
```

响应：上传成功无返回值

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/baseUrl/run"
```

响应：
```
{
  "name" : "baseUrl",
  "result" : "null"
}
```

`core.connectionRetryAttempts()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | int retries | 设置重新连接的次数，在1-10之间 |

cnnectionRetryAttempts.json:
```
{
    "name": "connectionRetryAttempts",
    "type": "groovy",
    "content": "core.connectionRetryAttempts(5)"
}
```

请求：
```
curl -X POST -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "connectionRetryAttempts", "type": "groovy", "content": "core.connectionRetryAttempts(5)" }'

```

响应：上传成功无返回内容

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/connectionRetryAttempts/run"
```

响应：
```
{
  "name" : "connectionRetryAttempts",
  "result" : "null"
}
```

`core.connectionTimeout()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | int timeout | 设置连接超时的时间，在1-3600秒之间 |

connectionTimeout.json:
```
{ 
    "name": "connectionRetryAttempts",
    "type": "groovy", 
    "content": "core.connectionTimeout(10)"
}
```

请求：
```
curl -X POST -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "connectionTimeout", "type": "groovy", "content": "core.connectionTimeout(10)" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/connectionTimeout/run"
```

响应：
```
{
  "name" : "connectionTimeout",
  "result" : "null"
}
```

`core.httpProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :---| :--- |
| void | String host, int port | 设置代理的host，设置代理的port，该方法用于设置一个未经验证的http代理 |

`core.httpProxyWithBasicAuth()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String host, int port, String username, String password| 设置代理的host ，设置代理的port，设置代理的username，设置代理的password，该方法用于设置一个以username/password验证的http代理 |

`core.httpProxyWithNTLMAuth()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String host, int port, String username, String password, String ntlmHost, String domain | 设置代理的host，设置代理的port ，设置代理的username ，设置代理的password ，设置ntlmHost ，设置ntlm的domain，创建一个用Windows NTLM验证的http代理 |


`core.httpsProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String host, int port | 设置代理的host，设置代理的port，该方法用于设置一个未经验证的https代理 |

`core.httpsProxyWithBasicAuth()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String host, int port, String username, String password | 设置代理的host ，设置代理的port ，设置代理的username ，设置代理的password，该方法用于设置一个以username/password验证的https代理 |

`core.httpsProxyWithNTLMAuth()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String host, int port, String username, String password, String ntlmHost, String domain | 设置代理的host ，设置代理的port ，设置代理的username ，设置代理的password ，设置ntlmHost ，设置ntlm的domain，创建一个用Windows NTLM验证的https代理 | 

`core.nonProxyHosts()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String... nonProxyHosts | 设置不需要被代理的主机 |

`core.removeBaseUrl()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | null | 删除任何现有的基准url |

`core.removeHTTPProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | null | 删除任何已存在的http代理配置 |

`core.removeHTTPSProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | | 删除任何已存在的https代理配置 |

`core.userAgentCustomization()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String userAgentSuffix | 通过追加此值来定制传出HTTP请求中的User-Agent头 |

## blobStore API

`blobStore.createFileBlobStore()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| BlobStoreConfiguration | String name, String path | 设置blob的名字，设置blob的路径，该方法用于创建一个新的文件类型的blob存储 |

createFileBlobStore.json:
```
{
    "name": "createFileBlobStore",
    "type": "groovy",
    "content": "createFileBlobStore('docker-private', '/nexus-data/blobs/docker-private')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "createFileBlobStore", "type": "groovy", "content": "blobStore.createFileBlobStore(\"docker-private\", \"/nexus-data/blobs/docker-private\")" }'
```

请求：
```
curl -v -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/createFileBlobStore/run" 2>/dev/null
```

响应：
```
{
  "name" : "createFileBlobStore",
  "result" : "BlobStoreConfiguration{name='docker-private', type='File', attributes={file={path=docker-private}}}"
}
```

`blobStore.getBlobStoreManager().delete()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String name | 设置blobStore的名字，该方法用于按名字删除blobStore |

deleteBlobStore.json:
```
{
    "name": "deleteBlobStore",
    "type": "groovy",
    "content": "blobStore.getBlobStoreManager().delete('testDelete')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "deleteBlobStore", "type": "groovy", "content": "blobStore.getBlobStoreManager().delete(\"testDelete\")" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/deleteBlobStore/run"
```

响应：
```
{
  "name" : "deleteBlobStore",
  "result" : "null"
}
```

`blobStore.getBlobStoreManager().get().getBlobStoreConfiguration()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| blobStoreConfiguration | null | 获取blobStore的信息 |

getBlobStore.json:
```
{
    "name": "getBlobStore",
    "type": "groovy",
    "content": "blobStore.getBlobStoreManager().get('docker-hub').getBlobStoreConfiguration()"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "getBlobStore", "type": "groovy", "content": "blobStore.getBlobStoreManager().get(\"docker-hub\").getBlobStoreConfiguration()" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/getBlobStore/run"
```

响应：
```
{
  "name" : "getBlobStore",
  "result" : "BlobStoreConfiguration{name='docker-hub', type='File', attributes={file={path=docker-hub}}}"
}
```

## repository API

`repository.createDockerHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort | 设置仓库的名字，设置http端口或https端口，该方法用于创建一个hosted类型的Docker仓库 |

`repository.createDockerHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort, String blobStoreName | 设置仓库的名字，设置http端口或https端口，设置blobStore的名字，该方法用于创建一个hosted类型的Docker仓库 |

`repository.createDockerHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort, String blobStoreName, boolean strictContentTypeValidation | 设置仓库的名字，设置http端口或https端口，设置blobStore的名字，设置是否进行严格内容验证，该方法用于创建一个hosted类型的Docker仓库 |

`repository.createDockerHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort, String blobStoreName, boolean strictContentTypeValidation, boolean v1Enabled | 设置仓库的名字，设置http端口或https端口，设置blobStore的名字，设置是否进行严格内容验证，设置是否启用Docker Registry API V1，该方法用于创建一个hosted类型的Docker仓库 |

`repository.createDockerHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort, String blobStoreName, boolean strictContentTypeValidation, boolean v1Enabled, WritePolicy writePolicy | 设置仓库的名字，设置http端口或https端口，设置blobStore的名字，设置是否进行严格内容验证，设置是否启用Docker Registry API V1，设置写策略（WritePolicy.ALLOW, WritePolicy.ALLOW\_ONCE, WritePolicy.DENY），该方法用于创建一个hosted类型的Docker仓库 |

createDockerHosted.json
```
{
    "name": "createDockerHosted",
    "type": "groovy",
    "content": "import org.sonatype.nexus.repository.storage.WritePolicy; repository.createDockerHosted('docker-private', 8082, null, 'docker-private', true, true, WritePolicy.ALLOW_ONCE)"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "createDockerHosted", "type": "groovy", "content": "import org.sonatype.nexus.repository.storage.WritePolicy; repository.createDockerHosted(\"docker-private\", 8082, null, \"docker-private\", true, true, WritePolicy.ALLOW)" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/createDockerHosted/run"
```

响应：
```
{
  "name" : "createDockerHosted",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=docker, name='docker-private'}"
}
```

`repository.createDockerProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String indexType, @Nullable String indexUrl, @Nullable Integer httpPort, @Nullable Integer httpsPort | 设置仓库的名字，设置远程url，设置索引类型，设置可选的索引url，设置http端口或https端口，该方法用于创建一个proxy类型的Docker仓库 |

`repository.createDockerProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String indexType, @Nullable String indexUrl, @Nullable Integer httpPort, @Nullable Integer httpsPort, String blobStoreName | 设置仓库的名字，设置远程url，设置索引类型，设置可选的索引url，设置http端口或https端口，设置blobStore的名字，该方法用于创建一个proxy类型的Docker仓库 |

`repository.createDockerProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String indexType, @Nullable String indexUrl, @Nullable Integer httpPort, @Nullable Integer httpsPort, String blobStoreName, boolean strictContentTypeValidation | 设置仓库的名字，设置远程url，设置索引类型，设置可选的索引url，设置http端口或https端口，设置blobStore的名字，设置是否进行严格内容验证，该方法用于创建一个proxy类型的Docker仓库 |

`repository.createDockerProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String indexType, @Nullable String indexUrl, @Nullable Integer httpPort, @Nullable Integer httpsPort, String blobStoreName, boolean strictContentTypeValidation, boolean v1Enabled | 设置仓库的名字，设置远程url，设置索引类型（CUSTOM，HUB，REGISTRY），设置可选的索引url，设置http端口或https端口，设置blobStore的名字，设置是否进行严格内容验证，设置是否启用Docker Registry API V1，方法用于创建一个proxy类型的Docker仓库 |

createDockerProxy.json
```
{
    "name": "createDockerProxy",
    "type": "groovy",
    "content": "repository.createDockerProxy('docker-hub', 'https://registry-1.docker.io', 'HUB', null, null, null, 'docker-hub', true, true)"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "createDockerProxy", "type": "groovy", "content": "repository.createDockerProxy(\"docker-hub\", \"https://registry-1.docker.io\", \"HUB\", null, null, null, \"docker-hub\", true, true)" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/createDockerProxy/run"
```

响应：
```
{
  "name" : "createDockerProxy",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=docker, name='docker-hub'}"
}
```

`repository.createDockerGroup()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort, List<String> members | 设置仓库的名字，设置可选的http或https端口，设置组员，该方法用于创建一个group类型的Docker仓库 |

`repository.createDockerGroup()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort, List<String> members, boolean v1Enabled | 设置仓库的名字，设置可选的http或https端口，设置组员，设置是否启用Docker Register API V1，该方法用于创建一个group类型的Docker仓库 |

`repository.createDockerGroup()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, @Nullable Integer httpPort, @Nullable Integer httpsPort, List<String> members, boolean v1Enabled, String blobStoreName | 设置仓库的名字，设置可选的http或https端口，设置组员，设置是否启用Docker Register API V1，设置blobStore的名字，该方法用于创建一个group类型的Docker仓库 |

createDockerGroup.json:
```
{
    "name": "createDockerGroup",
    "type": "groovy",
    "content": "repository.createDockerGroup('docker-group', 8083, null, ['docker-private', 'docker-hub'], true, 'docker-group')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "createDockerGroup", "type": "groovy", "content": "repository.createDockerGroup(\"docker-group\", 8083, null, [\"docker-private\", \"docker-hub\"], true, \"docker-group\")" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/createDockerGroup/run"
```

响应：
```
{
  "name" : "createDockerGroup",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=group, format=docker, name='docker-group'}"
}
```

`repository.createMavenHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name | 设置仓库的名字，该方法用于创建一个group类型的Docker仓库 |

`repository.createMavenHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String blobStoreName | 设置仓库的名字，设置blobStore的名字，该方法用于创建一个group类型的Docker仓库 |

`repository.createMavenHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String blobStoreName, boolean strictContentTypeValidation | 设置仓库的名字，设置blobStore的名字，设置是否进行严格内容验证，该方法用于创建一个group类型的Docker仓库 |

`repository.createMavenHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String blobStoreName, boolean strictContentTypeValidation, VersionPolicy versionPolicy | 设置仓库的名字，设置blobStore的名字，设置是否进行严格内容验证，设置版本策略（VersionPolicy.RELEASE，VersionPolicy.SNAPSHOT，VersionPolicy.MIXED），该方法用于创建一个group类型的Docker仓库 |

`repository.createMavenHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String blobStoreName, boolean strictContentTypeValidation, VersionPolicy versionPolicy, WritePolicy writePolicy | 设置仓库的名字，设置blobStore的名字，设置是否进行严格内容验证，设置版本策略（VersionPolicy.RELEASE，VersionPolicy.SNAPSHOT，VersionPolicy.MIXED），设置写策略（WritePolicy.ALLOW，WritePolicy.ALLOW\_ONCE，WritePolicy.DENY），该方法用于创建一个group类型的Docker仓库 |

`repository.createMavenHosted()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String blobStoreName, boolean strictContentTypeValidation, VersionPolicy versionPolicy, WritePolicy writePolicy, LayoutPolicy layoutPolicy | 设置仓库的名字，设置blobStore的名字，设置是否进行严格内容验证，设置版本策略（VersionPolicy.RELEASE，VersionPolicy.SNAPSHOT，VersionPolicy.MIXED），设置写策略（WritePolicy.ALLOW，WritePolicy.ALLOW\_ONCE，WritePolicy.DENY），设置布局策略（LayoutPolicy.STRICT，LayoutPolicy.PERMISSIVE），该方法用于创建一个group类型的Maven仓库 |

createMavenHosted.json
```
{
    "name": "createMavenHosted",
    "type": "groovy",
    "content": "import org.sonatype.nexus.repository.maven.VersionPolicy; import org.sonatype.nexus.repository.storage.WritePolicy; import org.sonatype.nexus.repository.maven.LayoutPolicy; repository.createMavenHosted('maven-releases', 'maven-releases', true, VersionPolicy.RELEASE, WritePolicy.ALLOW, LayoutPolicy.STRICT)"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "createMavenHosted", "type": "groovy", "content": "import org.sonatype.nexus.repository.maven.VersionPolicy; import org.sonatype.nexus.repository.storage.WritePolicy; import org.sonatype.nexus.repository.maven.LayoutPolicy; repository.createMavenHosted(\"maven-releases\", \"maven-releases\", true, VersionPolicy.RELEASE, WritePolicy.ALLOW, LayoutPolicy.STRICT)" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/createMavenHosted/run"
```

响应：
```
{
  "name" : "createMavenHosted",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=maven2, name='maven-releases'}"
}
```

`repository.createMavenProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl | 设置仓库的名字，设置远程url，该方法用于创建一个proxy类型的Maven仓库 |

`repository.createMavenProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String blobStoreName | 设置仓库的名字，设置远程url，设置blobStore的名字，该方法用于创建一个proxy类型的Maven仓库 |

`repository.createMavenProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String blobStoreName, boolean strictContentTypeValidation | 设置仓库的名字，设置远程url，设置blobStore的名字，设置是否开启严格内容验证，该方法用于创建一个proxy类型的Maven仓库 |

`repository.createMavenProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String blobStoreName, boolean strictContentTypeValidation, VersionPolicy versionPolicy | 设置仓库的名字，设置远程url，设置blobStore的名字，设置是否开启严格内容验证，设置版本策略（VersionPolicy.RELEASE，VersionPolicy.SNAPSHOT，VersionPolicy.MIXED），该方法用于创建一个proxy类型的Maven仓库 |

`repository.createMavenProxy()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, String remoteUrl, String blobStoreName, boolean strictContentTypeValidation, VersionPolicy versionPolicy, LayoutPolicy layoutPolicy | 设置仓库的名字，设置远程url，设置blobStore的名字，设置是否开启严格内容验证，设置版本策略（VersionPolicy.RELEASE，VersionPolicy.SNAPSHOT，VersionPolicy.MIXED），设置布局策略（LayoutPolicy.STRICT，LayoutPolicy.PERMISSIVE），该方法用于创建一个proxy类型的Maven仓库 |

createMavenProxy.json:
```
{
    "name": "createMavenProxy",
    "type": "groovy",
    "content": "import org.sonatype.nexus.repository.maven.VersionPolicy; import org.sonatype.nexus.repository.maven.LayoutPolicy; repository.createMavenProxy('maven-central', 'https://repo1.maven.org/maven2/', 'maven-central', true, VersionPolicy.RELEASE, LayoutPolicy.STRICT)"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "createMavenProxy", "type": "groovy", "content": "import org.sonatype.nexus.repository.maven.VersionPolicy; import org.sonatype.nexus.repository.maven.LayoutPolicy; repository.createMavenProxy(\"maven-central\", \"https://repo1.maven.org/maven2/\", \"maven-central\", true, VersionPolicy.RELEASE, LayoutPolicy.STRICT)" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/createMavenProxy/run"
```

响应：
```
{
  "name" : "createMavenProxy",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=maven2, name='maven-central'}"
}
```

`repository.createMavenGroup()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, List<String> members | 设置仓库的名字，设置组员，该方法用于创建一个group类型的Maven仓库 |

`repository.createMavenGroup()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name, List<String> members, String blobStoreName | 设置仓库的名字，设置组员，设置blobStore的名字，该方法用于创建一个group类型的Maven仓库 |

createMavenGroup.json:
```
{
    "name": "createMavenGroup",
    "type": "groovy",
    "content": "repository.createMavenGroup('maven-group', ['maven-releases', 'maven-central'], 'maven-group')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "createMavenGroup", "type": "groovy", "content": "repository.createMavenGroup(\"maven-group\", [\"maven-releases\", \"maven-central\"], \"maven-group\")" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/createMavenGroup/run"
```

响应：
```
{
  "name" : "createMavenGroup",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=group, format=maven2, name='maven-group'}"
}
```

`repository.getRepositoryManager().delete()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String name | 选择仓库的名字，该方法用于删除指定仓库 |

deleteRepository.json:
```
{
    "name": "deleteRepository",
    "type": "groovy",
    "content": "repository.getRepositoryManager().delete('bower-private')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "deleteRepository", "type": "groovy", "content": "repository.getRepositoryManager().delete(\"bower-private\")" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/deleteRepository/run"
```

响应：
```
{
  "name" : "deleteRepository",
  "result" : "null"
}
```

`repository.getRepositoryManager().get()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Repository | String name | 选择仓库名称，该方法用于获取仓库的详细信息 |

getRepository.json:
```
{
    "name": "getRepository",
    "type": "groovy",
    "content": "repository.getRepositoryManager().get('docker-hub')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "getRepository", "type": "groovy", "content": "repository.getRepositoryManager().get(\"docker-hub\")" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/getRepository/run"
```

响应：
```
{
  "name" : "getRepository",
  "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=docker, name='docker-hub'}"
}
```

`repository.getRepositoryManager().browse()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Iterable<Repository> | null | 获取所有的仓库信息 |

browseRepository.json:
```
{
    "name": "browseRepository",
    "type": "groovy",
    "content": "repository.getRepositoryManager().browse()"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "browseRepository", "type": "groovy", "content": "repository.getRepositoryManager().browse()" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/browseRepository/run"
```

响应：
```
{
  "name" : "browseRepository",
  "result" : "[RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=group, format=nuget, name='nuget-group'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=docker, name='docker-private'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=maven2, name='maven-central'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=group, format=maven2, name='maven-group'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=nuget, name='nuget.org-proxy'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=group, format=docker, name='docker-group'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=maven2, name='maven-releases'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=nuget, name='nuget-hosted'}, RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=docker, name='docker-hub'}]"
}
```

## security API

`security.addRole()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Role | String id, String name, String description, List<String> privileges, List<String> roles | 设置角色id，设置角色名字，添加描述，添加权限，添加包含的角色，该方法用于添加角色 |

addRole.json
```
{
    "name": "addRole",
    "type": "groovy",
    "content": "security.addRole('test', 'test', 'test role', ['nx-all'], [])"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{    "name": "addRole", "type": "groovy", "content": "security.addRole(\"test\", \"test\", \"test role\", [\"nx-all\"], [])" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/addRole/run"
```

响应：
```
{
  "name" : "addRole",
  "result" : "Role{roleId='test', name='test', source='default'}"
}
```

`security.addUser()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| User | String id, String firstName, String lastName, String email, boolean active, String password, List<String> roleIds | 设置用户id，填写姓，填写名，填写邮箱，设置是否活动，设置密码，授予角色权限，该方法用于添加用户 |

addUser.json:
```
{
    "name": "addUser",
    "type": "groovy",
    "content": "security.addUser('test', 'cheon', 'cron', 'cong.chen01@hand-china.com', true, '011235', ['test'])"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "adduser", "type": "groovy", "content": "security.adduser(\"test\", \"august\", \"rush\", \"test@test-example.com\", true, \"011235\", ['test'])" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/addUser/run"
```

响应：
```
{
  "name" : "addUser",
  "result" : "User{userId='test', firstName='cheon', lastName='cron', source='default'}"
}
```

`security.setUserRoles()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| User | String userId, List<String> roleIds | 选择用户id，选择用户角色，该方法用于设置用户角色 |

setUserRoles.json:
```
{
    "name": "setUserRoles",
    "type": "groovy",
    "content": "security.setUserRoles('test', ['nx-admin'])"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "setUserRoles", "type": "groovy", "content": "security.setUserRoles(\"test\", [\"nx-admin\"])"'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/setUserRoles/run"
```

响应：
```
{
  "name" : "setUserRoles",
  "result" : "User{userId='test', firstName='cheon', lastName='cron', source='default'}"
}
```

`security.getSecuritySystem().currentUser()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| User | null | 获取当前的用户 |

currentUser.json:
```
{
    "name": "currentUser",
    "type": "groovy",
    "content": "security.getSecuritySystem().currentUser()"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "currentUser", "type": "groovy", "content": "security.getSecuritySystem().currentUser()" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/currentUser/run"
```

响应：
```
{
  "name" : "currentUser",
  "result" : "User{userId='admin', firstName='Administrator', lastName='User', source='default'}"
}
```

`security.getSecuritySystem().deleteUser()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String userId | 确定userId，该方法用于删除指定用户 |

deleteUser.json
```
{
    "name": "deleteUser",
    "type": "groovy",
    "content": "security.getSecuritySystem().deleteUser('test')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "deleteUser", "type": "groovy", "content": "security.getSecuritySystem().deleteUser(\"test\")" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/deleteUser/run"
```

响应：
```
{
  "name" : "deleteUser",
  "result" : "null"
}
```

`security.getSecuritySystem().listRoles()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Set<Role> | null |列出所有角色 |

listRoles.json:
```
{
    "name": "listRoles",
    "type": "groovy",
    "content": "security.getSecuritySystem().listRoles()"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "listRoles", "type": "groovy", "content": "security.getSecuritySystem().listRoles()" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/listRoles/run"
```

响应：
```
{
  "name" : "listRoles",
  "result" : "[Role{roleId='nx-anonymous', name='null', source='default'}, Role{roleId='nx-admin', name='null', source='default'}, Role{roleId='test', name='test', source='default'}]"
}
```

`security.getSecuritySystem().listUsers()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| Set<User> | null | 列出所有用户 |

listUsers.json:
```
{
    "name": "listUsers",
    "type": "groovy",
    "content": "security.getSecuritySystem().listUsers()"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "listUsers", "type": "groovy", "content": "security.getSecuritySystem().listUsers()" }'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/listUsers/run"
```

响应：
```
{
  "name" : "listUsers",
  "result" : "[User{userId='anonymous', firstName='Anonymous', lastName='User', source='default'}, User{userId='admin', firstName='Administrator', lastName='User', source='default'}]"
}
```

`security.getSecuritySystem().getUser()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| User | String | 设置用户id，获取用户信息 |

```
{
    "name": "getUser",
    "type": "groovy",
    "content": "security.getSecuritySystem().getUser('test')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "getUser", "type": "groovy", "content": "security.getSecuritySystem().getUser(\"test\")"}'
```

请求：
```
curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/getUser/run"
```

响应：
```
{
  "name" : "getUser",
  "result" : "User{userId='test', firstName='August', lastName='Rush', source='default'}"
}
```

`security.getSecuritySystem().changePassword()`

| 返回值 | 参数 | 描述 |
| :--- | :--- | :--- |
| void | String userId, String newPassword | 设置userId，设置新密码，该方法用于修改用户密码 |

changePassword.json:
```
{
    "name": "changePassword",
    "type": "groovy",
    "content": "security.getSecuritySystem().changePassword('test', '53212765')"
}
```

请求：
```
curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d '{ "name": "changePassword", "type": "groovy", "content": "security.getSecuritySystem().changePassword(\"test\", \"53212765\")" }'
```

请求：
```
curl -X POST -H  "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/changePassword/run"
```

响应：
```
{
  "name" : "changePassword",
  "result" : "null"
}
```
