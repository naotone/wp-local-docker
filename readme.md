# wp-local-docker-traefik
A WordPress local development environment based on [10up/wp-local-docker](https://github.com/10up/wp-local-docker). Supports multiple projects with traefik, https, WordMove, and Sequel Pro.  
Tested on only macOS.

# Instruction
- Edit /etc/hosts
- Generate SSL Cert(Optional)
- Edit docker-compose.yml for your projects.
- docker-compose up traefik.yml
- docker-compose up site1.yml ,site2.yml...
- Run bin/setup.sh to install WordPress.

Chnage site1 and site2 to your project name or add projects.

# Edit hosts
```
sudo vi /etc/hosts

127.0.0.1 traefik.local

127.0.0.1 site1.local
127.0.0.1 mailcatcher.site1.local
127.0.0.1 site2.local
127.0.0.1 mailcatcher.site2 .local
```

# Generate SSL Cert(Option)
```
vi traefik/certs/local.cnf
```
Add your projects url on bottom.  
Safari works with wildcards.  
Chrome only works with sepecific host name or ip address.  
FireFox doesn't work with both.  
```
IP.1  = 127.0.0.1
DNS.1 = *.local
DNS.2 = *.*.local
DNS.3 = traefik.local
DNS.4 = site1.local
DNS.5 = site2.local
```
```
cd traefik/certs
sudo openssl req \        
  -newkey     rsa:2048 \
  -keyout     local.key \
  -x509 \
  -nodes \
  -out        local.crt \
  -reqexts    SAN \
  -extensions SAN \
  -config     local.cnf \
    -days       3650
```
Press enter to skip all input.
```
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain local.crt
```

# Edit docker-compose.yml for your projects.
## mysql
Use specific ports, like "3307:3306", "3308:3306"
 
```
  ports:
    - "3307:3306"
```

## mailcatcher
```
labels:
  - "traefik.backend=mailcatcher_site1"
  - "traefik.frontend.rule=Host:mailcatcher.site1.local"
```

## nginx
```
labels:
  - "traefik.backend=site1"
  - "traefik.frontend.rule=Host:site1.local"
```

# Run bin/setup.sh.
Read 10up's documentation.  
[https://github.com/10up/wp-local-docker](https://github.com/10up/wp-local-docker)


# Sequel Pro
```
Host: 127.0.0.1
Username: root
Password: password
Port: 3307 (Change port number to you defined in docker-compose.yml)
```

# WordMove
I use WordMove from macOS, not from a docker container.
It's hard to share Krypton with macOS and Docker for me😭
You need to install ruby 2.4 or higher to install WordMove. rbenv is easy to intall and manage ruby.
```
brew install rbyenv
rbenv install 2.5.1
rbenv global 2.5.1
gem install wordmove
```
If you are not a Krypton user, comment out WordMove section from docker-compose.yml.  
This article will help you. [https://medium.com/cluetip/wordpress-development-made-easy-440b564185f2](https://medium.com/cluetip/wordpress-development-made-easy-440b564185f2)
