---
title: Självstudiekurs - Konfigurera databaser i Azure Database för MySQL med Ansible
description: Lär dig hur du använder Ansible för att skapa och konfigurera en Azure Database for MySQL-server
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9cd574417733518b993bb242c2c168aba338e34a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247872"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Självstudiekurs: Konfigurera databaser i Azure Database för MySQL med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) är en relationsdatabastjänst baserad på MySQL Community Edition. Med Azure Database for MySQL kan du hantera MySQL-databaser i dina webbappar.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en MySql-server
> * Skapa en MySql-databas
> * Konfigurera en brandväggsregel så att en extern app kan ansluta till servern
> * Ansluta till mySql-servern från Azure Cloud Shell
> * Fråga dina tillgängliga MySQL-servrar
> * Lista alla databaser i anslutna servrar

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Spelbokskoden i det här avsnittet skapar en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

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

Innan du kör spelboken läser du följande anmärkningar:

* En resursgrupp `myResourceGroup` med namnet skapas.
* Resursgruppen skapas på `eastus` platsen:

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Skapa en MySQL-server och en databas

Spelbokskoden i det här avsnittet skapar en MySQL-server och en Azure-databas för MySQL-instans. Den nya MySQL-servern är en Gen 5 Basic `mysqlserveransible`Purpose-server med en vCore och heter . Databasinstansen `mysqldbansible`heter .

Mer information om prisnivåer finns i [Azure Database for MySQL-prisnivåer](/azure/mysql/concepts-pricing-tiers). 

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

Innan du kör spelboken läser du följande anmärkningar:

* I `vars` avsnittet måste värdet `mysqlserver_name` av vara unikt.
* Ersätt `vars` `<server_admin_password>` med ett lösenord i avsnittet.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel

Med en brandväggsregel på servernivå kan en extern app ansluta till servern via Azure MySQL-tjänstbrandväggen. Exempel på externa `mysql` appar är kommandoradsverktyget och MySQL Workbench.

Spelbokskoden i det här avsnittet `extenalaccess` skapar en brandväggsregel med namnet som tillåter anslutningar från en extern IP-adress. 

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

Innan du kör spelboken läser du följande anmärkningar:

* Ersätt `startIpAddress` och `endIpAddress`. Använd det intervall av IP-adresser som motsvarar det intervall som du ansluter från.
* Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I så fall kan du inte ansluta till servern såvida inte IT-avdelningen öppnar port 3306.
* Spelboken använder `azure_rm_resource` modulen, som möjliggör direkt användning av REST API.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Ansluta till servern

I det här avsnittet använder du Azure Cloud Shell för att ansluta till servern som du skapade tidigare.

1. Öppna shell.azure.com genom att välja nedan.

   [![Bädda in start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

1. Ange följande kod:

    ```sql
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. I prompten anger du följande kommando för att fråga serverns status:

    ```sql
    mysql> status
    ```
    
    Om allt går bra visas utdata som liknar följande resultat:
    
    ```output
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

Spelbokskoden i det här avsnittet frågar `myResourceGroup` in MySQL-servrar och listar databaserna på de hittade servrarna.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook mysql_query.yml
```

När du har kört spelboken visas utdata som liknar följande resultat:

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

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln. 

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)