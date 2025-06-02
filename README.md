# Local Maven cache for building OpenNMS

This repository is used to setup a local [Reposilite](https://reposilite.com/) instance to cache Maven artifacts which are downloaded during a build from public Maven repositories.
A local cache for Maven artifacts can have several benefits:
* Remove intermediate dependencies from public remote repositories
* Speedup downloads for artifacts in geographics other than North America or when you have low network connectivity bandwidth

## 👮 Requirements

* Docker with the Docker Compose plugin
* Optional: TLS reverse proxy if is exposed to the public internet
* Data is stored as a bind mount in `./data`, change if you want a different path for persisting the data. 

## 👩‍🏭 Installation and Usage

**Step 1**: Checkout the repository and switch working directory

```shell
git clone https://github.com/opennms-forge/maven-cache
cd maven-cache
```

**Step 2**: Set a secure initial admin password

```shell
cp env.sample .env
vi .env
```
Set a secure init admin password and save the file:

```shell
REPOSILITE_COMPOSE_OPTS=--token admin:<change-me>
```

**Step 3**: Start the service with

```shell
docker-compose up -d
```

## 👩‍🔧 Install a Systemd service unit

```shell
cp maven-cdn.service /etc/systemd/system
systemctl daemon-reload
systemctl enable --now maven-cdn
```

## 🕹️ Use cache with Maven

Create a `settings.xml` file for Maven with the following content:

```xml
<settings>
    <mirrors>
        <mirror>
            <id>{My-Maven-ID}</id>
            <name>{My-Name-For-This-Service}</name>
            <url>{My-Maven-http(s)://my-host-or-FQDN}/upstream</url>
            <mirrorOf>*</mirrorOf>
        </mirror>
    </mirrors>
</settings>
```
You can use it globally for Maven if you create this file in `~/.m2/settings.xml` or specifically by using `mvn --settings /path/to/my/settings.xml`.

If you want to use the public OpenNMS Maven caching instance create the `~/.m2/settings.xml` with the following content:

```xml
<settings>
    <mirrors>
        <mirror>
            <id>mvn.cdn.useast1.opennms.com-cache</id>
            <name>🇺🇸 Maven CDN in US East 1</name>
            <url>https://mvn.cdn.useast1.opennms.com/cache</url>
            <mirrorOf>*</mirrorOf>
        </mirror>
    </mirrors>
</settings>
```
