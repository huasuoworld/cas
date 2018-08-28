# apereo cas jdbc support
<h1>1.get cas template</h1>

```
git clone https://github.com/apereo/cas-overlay-template.git
git checkout -b 5.3.2
```
<h2>2.modify application.properties</h2>
apereo github project link: https://github.com/apereo<br>
Reference<br>
https://apereo.github.io/cas/5.3.x/installation/Configuration-Properties.html<br>
https://mkjwk.org/#shared<br>
https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html<br>
#CN domain ，OU organization，O organization name L city ST province C country changeit<br>
keytool -genkeypair -dname "cn=huasuoworld.org, ou=huasuoworld, o=huasuoworld, c=China" \<br>
-ext SAN="dns:huasuoworld,dns:localhost,ip:127.0.0.1" \<br>
-alias cas -keypass changeit -keystore /etc/cas/thekeystore \<br>
-storepass changeit -validity 1800<br>

```

#cas base config
cas.server.name=https://192.168.1.5:8443
cas.server.prefix=https://192.168.1.5:8443/cas
cas.host.name=192.168.1.5
#cas.mgmt.serverName=https://huasuoworld:8443
#cas.mgmt.userPropertiesFile=file:/opt/cas/admusers.properties

#cas tgc config
cas.tgc.secure=true
cas.tgc.crypto.signing.key=C1Zy-7glynd1gtQGecFoaPtD8ng7doOcs1S3dI3NBQMHSMfFQ7rCWEjzcPGNjGr3GOwCxyPzBWJos8AGCRyiwQ
cas.tgc.crypto.encryption.key=yoJoYKZOJbJ93PY3S2U7r1qunH6xLtqAi6OTiw5bBYM

#webflow manager
cas.webflow.crypto.signing.key=edhaSH6n-kyX6PHpvN7h2S03JCQBK38e2DjZ-6lTF_gyTMDLWpTSk2jZqKEXBxfGJgh-GdPtbMnSjsB0NSnaEQ
cas.webflow.crypto.encryption.key=iXorCBB9rq/FmBemehyE6w==

#
cas.adminPagesSecurity.actuatorEndpointsEnabled=true
cas.monitor.endpoints.enabled=true
endpoints.enabled=true
cas.adminPagesSecurity.ip=127\.0\.0\.1
cas.adminPagesSecurity.ip=^192\\.168\\.(50\\.[0-9]{1,3}|1\\.[12]0)$
cas.monitor.endpoints.sensitive=false
endpoints.sensitive=false

cas.adminPagesSecurity.loginUrl=https://192.168.1.5:8443/cas/login
cas.adminPagesSecurity.service=https://192.168.1.5:8443/cas/status/dashboard
cas.adminPagesSecurity.users=file:/opt/cas/admusers.properties

##
# CAS Authentication Credentials
#
#cas.authn.accept.users=casuser::Mellon
cas.jdbc.showSql=true
cas.jdbc.genDdl=true

cas.authn.jdbc.query[0].sql=SELECT * FROM user WHERE user_name = ?
cas.authn.jdbc.query[0].fieldPassword=password
cas.authn.jdbc.query[0].url=jdbc:mysql://192.168.1.5:3306/cas?useUnicode=true&characterEncoding=UTF-8
cas.authn.jdbc.query[0].dialect=org.hibernate.dialect.MySQLDialect
cas.authn.jdbc.query[0].user=root
cas.authn.jdbc.query[0].password=test123456
cas.authn.jdbc.query[0].driverClass=com.mysql.jdbc.Driver
cas.authn.jdbc.query[0].ddlAuto=create-drop

cas.authn.jdbc.query[0].passwordEncoder.type=DEFAULT
cas.authn.jdbc.query[0].passwordEncoder.characterEncoding=UTF-8
cas.authn.jdbc.query[0].passwordEncoder.encodingAlgorithm=MD5
cas.authn.jdbc.query[0].passwordEncoder.strength=16

cas.authn.jdbc.query[0].pool.suspension=false
cas.authn.jdbc.query[0].pool.minSize=6
cas.authn.jdbc.query[0].pool.maxSize=18
cas.authn.jdbc.query[0].pool.maxWait=2000
cas.authn.jdbc.query[0].pool.timeoutMillis=1000
```

<h3>3.modify pom.xml (or gradle)</h3>
add jdbc support

```
<dependency>
    <groupId>org.apereo.cas</groupId>
    <artifactId>cas-server-support-jdbc-drivers</artifactId>
    <version>${cas.version}</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.apereo.cas</groupId>
    <artifactId>cas-server-support-jdbc-authentication</artifactId>
    <version>${cas.version}</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.apereo.cas</groupId>
    <artifactId>cas-server-support-jdbc</artifactId>
    <version>${cas.version}</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.apereo.cas</groupId>
    <artifactId>cas-server-support-generic</artifactId>
    <version>${cas.version}</version>
    <scope>runtime</scope>
</dependency>
```

<h4>add msyql table</h4>

```
--create table--
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `password` varchar(64) COLLATE utf8_unicode_ci DEFAULT NULL,
  `user_name` varchar(64) COLLATE utf8_unicode_ci DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;

--insert into test values (md5 123456)--
INSERT INTO `cas`.`user` (`id`, `password`, `user_name`) VALUES ('1', 'e10adc3949ba59abbe56e057f20f883e', 'test');

```

<h5>5.build package</h5>

```
mvn clean package
```

<h5>run app</h5>

```
java -jar -Dlogging.config=file:/etc/cas/config/log4j2.xml cas.war
```
