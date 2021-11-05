# ckb-env

This is a project for establishing an all-in-one ckb environment with docker-compose. It includes the following components:

- ckb
- ckb-indexer
- ckb-mercury
- postgres


## Get started

First, you need a environment which can run Docker properly, the Apple M1 chip probably not working.

Then, you need to install [Docker Compose](https://docs.docker.com/compose/) or [Portainer](https://www.portainer.io/) to start all services in one click.

Now, clone this project with `git clone https://github.com/linkdesu/ckb-env.git` .

But before you start services, you need basicly 3 step to get configs ready:

> WARNING! DO NOT modify ports in configs, they are also hard-coded in `compose.yaml`, so only modify them in the `.env` file.

- Copy and paste `.env.*.example` to `.env` and modify it base on your requirements, you most likely want to modify the `PROJECT_DIR` to indicate where this project directory is on your system.
- Config `mercury/config.toml` base on your requirements, you most likely want to modify `network_config.network_type` to indicate mainnet or testnet.
- Config `ckb.toml` base on your requirements, you most likely want to modify `chain.spec` to indicate mainnet or testnet.

After all that preparation, you may start all services now with run `docker-compose up` or click `Deploy this stack` .


## Service Dependencies

If you know the references of compose.yaml you will find that:

- `ckb-node` is the core service, nothing can run without it.
- `mercury` is depend on `postgres`.
- If you do not need `mercury` you will not need `postgres`.
- You may choose one of `mercury` and `ckb-indexer`, `mercury` is the successor of `ckb-indexer`.

## Data directories

After start all services you may need to sync everything from ckb network in about 48 hours, faster machine and network can shrink the waiting time significantly.

Then if you do not want to sync again and again, remember to protected the following directory carefully:

- `./ckb-indexer`, it stores the rocksdb of ckb-indexer service.
- `./ckb-node/data`, it store the rocksdb of ckb-node service.
- `./postgres/data`, it store the postgres database which required by mercury service.


## Other things you should know

### Redeploy

You may face some failsure while deploying, network failsure, permission denied and so on. When any of these break the depolyment process, you need to clear `./postgres/data` directory, then it will be ok to redeploy as many times as possible until you solved every problems.

### Postgres Problems

- Before start the service, if you run Docker with user other than 1000:1000, you will need to create a user named postgres in host, this is caused by [how postgres runs the SQLs in initdb](https://hub.docker.com/_/postgres).

### Mercury Problems


