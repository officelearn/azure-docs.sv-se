---
title: Skapa och konfigurera en Azure Database for MySQL-server med Ansible (förhandsversion)
description: Lär dig hur du använder Ansible för att skapa och konfigurera en Azure Database for MySQL-server
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 508274d11a9693d28a9b3a01bd6ebbd7198e8711
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586708"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-using-ansible-preview"></a>Skapa och konfigurera en Azure Database for MySQL-server med Ansible (förhandsversion)
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database for MySQL-server på ungefär fem minuter med Azure Portal. 

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Den här artikeln visar hur du använder Ansible för att skapa en Azure Database for MySQL-server och konfigurera brandväggsregeln på fem minuter. 

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 krävs för att köra följande exempelspelböcker i den här självstudien. Du kan installera Ansible 2.7 RC-versionen genom att köra `sudo pip install ansible[azure]==2.7.0rc2`. Ansible 2.7 släpps i oktober 2018. Efter det behöver du inte ange versionen här, eftersom standardversionen blir 2.7.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

I följande exempel skapas en resursgrupp med namnet **myResourceGroup** på platsen **eastus**.

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

Spara spelboken ovan som *rg.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook rg.yml
```

## <a name="create-mysql-server-and-database"></a>Skapa MySQL-servern och databasen
I följande exempel skapas en MySQL-server med namnet **mysqlserveransible** och en Azure Database for MySQL med namnet **mysqldbansible**. Det här är 5:e generationens server för generell användning med en virtuell kärna. Observera att värdet för **mysqlserver_name** måste vara unikt, och läs mer i [dokumentationen om prisnivåer](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) så att du förstår vilka värden som är giltiga för varje region och nivå. 

Ange ett eget `<server_admin_password>`:

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

Spara spelboken ovan som *mysql_create.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-firewall-rule"></a>Konfigurera brandväggsregeln
En brandväggsregel på servernivå gör att externa program, som kommandoradsverktyget **mysql** eller MySQL Workbench, kan ansluta till servern via Azure MySQL-tjänstens brandvägg. I följande exempel skapas en brandväggsregel som kallas **externalaccess** och som tillåter anslutningar från en valfri extern IP-adress. 

Ange din egen **startIpAddress** och **endIpAddress** med det intervall av IP-adresser som motsvarar platsen du ska ansluta ifrån: 

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
> Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 3306.
> 

Här används modulen **azure_rm_resource** för att utföra åtgärden, vilket möjliggör direkt användning av REST API.

Spara spelboken ovan som *mysql_firewall.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-using-command-line-tool"></a>Anslut till servern med kommandoradsverktyget
Du kan hämta MySQL [här](https://dev.mysql.com/downloads/) och installera programmet på din dator. Du kan även klicka på knappen **Prova** på kodexemplen eller på `>_`-knappen i det övre högra verktygsfältet i Azure-portalen och starta **Azure Cloud Shell**.

Skriv nästa kommandon: 

1. Anslut till servern med kommandoradsverktyget **mysql**:
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. Visa status för servern:
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

## <a name="using-facts-to-query-mysql-servers"></a>Använda fakta för att ställa frågor till MySQL-servrar
Följande exempel frågar MySQL-servrar i **myResourceGroup** och därefter alla databaser på servern:

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

Spara spelboken ovan som *mysql_query*.yml. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook mysql_query.yml
```

Därefter visas följande utdata för MySQL-server: 
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

Du kan även se följande utdata för MySQL-databas:
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

Om du inte behöver resurserna kan du ta bort dem genom att köra exemplet nedan. Exemplet tar bort en resursgrupp med namnet **myResourceGroup**. 

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

Spara spelboken ovan som *rg_delete*.yml. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook rg_delete.yml
```

Om du bara vill ta bort den enda MySQL-servern som skapades nyligen kan du göra det genom att köra följande exempel:

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

Spara spelboken ovan som *mysql_delete.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible i Azure](https://docs.microsoft.com/azure/ansible/)