### 关于Maven

Maven是Java项目的管理和构建工具，它主要提供**标准化的**：

- 项目结构
- 构建流程（编译，测试，打包，发布等）
- 依赖管理机制

---

#### Maven项目结构

Maven管理的普通Java项目**默认结构**如下

- pom.xml
- src/
    - main/
        - java/
        - resources/
    - test/
        - java/
        - resources/
- target/

---

#### Maven依赖管理

Maven维护了一个**中央仓库 repo1.maven.org**，所有第三方库将自身的jar以及相关信息上传至中央仓库，Maven就可以从中央仓库把所需依赖下载到本地用户主目录的`.m2`目录。除了可以从中央仓库下载外，还可以从**
镜像仓库**下载，镜像仓库定期从中央仓库同步，例如

```xml

<settings>
    <mirrors>
        <mirror>
            <id>aliyun</id>
            <name>aliyun</name>
            <mirrorOf>central</mirrorOf>
            <url>https://maven.aliyun.com/nexus/content/groups/public/</url>
        </mirror>
    </mirrors>
</settings>
```

国内常用的Maven镜像仓库

- `https://maven.aliyun.com/nexus/content/groups/public/`
- `https://mirrors.cloud.tencent.com/nexus/repository/maven-public/`
- `https://mirrors.huaweicloud.com/repository/maven/`
- `https://mirrors.163.com/maven/repository/maven-public/`

Maven依赖由`groupId`，`artifactId`和`version`作为唯一标识，例如

```xml

<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.2</version>
    <scope>provided</scope>
</dependency>
```

Maven通过**PGP签名**确保任何一个jar包一经发布就无法修改，某个jar包一旦被Maven下载过，即可永久地安全缓存在本地

> 以`-SNAPSHOT`结尾的版本号会被Maven视为开发版本，每次都会重复下载

Maven定义了四种`scope`

| Scope | 说明 |
| :--- | :--- |
| compile | (默认) 编译时需要用到该jar包，Maven会把这种类型的依赖直接放入classpath |
| test | 仅在测试时使用，正常运行时并不需要，例如JUnit |
| runtime | 编译时不需要，但运行时需要，例如JDBC驱动 |
| provided | 编译时需要，但运行时不需要，例如Servlet相关jar包 |

---

#### Maven构建流程

Maven的**生命周期**（lifecycle）由一系列**阶段**（phase）构成，Maven内置定义了以下三种lifecycle

**default**

```xml
<phases>
    <phase>validate</phase>
    <phase>initialize</phase>
    <phase>generate-sources</phase>
    <phase>process-sources</phase>
    <phase>generate-resources</phase>
    <phase>process-resources</phase>
    <phase>compile</phase>
    <phase>process-classes</phase>
    <phase>generate-test-sources</phase>
    <phase>process-test-sources</phase>
    <phase>generate-test-resources</phase>
    <phase>process-test-resources</phase>
    <phase>test-compile</phase>
    <phase>process-test-classes</phase>
    <phase>test</phase>
    <phase>prepare-package</phase>
    <phase>package</phase>
    <phase>pre-integration-test</phase>
    <phase>integration-test</phase>
    <phase>post-integration-test</phase>
    <phase>verify</phase>
    <phase>install</phase>
    <phase>deploy</phase>
</phases>
```

**clean**

```xml
<xxx>
    <phases>
        <phase>pre-clean</phase>
        <phase>clean</phase>
        <phase>post-clean</phase>
    </phases>
    <default-phases>
        <clean>
            org.apache.maven.plugins:maven-clean-plugin:2.5:clean
        </clean>
    </default-phases>
</xxx>
```

**site**

```xml
<xxx>
    <phases>
        <phase>pre-site</phase>
        <phase>site</phase>
        <phase>post-site</phase>
        <phase>site-deploy</phase>
    </phases>
    <default-phases>
        <site>
            org.apache.maven.plugins:maven-site-plugin:3.3:site
        </site>
        <site-deploy>
            org.apache.maven.plugins:maven-site-plugin:3.3:deploy
        </site-deploy>
    </default-phases>
</xxx>
```

Maven通过为`phase`绑定插件`plugin`来完成实际的构建过程，例如以下为`jar` packaging的plugin配置

```xml
<phases>
  <process-resources>
    org.apache.maven.plugins:maven-resources-plugin:2.6:resources
  </process-resources>
  <compile>
    org.apache.maven.plugins:maven-compiler-plugin:3.1:compile
  </compile>
  <process-test-resources>
    org.apache.maven.plugins:maven-resources-plugin:2.6:testResources
  </process-test-resources>
  <test-compile>
    org.apache.maven.plugins:maven-compiler-plugin:3.1:testCompile
  </test-compile>
  <test>
    org.apache.maven.plugins:maven-surefire-plugin:2.12.4:test
  </test>
  <package>
    org.apache.maven.plugins:maven-jar-plugin:2.4:jar
  </package>
  <install>
    org.apache.maven.plugins:maven-install-plugin:2.4:install
  </install>
  <deploy>
    org.apache.maven.plugins:maven-deploy-plugin:2.7:deploy
  </deploy>
</phases>
```



[看完了，回到目录](/README.md)