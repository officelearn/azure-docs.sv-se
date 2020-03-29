---
title: Installera MySQL på en OpenSUSE-virtuell dator i Azure
description: Lär dig att installera MySQL på en Virtual OpenSUSE Linux-dator i Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 0d3f0a61da3654c31c99cfac43c86b081876f700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944579"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installera MySQL på en virtuell dator som kör OpenSUSE Linux i Azure

[MySQL](https://www.mysql.com) är en populär SQL-databas med öppen källkod. Den här självstudien visar hur du skapar en virtuell dator som kör OpenSUSE Linux och installerar sedan MySQL.


Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Skapa en virtuell dator som kör OpenSUSE Linux

Skapa först en resursgrupp. I det här exemplet heter resursgruppen *mySQSUSEResourceGroup* och skapas i regionen *östra USA.*

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Skapa den virtuella datorn. I det här exemplet heter den virtuella datorn *myVM* och vm-storleken *är Standard_D2s_v3*, men du bör välja den [vm-storlek](sizes.md) som du tycker är mest lämplig för din arbetsbelastning.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Du måste också lägga till en regel i nätverkssäkerhetsgruppen för att tillåta trafik över port 3306 för MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Du använder SSH för att ansluta till den virtuella datorn. I det här exemplet är den offentliga IP-adressen för den virtuella datorn *10.111.112.113*. Du kan se IP-adressen i utdata när du skapade den virtuella datorn.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Uppdatera den virtuella datorn
 
När du är ansluten till den virtuella datorn installerar du systemuppdateringar och korrigeringsfiler. 
   
```bash
sudo zypper update
```

Följ anvisningarna för att uppdatera den virtuella datorn.

## <a name="install-mysql"></a>Installera MySQL 


Installera MySQL i den virtuella datorn över SSH. Svara på uppmaningar efter behov.

```bash
sudo zypper install mysql
```
 
Ställ in MySQL att starta när systemet startar. 

```bash
sudo systemctl enable mysql
```
Kontrollera att MySQL är aktiverat.

```bash
systemctl is-enabled mysql
```

Detta bör returneras: aktiverat.

Starta om servern.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL-lösenord

Efter installationen är MySQL-rotlösenordet tomt som standard. Kör **mysql\_\_säker installation** skript för att säkra MySQL. Skriptet uppmanar dig att ändra MySQL-rotlösenordet, ta bort anonyma användarkonton, inaktivera fjärrrotinloggning, ta bort testdatabaser och läsa in behörighetstabellen igen. 

När servern startas om, ssh till den virtuella datorn igen.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Logga in på MySQL

Nu kan du logga in och ange MySQL-prompten.

```bash  
mysql -u root -p
```
Detta växlar dig till MySQL-prompten där du kan utfärda SQL-uttryck för att interagera med databasen.

Nu, skapa en ny MySQL användare.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Semikolon (;) i slutet av raden är avgörande för att avsluta kommandot.


## <a name="create-a-database"></a>Skapa en databas


Skapa en databas `mysqluser` och bevilja användarbehörigheterna.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Databasanvändarnamn och lösenord används endast av skript som ansluter till databasen.  Databasanvändarkontonamn representerar inte nödvändigtvis faktiska användarkonton i systemet.

Aktivera inloggning från en annan dator. I det här exemplet är DATORNS IP-adress som ska tillåta inloggning från *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Om du vill avsluta verktyget för administration av MySQL-databaser skriver du:

```    
quit
```


## <a name="next-steps"></a>Nästa steg
Mer information om MySQL finns i [MySQL-dokumentationen](https://dev.mysql.com/doc).




