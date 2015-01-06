## RDS Catalog for Photo Supreme

Creates an RDS instance for using as the catalog for Photo Supreme Server Edition (http://www.idimager.com/WP/?page_id=20)

### Prerequisites

* Ansible 1.8.2
* Python `psycopg2` module
* [`ec2.py`](https://raw.githubusercontent.com/ansible/ansible/devel/plugins/inventory/ec2.py) dynamic inventory script
* [`ec2.ini`](https://raw.githubusercontent.com/ansible/ansible/devel/plugins/inventory/ec2.ini) configuration for `ec2.py`

### Usage

```
$ ansible-playbook -i ec2.py --verbose playbook.yml
```

### Description

Creates and configures a VPC to hold an RDS instance.

### Caveats

Still very much a work in progress.
