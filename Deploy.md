# How to compile & Deploy on Ubuntu 18.04
The project is using Typescript as main language and not other dependencies on external libraries (everything is already included).

# requirements
root access
```
apt update && apt upgrade -y && apt install nodejs npm apache2 php php-curl -y
```

# Compilation
The first step will be to compile the typescript code into javascript code so browsers will be able to understand it. 
You also need to build some files that are dynamically generated like the manifest ...
This task is doable with :
```
git clone https://github.com/ParsiCoin/webwallet && cd webwallet
npm install
nodejs ./node_modules/typescript/bin/tsc --project tsconfig.prod.json
nodejs build.js
```
The first task install dependencies (typescript) and the text one compile the typescript code.
We are using a custom tsconfig file which is optimized for production.

# Change configuration
You will have to edit the file src/config.js in order to change the API endpoint. 
The default value use the same domain appended by /api/

That's all

# Deploy
All the content of the src directory needs to be exposed with a web-server.
You will also need to expose the content of the src_api content to an endpoint which can interpret PHP.
By default the configuration looks at domainname.com/api/
you need to run parsicoind with the following flags :
```
./parsicoind --restricted-rpc --enable-cors=* --enable-blockchain-indexes --rpc-bind-ip=0.0.0.0
```
# Permissions
The API stores precomputed data for performances in a directory called cache/ in the same directory of the API code (PHP code).
You will need to create this directory with the write permissions.

# Cron task / Process
Precomputed data are build by another process. This process will call the ParsiCoin daemon and compute blocks into chunks of blocks to reduce network latency.

One way to handle this is by running a cron task on /etc/crontab each 2 minutes with something like:
```
*/2 * * * * root curl https://wallet.domainname.com/api/blockchain.php?gen=1
```
or :
```
* * * * * root cd /var/www/html/api && export generate=true && php blockchain.php
```
