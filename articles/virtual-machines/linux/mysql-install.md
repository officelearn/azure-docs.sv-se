---
title: Konfigurera MySQL på en Linux-VM i Azure | Microsoft Docs
description: Lär dig att installera MySQL-stacken på en Linux-dator (Ubuntu eller Red Hat-familjen OS) i Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158443"
---
# <a name="how-to-install-mysql-on-azure"></a>Så installerar du MySQL på Azure
I den här artikeln får lära du dig att installera och konfigurera MySQL på Azure-datorer som kör Linux.


> [!NOTE]
> Du måste redan ha en Microsoft Azure-dator som kör Linux för att kunna slutföra den här självstudien. Finns det [virtuell Linux-dator självstudien](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att skapa och konfigurera en Linux VM med `mysqlnode` som VM-namnen och `azureuser` som användare innan du fortsätter.
> 
> 

I så fall använda port 3306 som MySQL-port.  

Vi använder databasen paket för att installera MySQL5.6 som ett exempel i den här artikeln. MySQL5.6 har alltså flera förbättringar i prestanda än MySQL5.5.  Mer information [här](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Installera MySQL5.6 på Ubuntu
Vi använder en Linux VM som kör Ubuntu.


### <a name="install-mysql"></a>Installera MySQL

Installera MySQL Server 5.6 genom att växla till den `root` användaren:

```bash  
sudo su -
```

Installera mysql-server 5.6:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
Under installationen visas en dialogruta som visas för att be dig att ange lösenordet för MySQL-roten nedan och du måste ange lösenord här.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Ange lösenordet igen för att bekräfta.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Logga in
  
När du MySQL server-installationen är klar kommer MySQL-tjänsten att startas automatiskt. Du kan logga in på MySQL-Server med den `root` användaren, och ange ditt lösenord.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>Hantera MySQL-tjänsten

Hämta status för MySQL-tjänsten

```bash   
service mysql status
```
  
Starta MySQL-tjänsten

```bash  
service mysql start
```
  
Stoppa MySQL-tjänsten

```bash  
service mysql stop
```
  
Starta om tjänsten MySQL

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Installera MySQL på Red Hat OS, CentOS, Oracle Linux
Vi använder Linux VM med CentOS eller Oracle Linux här.

### <a name="add-the-mysql-yum-repository"></a>Lägg till MySQL yum lagringsplats
    
Växla till `root` användare:

```bash  
sudo su -
```

Hämta och installera MySQL-versionspaketet:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>Aktivera MySQL-databasen
Redigera nedan konfigureringsfilen för att aktivera MySQL-databasen för att ladda ned paketet MySQL5.6.

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Uppdatera varje värde i den här filen till nedan:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>Installera MySQL 

Installera MySQL från databasen.

```bash  
yum install mysql-community-server
```
  
MySQL-RPM-paketet och alla relaterade paket kommer att installeras.


## <a name="manage-the-mysql-service"></a>Hantera MySQL-tjänsten
  
Kontrollera status för tjänsten på den MySQL-servern:

```bash  
service mysqld status\
```
  
Kontrollera om standard-port av MySQL-servern körs:

```bash  
netstat  –tunlp|grep 3306
```

Starta MySQL-server:

```bash
service mysqld start
```

Stoppa MySQL-server:

```bash
service mysqld stop
```

Ange MySQL att starta när systemet uppstart:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>Installera MySQL på SUSE Linux

Vi använder Linux VM med OpenSUSE här.

### <a name="download-and-install-mysql-server"></a>Ladda ned och installera MySQL-Server
  
Växla till `root` användaren med kommandot nedan:  

```bash  
sudo su -
```
  
Hämta och installera MySQL-paketet:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>Hantera MySQL-tjänsten
  
Kontrollera status för MySQL-server:

```bash  
rcmysql status
```
  
Kontrollera om standardporten för MySQL-servern:

```bash  
netstat  –tunlp|grep 3306
```

Starta MySQL-server:

```bash
rcmysql start
```

Stoppa MySQL-server:

```bash
rcmysql stop
```

Ange MySQL att starta när systemet uppstart:

```bash
insserv mysql
```

## <a name="next-step"></a>Nästa steg
Mer information finns i den [MySQL](https://www.mysql.com/) webbplats.

