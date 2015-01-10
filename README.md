## RDS Catalog for Photo Supreme

Creates an RDS instance for using as the catalog for Photo Supreme Server Edition (http://www.idimager.com/WP/?page_id=20)

### Prerequisites

* Ansible 1.8.2
* Python `psycopg2` module
* [`ec2.py`](https://raw.githubusercontent.com/ansible/ansible/devel/plugins/inventory/ec2.py) dynamic inventory script
* [`ec2.ini`](https://raw.githubusercontent.com/ansible/ansible/devel/plugins/inventory/ec2.ini) configuration for `ec2.py`
* AWS Credentials

### Usage

```
$ AWS_ACCESS_KEY_ID=<accessKey> AWS_SECRET_ACCESS_KEY=<secretKey> ansible-playbook -i ec2.py --verbose playbook.yml
```

### Description

Creates and configures a VPC to hold an RDS instance.

### To Do

* Fix the issue (below) with RDS not being marked as publicly accessible. Without this, the script will only be able to set up the network for you only (which is still somewhat useful).
* Include the [configuration tuning parameters](http://www.idimager.com/Trial/QuickInstall-PostgreSQL9-PhotoSupreme.pdf) as part of the setup.
* Apply the schema creation scripts.

### Caveats

* RDS instance is not marked as public.  Since AWS does not provide a way to change this property after the instance has been created it needs to be dropped and recreated. More info:
    * https://github.com/ebridges/rds-photo-supreme-catalog/issues/1
    * https://groups.google.com/forum/#!topic/ansible-project/jPcfjXH0cc0
    * https://github.com/ansible/ansible-modules-core/pull/5
    * https://github.com/ansible/ansible/pull/8834

* After running, an inbound rule needs to be created in the routing table for the VPC:

 | Destination | Target | Status | Propagated |
 | ----------- | ------ | ------ | ---------- |
 | 0.0.0.0/0   |  igw   | Active | No         |
