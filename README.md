## RDS Catalog for Photo Supreme

Creates and configures a VPC to hold an RDS instance & a functioning database for use as the catalog DB by Photo Supreme Server Edition (http://www.idimager.com/WP/?page_id=20).  See [discussion on the forum](http://forum.idimager.com/viewtopic.php?f=57&t=23689&p=108409#p108409).

### Prerequisites

* Ansible 1.8.2
* Python `psycopg2` module
* AWS Credentials

### Usage

```
$ AWS_ACCESS_KEY_ID=<accessKey> AWS_SECRET_ACCESS_KEY=<secretKey> ansible-playbook -i hosts.ini --verbose playbook.yml
```

### Tuning Parameters

| Parameter              | Recommended value/formula            |
|------------------------|--------------------------------------|
| `max_connections`      | 100                                  |
| `shared_buffers`       | `{DBInstanceClassMemory/32768}`      |
| `effective_cache_size` | `{DBInstanceClassMemory*75/819200}`  |
| `work_mem`             | `{DBInstanceClassMemory/102400}` [1] | 

* [Photo Supreme Installation Guide](http://www.idimager.com/Trial/QuickInstall-PostgreSQL9-PhotoSupreme.pdf)
* [Guide to tuning a Postgres RDS Instance](http://www.davidmkerr.com/2013/11/tune-your-postgres-rds-instance-via.html)

Alternatively, you can use a tool like [RDSTune](https://bitbucket.org/davidkerr/rdstune).

### Bugs

* RDS instance is not marked as public.  Since AWS does not provide a way to change this property after the instance has been created it needs to be dropped and recreated. More info:
    * https://github.com/ebridges/rds-photo-supreme-catalog/issues/1
    * https://groups.google.com/forum/#!topic/ansible-project/jPcfjXH0cc0
    * https://github.com/ansible/ansible-modules-core/pull/5
    * https://github.com/ansible/ansible/pull/8834

* After running, an inbound rule needs to be created in the routing table for the VPC:

 | Destination | Target | Status | Propagated |
 | ----------- | ------ | ------ | ---------- |
 | 0.0.0.0/0   |  igw   | Active | No         |

* The Ansible `rds_parameter_group` task currently is not succeeding in creating a parameter group, to tweak the database's parameters.  As a result these must be entered manually (or use RDSTune).

## Creating the Schema

After an instance & database have been created, the the Photo Supreme schema can be built.  This is done using the two scripts that accompany the distribution.

```sh
$ psql -W \
    --host <DB HOSTNAME> \
    --user idimager_main \
    --file "<PATH TO PHOTO SUPREME INSTALLATION>/postgresql9_ididb.sql" \
    photosupreme
```

And the thumbnail schema...
```sh
$ psql -W \
    --host <DB HOSTNAME> \
    --user idimager_main \
    --file "<PATH TO PHOTO SUPREME INSTALLATION>/postgresql9_tmbdb.sql" \
    photosupreme
```

[1]: max_connections * 1024 = 102400
