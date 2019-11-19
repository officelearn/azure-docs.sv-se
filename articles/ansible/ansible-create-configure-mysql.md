---
title: Självstudie – konfigurera databaser i Azure Database for MySQL med Ansible
description: Lär dig hur du använder Ansible för att skapa och konfigurera en Azure Database for MySQL-server
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d8c8fd361f37a9cb961012807fe40b905c10c047
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156525"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Självstudie: konfigurera databaser i Azure Database for MySQL med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) är en Relations databas tjänst baserad på MySQL Community Edition. Med Azure Database for MySQL kan du hantera MySQL-databaser i dina webbappar.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en MySql-Server
> * Skapa en MySql-databas
> * Konfigurera en filewall-regel så att en extern app kan ansluta till servern
> * Ansluta till MySql-servern från Azure Cloud Shell
> * Fråga dina tillgängliga MySQL-servrar
> * Visa en lista över alla databaser på dina anslutna servrar

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Spelbok-koden i det här avsnittet skapar en Azure-resurs grupp. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

Spara följande spelbok som `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Se följande anmärkningar innan du kör Spelbok:

* En resurs grupp med namnet `myResourceGroup` skapas.
* Resurs gruppen skapas på `eastus` plats:

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Skapa en MySQL-server och en databas

Spelbok-koden i det här avsnittet skapar en MySQL-server och en Azure Database for MySQL-instans. Den nya MySQL-servern är en gen 5-grundläggande syftes server med en vCore och heter `mysqlserveransible`. Databas instansen heter `mysqldbansible`.

Mer information om pris nivåer finns i [Azure Database for MySQL pris nivåer](/azure/mysql/concepts-pricing-tiers). 

Spara följande spelbok som `mysql_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Se följande anmärkningar innan du kör Spelbok:

* I avsnittet `vars` måste värdet för `mysqlserver_name` vara unikt.
* I avsnittet `vars` ersätter du `<server_admin_password>` med ett lösen ord.

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel

En brand Väggs regel på server nivå gör att en extern app kan ansluta till servern via Azure MySQL-tjänstens brand vägg. Exempel på externa appar är kommando rads verktyget `mysql` och MySQL Workbench.

Spelbok-koden i det här avsnittet skapar en brand Väggs regel med namnet `extenalaccess` som tillåter anslutningar från valfri extern IP-adress. 

Spara följande spelbok som `mysql_firewall.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

Se följande anmärkningar innan du kör Spelbok:

* I avsnittet kopplingsvariabler ersätter du `startIpAddress` och `endIpAddress`. Använd intervallet av IP-adresser som motsvarar intervallet som du ska ansluta till.
* Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I så fall kan du inte ansluta till servern såvida inte IT-avdelningen öppnar port 3306.
* Spelbok använder `azure_rm_resource`-modulen, som tillåter direkt användning av REST API.

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Ansluta till servern

I det här avsnittet använder du Azure Cloud Shell för att ansluta till den server som du skapade tidigare.

1. Välj knappen **prova** i följande kod:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Ange följande kommando i kommando tolken för att fråga Server statusen:

    ```sql
    mysql> status
    ```
    
    Om allt går bra ser du utdata som liknar följande resultat:
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>Fråga MySQL-servrar

Spelbok-koden i det här avsnittet frågar MySQL-servrar i `myResourceGroup` och listar databaserna på de servrar som hittas.

Spara följande spelbok som `mysql_query.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook mysql_query.yml
```

När du har kört Spelbok visas utdata som liknar följande resultat:

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Du ser även följande utdata för MySQL-databasen:

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs. 

Spara följande spelbok som `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)