# pmm-monitoring

## Install server:
0. You will need docker installed
1. copy docker-compose to /opt
2. do a `docker compose up -d`
3. change admin password: `docker exec -t pmm-server change-admin-password MySuperStrongPassword`

## Install client

### Debian
<code>
wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
dpkg -i percona-release_latest.generic_all.deb
apt update
apt install -y pmm2-client
pmm-admin --version
</code>

### Redhat
<code>
yum install -y https://repo.percona.com/yum/percona-release-latest.noarch.rpm
yum install -y pmm2-client
pmm-admin --version
</code>

### Manual installation
1. Visit the Percona Monitoring and Management 2 download page.
2. Under Version:, select the one you want (usually the latest).
3. Under Software:, select the item matching your software platform.
4. Copy link and download & install

## Configure client
1. Create mysql user
   <code>
   CREATE USER 'pmm'@'127.0.0.1' IDENTIFIED BY '<b>pass</b>' WITH MAX_USER_CONNECTIONS 10;
   </code>
2. Grant privileges
   MySQL 8:
   <code>
   GRANT SELECT, PROCESS, REPLICATION CLIENT, RELOAD, BACKUP_ADMIN ON *.* TO 'pmm'@'127.0.0.1';
   FLUSH PRIVILEGES;
   </code>
   MySQL 5.7:
   <code>
   GRANT SELECT, PROCESS, REPLICATION CLIENT, RELOAD ON *.* TO 'pmm'@'127.0.0.1';
   FLUSH PRIVILEGES;
   </code>
4. Configure MySQL:
   <code>
   slow_query_log=ON
   log_output=FILE
   long_query_time=1
   log_slow_admin_statements=ON
   log_slow_slave_statements=ON
   max_digest_length=8192
   performance_schema_max_digest_length=8192
   </code>
5. Restart MySQL
6. Add Service MySQL:
   <code>
   pmm-admin add mysql --username=pmm --host=127.0.0.1 --password=<b>pass</b> --size-slow-logs=2GiB
   </code>
   For mode parameters, see https://docs.percona.com/percona-monitoring-and-management/details/commands/pmm-admin.html#mysql


   
   
