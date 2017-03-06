# Nexus镜像使用

```
sudo docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus sonatype/nexus3
```

Nexus3的主界面可以通过8081端口访问，默认的用户名和密码是admin/admin123。或者你也可以把Nexus3的数据文件夹映射到本地，只需添加`-v /opt/nexus-data:/nexus-data`

-----
# 使用Nexus3作为Maven私有库

我们将创建以下仓库用于构建Maven私有库：
* 一个maven2(hosted)类型的仓库用于存放快照
* 一个maven2(hosted)类型的仓库用于存放发行版
* 一个maven2(proxy)类型的仓库指向Maven Central
* 一个maven2(group)类型的仓库用于提供一个单独的URL来访问以上所有仓库

建议对于每一个仓库都创建一个新的blob store来存储，这样可以使每个仓库存放的数据都在/nexus-data下不同的文件夹，但这不是强制要求的。

-----
## snapshots repo

该仓库用于存放你配置的在pom.xml文件中以-SNAPSHOT作为版本标签结尾的maven artifacts：
```
<version>1.0.0-SNAPSHOT<version>
```

按照以下的步骤创建一个名为maven-snapshots的blob store：

1. 创建一个名为blobStore-snapshots.json的文件，内容如下：
    ```
    {
        "name": "blobStore-snapshots.json",
        "type": "groovy",
        "content": "blobStore.createFileBlobStore('maven-snapshots', '/nexus-data/blobs/maven-snapshots')"
    }
    ```
 
2. 上传blobStore-snapshots.json文件：

    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @blobStore-snapshots.json
    ```

3. 运行blobStore-snapshots:
    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/blobStore-snapshots/run"
    ```

    ```
    {
      "name" : "blobStore-snapshots",
      "result" : "BlobStoreConfiguration{name='maven-snapshots', type='File', attributes={file={path=maven-snapshots}}}"
    }
    ```

按照以下的步骤创建一个snapshots repo：

1. 创建一个名为maven-snapshots.json的文件，内容如下：

    ```
    {
        "name": "maven-snapshots",
        "type": "groovy",
        "content": "import org.sonatype.nexus.repository.maven.VersionPolicy; import org.sonatype.nexus.repository.storage.WritePolicy; import org.sonatype.nexus.repository.maven.LayoutPolicy; repository.createMavenHosted('maven-snapshots', 'maven-snapshots', true, VersionPolicy.SNAPSHOT, WritePolicy.ALLOW, LayoutPolicy.STRICT)"
    }
    ```

2. 上传maven-snapshots.json文件：

    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @maven-snapshots.json
    ```

3. 运行maven-snapshots：

    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/maven-snapshots/run"
    ```

    ```
    {
      "name" : "maven-snapshots",
      "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=maven2, name='maven-snapshots'}"
    }
    ```

-----
## releases repo

该仓库用于存放你配置的在pom.xml文件中不以-SNAPSHOT作为版本标签结尾的maven artifacts

按照以下的步骤创建一个名为maven-releases的blob store：

1. 创建一个名为blobStore-releases.json的文件，内容如下：
```
{
    "name": "blobStore-releases",
    "type": "groovy",
    "content": "blobStore.createFileBlobStore('maven-releases', '/nexus-data/blobs/maven-releases')"
}
```

2. 上传blobStore-releases：

    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @blobStore-releases.json
    ```

3. 运行blobStore-releases：

    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/blobStore-releases/run"
    ```

    ```
    {
      "name" : "blobStore-releases",
      "result" : "BlobStoreConfiguration{name='maven-releases', type='File', attributes={file={path=maven-releases}}}"
    }
    ```

按照以下步骤创建一个releases repo：

1. 创建一个名为maven-releases.json的文件，内容如下：

    ```
    {
        "name": "maven-releases",
        "type": "groovy",
        "content": "import org.sonatype.nexus.repository.maven.VersionPolicy; import org.sonatype.nexus.repository.storage.WritePolicy; import org.sonatype.nexus.repository.maven.LayoutPolicy; repository.createMavenHosted('maven-releases', 'maven-releases', true, VersionPolicy.RELEASE, WritePolicy.ALLOW_ONCE, LayoutPolicy.STRICT)"
    }
    ```

2. 上传maven-releases.json

    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @maven-releases.json
    ```

3. 运行maven-releases：

    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/maven-releases/run"
    ```

    ```
    {
      "name" : "maven-releases",
      "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=hosted, format=maven2, name='maven-releases'}"
    }
    ```

-----
## proxy to Maven Central repo

该仓库用于代理你从Maven Central下载的所有内容，第二次下载相同的倚赖时，它将会被缓存到你的Nexus中。

按照以下步骤创建一个名为maven-proxy的blob Store：

1. 创建一个名为blobStore-central.json的文件，内容如下：

    ```
    {
        "name": "blobStore-central",
        "type": "groovy",
        "content": "blobStore.createFileBlobStore('maven-central', '/nexus-data/blobs/maven-central')"
    }
    ```

2. 上传blobStore-central.json：
    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @blobStore-central.json
    ```

3. 运行blobStore-central：

    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/blobStore-central/run"
    ```

    {
      "name" : "blobStore-central",
      "result" : "BlobStoreConfiguration{name='maven-central', type='File', attributes={file={path=maven-central}}}"
    }
    ```

按照以下步骤创建一个proxy repo：

1. 创建一个名为maven-central.json的文件，内容如下：

    ```
    {
        "name": "maven-central",
        "type": "groovy",
        "content": "import org.sonatype.nexus.repository.maven.VersionPolicy; import org.sonatype.nexus.repository.maven.LayoutPolicy; repository.createMavenProxy('maven-central', 'https://repo1.maven.org/maven2/', 'maven-central', true, VersionPolicy.RELEASE, LayoutPolicy.STRICT)"
    }
    ```

2. 上传maven-central.json文件：
    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @maven-central.json
    ```

3. 运行maven-central：

    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/maven-central/run"
    ```

    ```
    {
      "name" : "maven-central",
      "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=proxy, format=maven2, name='maven-central'}"
    }
    ```

-----
## group repo

该仓库会把以上的仓库汇整为一组，并提供一个单一的URL供你的客户端上传和下载。

按照以下的步骤创建一个名为maven-group的blob store：

1. 创建一个名为blobStore-group.json的文件，内容如下：

    ```
    {
        "name": "blobStore-group",
        "type": "groovy",
        "content": "blobStore.createFileBlobStore('maven-group', '/nexus-data/blobs/maven-group')"
    }
    ```

2. 上传blobStore-group.json：

    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @blobStore-group.json
    ```

3. 运行blobStore-group：

    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/blobStore-group/run"
    ```
    ```
    {
      "name" : "blobStore-group",
      "result" : "BlobStoreConfiguration{name='maven-group', type='File', attributes={file={path=maven-group}}}"
    }
    ```

按照以下步骤创建一个group repo：

1. 创建一个名为maven-group.json的文件，内容如下：

    ```
    {
        "name": "maven-group",
        "type": "groovy",
        "content": "repository.createMavenGroup('maven-group', ['maven-central', 'maven-releases', 'maven-snapshots'], 'maven-group')"
    }
    ```

2. 上传maven-group：

    ```
    curl -H "Content-Type: application/json" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" 'http://localhost:8081/service/siesta/rest/v1/script/' -d @maven-group.json
    ```

3. 运行maven-group：
    ```
    curl -X POST -H "Content-Type: text/plain" -H "Authorization: Basic YWRtaW46YWRtaW4xMjM=" "http://localhost:8081/service/siesta/rest/v1/script/maven-group/run"
    ```

    ```
    {
      "name" : "maven-group",
      "result" : "RepositoryImpl$$EnhancerByGuice$$c5f0822b{type=group, format=maven2, name='maven-group'}"
    }
    ```

-----
## 配置本地maven使用私有库

在~/.m2文件夹下创建一个名为settings.xml的配置文件，内容如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.1.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd">

  <servers>
    <server>
      <id>nexus-snapshots</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
    <server>
      <id>nexus-releases</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
  </servers>

  <mirrors>
    <mirror>
      <id>central</id>
      <name>central</name>
      <url>http://localhost:8081/repository/maven-group/</url>
      <mirrorOf>*</mirrorOf>
    </mirror>
  </mirrors>
</settings>
```

这个文件将配置发布到你私有库的凭据，并且告诉你的mvn使用你的私有库作为中央镜像仓库。
现在配置你的项目：

* 如果你只想要从Nexus下载倚赖，则将以下内容加入pom.xml：
    ```
    <project ...>
        ...
    
        <repositories>
            <repository>
                <id>maven-group</id>
                <url>http://your-host:8081/repository/maven-group/</url>
            </repository>
        </repositories>
    </project>
    ```
* 如果你还想要发布你的项目，添加：
    ```
    <project ...>
        ...
    
        <distributionManagement>
            <snapshotRepository>
                <id>nexus-snapshots</id>
                <url>http://your-host:8081/repository/maven-snapshots/</url>
            </snapshotRepository>
            <repository>
                <id>nexus-releases</id>
                <url>http://your-host:8081/repository/maven-releases/</url>
            </repository>
        </distributionManagement>
    </project>
    ```

现在如果你在项目中运行
```
mvn install
```
或者
```
mvn deploy
```
你的mvn将会指向你的Nexus实例。
