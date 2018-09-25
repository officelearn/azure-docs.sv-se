---
title: Installera MySQL på en OpenSUSE-VM i Azure | Microsoft Docs
description: Lär dig att installera MySQL på en OpenSUSE Linux VMirtual virtuell dator i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 838915e7a5fe1d1838c9bd802305e83ec9a773d9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986786"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installera MySQL på en virtuell dator som kör OpenSUSE Linux i Azure

[MySQL](http://www.mysql.com) är en populär, open source-SQL-databas. Den här självstudien visar hur du skapar en virtuell dator som kör OpenSUSE Linux och sedan installera MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Skapa en virtuell dator som kör OpenSUSE Linux

Skapa först en resursgrupp. I det här exemplet heter resursgruppen *mySQSUSEResourceGroup* och den har skapats i den *USA, östra* region.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Skapa den virtuella datorn. I det här exemplet heter den virtuella datorn *myVM* och VM-storleken är *Standard_D2s_v3*, men du bör välja den [VM-storlek](sizes.md) du tycker passar bäst för din arbetsbelastning.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Du måste också lägga till en regel för nätverkssäkerhetsgrupp som tillåter trafik via port 3306 för MySQL.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Du använder SSH för att ansluta till den virtuella datorn. I det här exemplet, offentliga IP-adressen för den virtuella datorn är *10.111.112.113*. Du kan se IP-adressen i utdata när du skapade den virtuella datorn.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>Uppdatera den virtuella datorn
 
När du är ansluten till den virtuella datorn, installera uppdateringar och korrigeringar. 
   
```bash
sudo zypper update
```

Följ anvisningarna för att uppdatera den virtuella datorn.

## <a name="install-mysql"></a>Installera MySQL 


Installera MySQL på den virtuella datorn via SSH. Svara på frågor efter behov.

```bash
sudo zypper install mysql
```
 
Ange MySQL att starta när systemet startas. 

```bash
sudo systemctl enable mysql
```
Kontrollera att MySQL är aktiverad.

```bash
systemctl is-enabled mysql
```

Detta bör returnera: aktiverat.

Starta om servern.

```bash
sudo reboot
```


## <a name="mysql-password"></a>Lösenordet för MySQL

Rotlösenord för MySQL är tomt som standard efter installationen. Kör den **mysql\_säker\_installation** skript för att skydda MySQL. Skriptet uppmanas du att ändra rotlösenord för MySQL, ta bort anonym användarkonton, inaktivera fjärråtkomst rot logga in, ta bort test databaser och läsa in tabellen privilegier. 

När servern startas om, ssh till den virtuella datorn igen.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Logga in till MySQL

Nu kan du logga in och ange MySQL-prompten.

```bash  
mysql -u root -p
```
Det här växlar du till MySQL-prompten där du kan skicka SQL-uttryck för att interagera med databasen.

Nu skapa en ny MySQL-användare.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Semikolon (;) i slutet av raden är avgörande för att avsluta kommandot.


## <a name="create-a-database"></a>Skapa en databas


Skapa en databas och ge den `mysqluser` användarbehörigheter.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Databas-användarnamn och lösenord som endast används av skript som ansluter till databasen.  Databasen användarkontonamn utgör inte nödvändigtvis faktiska användarkonton på systemet.

Aktivera inloggning från en annan dator. I det här exemplet är IP-adressen för datorn att logga in från *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Om du vill avsluta verktyget för administration av MySQL-databas, skriver du:

```    
quit
```


## <a name="next-steps"></a>Nästa steg
Mer information om MySQL finns den [MySQL-dokumentation](http://dev.mysql.com/doc/index-topic.html).




