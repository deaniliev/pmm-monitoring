# pmm-monitoring

## Install server

### Docker

0. You will need docker installed
1. copy docker-compose to /opt
2. do a `docker compose up -d`
3. change admin password: `docker exec -t pmm-server change-admin-password MySuperStrongPassword`

### Install Dashboard "MySQL Query Performance Troubleshooting (Designed for PMM2)"

1. This plugin requres the following plugins installed:

   - Breadcrumb
   - Graph (old) Singlestat Table
   - Text

2. Install and configure "Altinity plugin for ClickHouse"

   - After installing the above plugin go to "Configuration > Data sources" and select newly installed plugin "Altinity plugin for ClickHouse". In "URL" field we add `http://localhost:8123` and save.
   - Next, we need to import new Dashboard: "Dashboards > + Import", then in field "Import via grafana.com" we enter 12630 and click Load and Import. In the configuration we should select where to place this Dashboard (typically under MySQL), Prometheus Metrics and ClickHouse datasource.
   - Save and voila.

## Install client

### Debian

```bash
wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
dpkg -i percona-release_latest.generic_all.deb
apt update
apt install -y pmm2-client
pmm-admin --version
```

### Redhat

```bash
yum install -y https://repo.percona.com/yum/percona-release-latest.noarch.rpm
yum install -y pmm2-client
pmm-admin --version
```

### Manual installation

1. Visit the Percona Monitoring and Management 2 download page.
2. Under Version:, select the one you want (usually the latest).
3. Under Software:, select the item matching your software platform.
4. Copy link and download & install

## Configure client

1. Create mysql user

   ```bash
   CREATE USER 'pmm'@'127.0.0.1' IDENTIFIED BY 'pass' WITH MAX_USER_CONNECTIONS 10;
   ```

2. Grant privileges
   MySQL 8:

   ```bash
   GRANT SELECT, PROCESS, REPLICATION CLIENT, RELOAD, BACKUP_ADMIN ON *.* TO 'pmm'@'127.0.0.1';
   FLUSH PRIVILEGES;
   ```

   MySQL 5.7:

   ```bash
   GRANT SELECT, PROCESS, REPLICATION CLIENT, RELOAD ON *.* TO 'pmm'@'127.0.0.1';
   FLUSH PRIVILEGES;
   ```

3. Configure MySQL:

   ```bash
   slow_query_log=ON
   log_output=FILE
   long_query_time=1
   log_slow_admin_statements=ON
   log_slow_slave_statements=ON
   max_digest_length=8192
   performance_schema_max_digest_length=8192
   ```

4. Restart MySQL

5. Register node:

   ```bash
   pmm-admin config --server-insecure-tls --server-url=https://admin:pass@server-hostname-or-ip:443
   ```

6. Add Service MySQL:

   ```bash
   pmm-admin add mysql --username=pmm --host=127.0.0.1 --password=pass --size-slow-logs=2GiB
   ```

   For mode parameters, see https://docs.percona.com/percona-monitoring-and-management/details/commands/pmm-admin.html#mysql
