# Starting/Stoppping a Stack in AWS

This document explains how to start/stop a stack that's already been deployed to Amazon Web Services (`AWS`). This is useful for situations where the application doesn't need to be running all the time &mdash; e.g. for demos.


## Locating the machines withing AWS

In both the starting and stopping scenarios, the AWS Management Console can be used to change the state of the machines. In order to locate the appropriate machines:

1. Log into the AWS Management Console
2. Browse to the EC2 Management Console section
3. Find the appropriate machines. There will be two of them per stack:
   * `Celery` &mdash; machine performs batch processes, such as CSV generation
   * `App` &mdash; machine that serves web and tile requests
4. Browse to the RDS Managment Console section:
   * `RDS` &mdash; machine that stores and serves the data from database
5. Browse to the Redis Cluster from AWS ElastiCache Console section:
   * `Redis` &mdash; machine that cached the database information.
6. Browse to the VPC console:
   * `VPC` &mdash; Create virtual network in AWS singapose region for secure application host.

## Stopping a stack

1. Locate the machines using the above instructions
2. Right-click each and choose `Instance State` -> `Stop`
3. Nothing else needs to be done: these machines will remain down until they are manually started back up


## Starting a stack

1. Locate the machines using the above instructions
2. Right-click each and choose `Instance State` -> `Start`
3. After a few minutes, the machines should be running, and all services should have started up
4. If any problems are noticed, see the `Troubleshooting` section below


## Troubleshooting

Most troubleshooting procedures involve logging into the machines via `SSH`. In order to do so, the key-pair that was created upon creating the stack will need to be used. Run the following command from a computer that has the `ssh` command-line utility installed, supplying both a path to the keypair file, as well as the public IP adress of the desired machine, as determined in the AWS Management Console:

```
ssh -i <PATH_TO_KEYPAIR.pem> ubuntu@<PUBLIC_IP_ADDRESS_OF_MACHINE>
```

One problem that may be encountered if the stack has been down for a long period of time is an out-of-date SSL certificate. The SSL certificate renews automatically on a schedule. However if the stack was down for a few months and missed a scheduled renewal, it can be manually renewed by connecting via `SSH` to the `App` machine and running:

```
sudo /var/lib/letsencrypt/renew-certs.py
```

Application Database hosted in RDS. If need to Manage database, it will login through cli or postgres client. RDS can not be access publicly. need to access through EC2 instance. Both EC2 instance and RDS are in same VPC. `ssh` to the EC2 instance with public IP & key-pair file. then make sure that postgres client install in EC2 instance. postgres client install in EC2 instance. Then database hostname, port,dbname collect from RDS console. Using RDS credential can be login to the postgres RDS. Commnad given below:

```
ssh -i <PATH_TO_KEYPAIR.pem> ubuntu@<PUBLIC_IP_ADDRESS_OF_MACHINE>
```
```
sudo apt install postgresql-client-common
sudo apt install postgres-xc-client
```
```
psql \
   --host=example.x1y2z3.ap-southeast-1.rds.amazonaws.com \
   --port=5432 \
   --username=example \
   --password \
   --dbname=example
```

Any other problems encountered are most likely not related to the starting/stopping of the stack, but rather general system issues, which may be covered in the system administration document.

See [system-administration.md](system-administration.md) for further troubleshooting guidance.
