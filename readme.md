This project is a compilation of examples of træfik configurations for the authentication.

# Usage

## launch the stack

Go the the directory of the example you want. Then use the docker command :

```bash
docker-compose up -d
```

To authenticate you, use the test:test credential or user:user for the forward authentication.  
Now you just have to GET the `http://whoami` URL with the appropriate authentication method.


The actual examples are:

| Authent  | Conf       | Provider |  Example directory                  |
| -------- | ---------- | -------- | ----------------------------------- |
| Basic    | Entrypoint | File     | basic_auth_on_entrypoint/           |
| Basic    | Frontend   | File     | basic_auth_on_frontend_with_file/   |
| Basic    | Frontend   | Docker   | basic_auth_on_frontend_with_label/  |
| Digest   | Entrypoint | File     | digest_auth_on_entrypoint/          |
| Digest   | Frontend   | File     | digest_auth_on_frontend_with_file/  |
| Digest   | Frontend   | Docker   | digest_auth_on_frontend_with_label/ |
| Forward  | EntryPoint | File     | forward_auth_on_entrypoint/         |
| Forward  | Frontend   | File     | forward_auth_on_frontend_with_file/ |
| Forward  | Frontend   | Docker   | forward_auth_on_frontend_with_label/|

# Basic Auth
Use htpasswd to generate the user:mdp both for file and label configuration.  
Use ```sed -e s/\\$/\\$\\$/g``` to escape the passwords in the labels.  
Their is both users and usersFile configurations to check that all user:password are taken into account.  

# Digest Auth
Use htdigest to generate the user:mdp both for file and label configuration.  
Their is both users and usersFile configurations to check that all user:realm:password are taken into account.  

# Forward Auth

The forward auth uses and httpd (v2.4) server as an external http auth server.  
It is configured from [this documentation](https://httpd.apache.org/docs/2.4/howto/auth.html)  
You can custom the configuration by overriding the httpd.conf file.  
You can change the user:password by modifying .htpasswd and .htdigest files.  


# How to

## create the basic auth password file

To create the password file
```bash
htpasswd -c .htpasswd user
# then type the password
```
To add another user:password
```bash
htpasswd .htpasswd anotherUser
```

Use ```sed -e s/\\$/\\$\\$/g``` to escape the passwords in the labels

## create the digest auth password file

To create the password file
```bash
htdigest -c .htdigest user realm
# then type the password
```
To add another user:password
```bash
htdigest .htdigest anotherUser realm
```

## test the auth server with curl (and logs)

Add the auth server in the /etc/hosts
```bash
sudo sh -c 'echo "10.0.1.10       auth.server" >> /etc/hosts'
```
Then, you can make the request with the server name.
```bash
curl -u user:user -k https://auth.server:443 --cert certs/client.pem --key certs/client.key -vvv
```