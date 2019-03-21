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
ms.openlocfilehash: f9e0582a1338bcae7b330c7ece7c3d8cc8593cfa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004510"
---
# <a name="how-to-install-mysql-on-azure"></a>Så installerar du MySQL på Azure
I den här artikeln får lära du dig att installera och konfigurera MySQL på Azure-datorer som kör Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Installera MySQL på den virtuella datorn
> [!NOTE]
> Du måste redan ha en Microsoft Azure-dator som kör Linux för att kunna slutföra den här självstudien. Finns det [virtuell Linux-dator självstudien](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att skapa och konfigurera en Linux VM med `mysqlnode` som VM-namnen och `azureuser` som användare innan du fortsätter.
> 
> 

I så fall använda port 3306 som MySQL-port.  

Ansluta till Linux-dator som du skapade via putty. Om det här är första gången du använder virtuell Linux-dator, se hur du använder putty ansluta till en Linux VM [här](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vi använder databasen paket för att installera MySQL5.6 som ett exempel i den här artikeln. MySQL5.6 har alltså flera förbättringar i prestanda än MySQL5.5.  Mer information [här](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Hur du installerar MySQL5.6 på Ubuntu
Vi använder Linux VM med Ubuntu från Azure här.

* Steg 1: Installera MySQL Server 5.6 växla till `root` användare:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Installera mysql-server 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Under installationen visas en dialogruta som visas för att be dig att ange lösenordet för MySQL-roten nedan och du måste ange lösenord här.
  
    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Ange lösenordet igen för att bekräfta.

    ![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Steg 2: Logga in MySQL-Server
  
    När du MySQL server-installationen är klar kommer MySQL-tjänsten att startas automatiskt. Du kan logga in MySQL-Server med `root` användare.
    Använd den nedanstående kommando till lösenord för inloggning och indata.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Steg 3: Hantera tjänsten som körs MySQL
  
    (a) Hämta status för MySQL-tjänsten
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) starta MySQL-tjänsten
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) Stoppa MySQL-tjänsten
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) starta om tjänsten MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Så här installerar du MySQL på Red Hat OS-familj som CentOS, Oracle Linux
Vi använder Linux VM med CentOS eller Oracle Linux här.

* Steg 1: Lägg till MySQL Yum lagringsplats växel till `root` användare:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Hämta och installera MySQL-versionspaketet:
  
            #[root@mysqlnode ~]# wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Steg 2: Redigera nedan konfigureringsfilen för att aktivera MySQL-databasen för att ladda ned paketet MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Uppdatera varje värde i den här filen till nedan:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Steg 3: Installera MySQL från MySQL-databasen installera MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    MySQL-RPM-paket och alla relaterade paket kommer att installeras.
* Steg 4: Hantera tjänsten som körs MySQL
  
    (a) kontrollera status för tjänsten på den MySQL-servern:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) kontrollera om standard-port av MySQL-servern körs:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) starta MySQL-server:

           #[root@mysqlnode ~]#service mysqld start

    (d) Stoppa MySQL-server:

           #[root@mysqlnode ~]#service mysqld stop

    (e) set MySQL att starta när systemet uppstart:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Så här installerar du MySQL på SUSE Linux
Vi använder Linux VM med OpenSUSE här.

* Steg 1: Ladda ned och installera MySQL-Server
  
    Växla till `root` användaren med kommandot nedan:  
  
           #sudo su -
  
    Hämta och installera MySQL-paketet:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Steg 2: Hantera tjänsten som körs MySQL
  
    (a) kontrollera status för MySQL-server:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) kontrollera om standardporten för MySQL-servern:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) starta MySQL-server:

           #[root@mysqlnode ~]# rcmysql start

    (d) Stoppa MySQL-server:

           #[root@mysqlnode ~]# rcmysql stop

    (e) set MySQL att starta när systemet uppstart:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Nästa steg
Hitta fler användning och information om MySQL [här](https://www.mysql.com/).

