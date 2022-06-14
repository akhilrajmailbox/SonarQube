# SonarQube Server

## Prerequisites

* We are using `devops-network` docker network for all devops deployment.
* You need at least Docker Engine 17.06.0  and docker-compose 1.18 for this to work.

```bash
docker --version
docker-compose --version
```

## Create custom Docker network before deploying container (If the network is already created then ignore it)

*Note : Make sure that the subnet won't conflict with any other subnets*

```bash
docker network create --driver=bridge --subnet=172.31.0.0/16 devops-network
```

## Deploy SonarQube as Docker container

```bash
docker-compose -f docker-compose.yml up -d
```


## Working with SonarQube


* Login to SonarQube server

```bash
docker exec -it sonarqube /bin/bash
```

* Restarting SonarQube

```bash
docker-compose -f docker-compose.yml restart
```

* stopping/starting SonarQube

```
docker-compose -f docker-compose.yml stop/start
```

* check the logs of SonarQube server

```bash
docker logs -f sonarqube
```

## Update Admin Password and do the following configuration

* default username and password : `admin\admin`

* update the `Administrator` page with required configruations

* Create another user for jenkins and create the api key for the same


## LDAP configuration

*Even Ldap configured, local user still can autheticate*

### Configure the sonar properties for Ldap authetication

update these entries in `$SONARQUBE-HOME/conf/sonar.properties` and restart the service with `docker-compose.yml` file

# General Configuration

```code
sonar.security.realm=LDAP
ldap.url=ldap://ldap-server
ldap.bindDn=cn=Query Admin,ou=DevOps,ou=People,dc=my,dc=devops,dc=example,dc=com
ldap.bindPassword=SecurePassword
```

# User Configuration

```code
ldap.user.baseDn=ou=People,dc=my,dc=devops,dc=example,dc=com
ldap.user.request=(&(objectClass=posixAccount)(uid={login}))
ldap.user.realNameAttribute=cn
ldap.user.emailAttribute=mail
```

# Group Configuration

```code
ldap.group.baseDn=ou=Groups,dc=my,dc=devops,dc=example,dc=com
ldap.group.request=(&(objectClass=posixGroup)(memberUid={uid}))
```

### Restart sonarqube

```bash
docker-compose -f docker-compose.yml restart
```

### Ldap Group in Sonarqube

* Login as local admin in sonar ui

* Always use local user and its api key for jenkins or scanner integration and grant specific permission for that user `Administration > Security > Users`

* Add the Admin group name of Ldap in sonar `Administration > Security > Groups` and give `Administrator` permission

* Add the Developer group name of Ldap in sonar `Administration > Security > Groups` and give Readonly / required permission


[ldap sonar configuration](https://docs.sonarqube.org/8.9/instance-administration/delegated-auth/)

[ldap group configuration](https://docs.sonarqube.org/8.9/instance-administration/security/)