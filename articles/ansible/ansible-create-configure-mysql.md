---
title: Skapa och konfigurera en Azure Database for MySQL-server med hjälp av Ansible
description: Lär dig hur du använder Ansible för att skapa och konfigurera en Azure Database for MySQL-server
ms.service: azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 63472cf9c4b6b16f74ececfb6c6e61cf5f89ff9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095406"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible"></a>Skapa och konfigurera en Azure Database for MySQL-server med hjälp av Ansible
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. 

Den här snabbstarten visar hur du använder Ansible för att skapa en Azure Database for MySQL-server och konfigurera dess brandväggsregeln. Du kan slutföra dessa uppgifter på ungefär fem minuter med hjälp av Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 krävs för att köra följande exempelspelböcker i den här självstudien. 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

I följande exempel skapas en resursgrupp med namnet **myResourceGroup** på platsen **eastus**:

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

Spara den föregående spelboken som **rg.yml**. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Skapa en MySQL-server och en databas
I följande exempel skapas en MySQL-server med namnet **mysqlserveransible** och en Azure Database for MySQL-instans med namnet **mysqldbansible**. Det här är 5:e generationens server för generell användning med en virtuell kärna. 

Värdet för **mysqlserver_name** måste vara unikt. Information om giltiga värden per region och per nivå finns i [dokumentationen om prisnivåer](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Ersätt `<server_admin_password>` med ett lösenord.

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

Spara den föregående spelboken som **mysql_create.yml**. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel
En brandväggsregel på servernivå gör att ett externt program kan ansluta till din server via Azure PostgreSQL-tjänstens brandvägg. Ett exempel på ett externt program är **mysql**-kommandoradsverktyget eller MySQL Workbench.
I följande exempel skapas en brandväggsregel som kallas **externalaccess** och som tillåter anslutningar från en valfri extern IP-adress. 

Ange dina egna värden för **startIpAddress** och **endIpAddress**. Använd intervallet med de IP-adresser som motsvarar den plats som du kommer att ansluta från. 

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

> [!NOTE]
> Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I så fall kan du inte ansluta till servern såvida inte IT-avdelningen öppnar port 3306.
> 

Här används modulen **azure_rm_resource** till att utföra den här åtgärden. Den medger direkt användning av REST API.

Spara den föregående spelboken som **mysql_firewall.yml**. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Ansluta till servern med hjälp av kommandoradsverktyget
Du kan [ladda ned MySQL](https://dev.mysql.com/downloads/) och installera det på datorn. Du kan även välja knappen **Prova** i kodexemplen eller knappen **>_** i det övre högra verktygsfältet på Azure-portalen och öppna **Azure Cloud Shell**.

Ange nästa kommandon: 

1. Anslut till servern med hjälp av **mysql**-kommandoradsverktyget:
   ```azurecli-interactive
   mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
   ```

2. Visa serverstatusen:
   ```sql
   mysql> status
   ```

Om allt går väl kommer kommandoradverktyget returnera följande text:

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

## <a name="using-facts-to-query-mysql-servers"></a>Använda fakta för att köra frågor mot MySQL-servrar
Följande exempel kör frågor mot MySQL-servrar i **myResourceGroup** och därefter alla databaser på servrarna:

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

Spara den föregående spelboken som **mysql_query.yml**. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook mysql_query.yml
```

Därefter visas följande utdata för MySQL-servern: 
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

Det visas även följande utdata för MySQL-databasen:
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

Om du inte behöver resurserna kan du ta bort dem genom att köra följande exempel. Exemplet tar bort en resursgrupp med namnet **myResourceGroup**. 

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

Spara den föregående spelboken som **rg_delete.yml**. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook rg_delete.yml
```

Om du bara vill ta bort den nyligen skapade MySQL-server kör du följande exempel:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Spara den föregående spelboken som **mysql_delete.yml**. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](https://docs.microsoft.com/azure/ansible/)
