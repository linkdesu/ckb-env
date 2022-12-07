# ckb-env

> ⚠️ Mercury is currently not supported. ⚠️

This is a project for establishing an all-in-one ckb environment with docker-compose. It includes the following components:

- ckb
- ckb-mercury
- postgres


## Get started

First, you need a environment which can run Docker properly, the Apple M1 chip probably not working.

Then, you need to install [Docker Compose](https://docs.docker.com/compose/) or [Portainer](https://www.portainer.io/) to start all services in one click.

Now, clone this project with `git clone https://github.com/linkdesu/ckb-env.git` .

But before you start services, you need basicly 3 step to get configs ready:

> WARNING! DO NOT modify ports in configs like `ckb-node/*.toml` or `ckb-indexer/*.toml`, they are also hard-coded in `compose.yaml`, so only modify them through the `.env` file.

- Copy and paste `.env.*.example` to `.env` and modify it base on your requirements, you most likely want to modify the `PROJECT_DIR` to indicate where this project directory is on your system.
- Copy and paste `ckb-node/ckb.*.toml` to `ckb-node/ckb.toml` and `ckb-node/ckb-miner.*.toml` to `ckb-node/ckb-miner.toml` base on your requirements.
- Config `mercury/config.toml` base on your requirements, you most likely want to modify `network_config.network_type` to indicate mainnet or testnet.
- Confirm the directory permissions by simply run `docker compose up ckb-node`, if this command executed sueccessfully, then everything is OK, otherwise you need to solve the permission issues following [Permission Problems](#permission-problems).

After all that preparation, you may start all services now with run `docker compose up` or click `Deploy this stack` .


## Service Dependencies

If you know the references of compose.yaml you will find that:

- `ckb-node` is the core service, nothing can run without it.
- `mercury` is depend on `postgres`.
- If you do not need `mercury` you will not need `postgres`.


## Data directories

After start all services you may need to sync everything from ckb network in about 48 hours, faster machine and network can shrink the waiting time significantly.

Then if you do not want to sync again and again, remember to protected the following directory carefully:

- `./ckb-node/data`, it store the rocksdb of ckb-node service.
- `./postgres/data`, it store the postgres database which required by mercury service.


## Other things you should know

### Permission Problems

The service ckb-node will run as the user **ckb(1000:1000)** in container, so when the `permission denied` error occurs, you have two ways to make everything works:

- The first way is run the services as user 1000:1000 which means the id and group of the user is 1000, and ensure the path to the project directory is writable by user(1000:1000), the user's name do not have to be ckb;
- The second way is run the services as root, in this way you need to uncomment the `# user: root` line in `compose.yaml` ;

The best practice is not to run the container as root, but it is ok to choose any of them.

### Redeploy

You may face some failsure while deploying, network failsure, permission denied and so on. When any of these break the depolyment process, you need to clear `./postgres/data` directory, then it will be ok to redeploy as many times as possible until you solved every problems.

### Service postgres Problems

- Before start the service, if you run Docker with user other than 1000:1000, you will need to create a user named postgres in host, this is caused by [how postgres runs the SQLs in initdb](https://hub.docker.com/_/postgres).

### Mercury Problems

- Because we do not need it in production, it has been lost maintenance for too long, any PR is welcome.
