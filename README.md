## Cloudflare Argo Tunnel with Guacamole

Create a Guacamole lab environment on the Internet using `docker-compose` and Cloudflare Argo Tunnel (`cloudflared`) with just a few commands.

## Setup

1. Install Cloudflare Argo Tunnel binary `cloudflared` on your local desktop device [Download link](https://developers.cloudflare.com/argo-tunnel/downloads/)


1. Create `secrets.env` with the following environment variables.

```
MYSQL_ROOT_PASSWORD=
MYSQL_DATABASE=
MYSQL_USER=
MYSQL_PASSWORD=
TUNNEL_HOSTNAME=
```

1. Run `docker-compose up`

1. Login to Guacamode admin console
URL: http://localhost:8080/guacamole/#/
User: `guacadmin`
Password:  `guacadmin`

1. Change the default admin username and password!!!

## Cheatsheet

```
docker run --name my-guacd -d -p 4822:4822 guacamole/guacd
# Docker secret

docker run --name my-mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=P@ssw0rd -e MYSQL_DATABASE=mysql -e MYSQL_USER=user -e MYSQL_PASSWORD=P@ssw0rd -v "$PWD"docker-entrypoint-initdb.d/:/docker-entrypoint-initdb.d/ mysql

docker run --name my-guacamole  --link my-guacd:guacd  --link my-mysql:mysql  -d -p 8080:8080 guacamole/guacamole
docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --mysql > initdb.sql
docker logs my-guacamo
docker exec -it mysql bash
docker volume ls
#docker-compose


# Troubleshoot mySQL
mysql -h 127.0.0.1 -u root -p 
mysql --host=127.0.0.1 --port=3306 --user=xxxx --password=xxxx
mysql> show databases;
mysql> use guacamole;
```

## Troubleshooting
### Issue: Changes to mysql environement variables do not take effect 

```
docker-compose rm -v 
```

## Issue: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure

```
guacamole    | 06:58:01.089 [http-nio-8080-exec-5] WARN  o.a.g.e.AuthenticationProviderFacade - The "mysql" authentication provider has encountered an internal error which will halt the authentication process. If this is unexpected or you are the developer of this authentication provider, you may wish to enable debug-level logging. If this is expected and you wish to ignore such failures in the future, please set "skip-if-unavailable: mysql" within your guacamole.properties.
guacamole    | 06:58:01.091 [http-nio-8080-exec-5] ERROR o.a.g.rest.RESTExceptionMapper - Unexpected internal error: 
guacamole    | ### Error querying database.  Cause: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
guacamole    | 
guacamole    | The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.
guacamole    | ### The error may exist in org/apache/guacamole/auth/jdbc/user/UserMapper.xml
guacamole    | ### The error may involve org.apache.guacamole.auth.jdbc.user.UserMapper.selectOne
guacamole    | ### The error occurred while executing a query
guacamole    | ### Cause: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
guacamole    | 
guacamole    | The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.
```

Solutions:
Ensure you (a) define the `networks` or (b) use `link`  

## References
https://dev.mysql.com/doc/mysql-installation-excerpt/8.0/en/docker-mysql-getting-started.html
https://elatov.github.io/2018/06/install-guacamole-on-docker/
https://docs.docker.com/compose/compose-file/
https://guacamole.apache.org/doc/gug/guacamole-docker.html
https://github.com/oznu/docker-guacamole
https://dev.mysql.com/doc/mysql-linuxunix-excerpt/8.0/en/docker-mysql-more-topics.html
https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_bind_addresss