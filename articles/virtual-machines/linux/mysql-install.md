---
title: Ställ in MySQL på en Linux-VM i Azure | Microsoft Docs
description: Lär dig hur du installerar MySQL-stacken på en virtuell Linux-dator (Ubuntu eller RedHat familjen OS) i Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: d91f8cf8455a60d3e0afb2f209ba07933bcdee1c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239182"
---
# <a name="how-to-install-mysql-on-azure"></a>Så installerar du MySQL på Azure
I den här artikeln får lära du dig att installera och konfigurera MySQL på en Azure-dator som kör Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Installera MySQL på den virtuella datorn
> [!NOTE]
> Du måste redan ha en Microsoft Azure-dator som kör Linux för att kunna slutföra den här kursen. Finns det [virtuella Azure Linux-datorn kursen](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att skapa och konfigurera en Linux-VM med `mysqlnode` som namnet på virtuella datorn och `azureuser` som användare innan du fortsätter.
> 
> 

I det här fallet Använd 3306 port som MySQL-port.  

Ansluta till Linux VM som du skapade via putty. Om det här är första gången du använder virtuella Azure Linux-datorn, se hur du använder putty ansluta till en Linux-VM [här](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vi använder databasen paketet för att installera MySQL5.6 som ett exempel i den här artikeln. Faktiskt har MySQL5.6 flera förbättringar i prestanda än MySQL5.5.  Mer information [här](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Hur du installerar MySQL5.6 på Ubuntu
Vi använder Linux VM med Ubuntu från Azure här.

* Steg 1: Installera MySQL Server 5.6 växla till `root` användare:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Installera server 5.6 mysql:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Under installationen visas en dialogruta fönstret poping upp till som ber du ställa in MySQL rotlösenordet nedan, och du måste ange lösenordet här.
  
    ![Bild](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Ange lösenordet igen för att bekräfta.

    ![Bild](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Steg 2: Logga in MySQL-Server
  
    När du är klar MySQL-serverinstallation startas MySQL-tjänsten automatiskt. Du kan logga in MySQL-Server med `root` användare.
    Använd den nedan kommando för att logga in och indata lösenord.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Steg 3: Hantera MySQL-tjänst som körs
  
    (a) Hämta status för MySQL-tjänst
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) Start MySQL Service
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) Stoppa MySQL-tjänst
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) starta om tjänsten MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Så här installerar du MySQL för Red Hat OS-familjen som CentOS, Oracle Linux
Vi använder Linux VM med CentOS eller Oracle Linux här.

* Steg 1: Lägg till MySQL Yum-databas växeln till `root` användare:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Hämta och installera versionspaket MySQL:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Steg 2: Redigera nedan filen för att aktivera MySQL-databasen för att ladda ned paketet MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Uppdatera varje värde i den här filen till nedan:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Steg 3: Installera MySQL från MySQL-databas installera MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    MySQL-RPM-paket och alla relaterade paket kommer att installeras.
* Steg 4: Hantera MySQL-tjänst som körs
  
    (a) kontrollera status för tjänsten för MySQL-servern:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) kontrollera om port MySQL standardservern körs:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) starta MySQL-server:

           #[root@mysqlnode ~]#service mysqld start

    (d) Stoppa MySQL-servern:

           #[root@mysqlnode ~]#service mysqld stop

    (e) Ange MySQL att starta när uppstart system:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Hur du installerar MySQL på SUSE Linux
Vi använder Linux VM med OpenSUSE här.

* Steg 1: Hämta och installera MySQL-Server
  
    Växla till `root` användare via nedan kommando:  
  
           #sudo su -
  
    Hämta och installera MySQL-paketet:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Steg 2: Hantera MySQL-tjänst som körs
  
    (a) kontrollera status för MySQL-servern:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) kontrollera om standardporten MySQL-servern:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) starta MySQL-server:

           #[root@mysqlnode ~]# rcmysql start

    (d) Stoppa MySQL-servern:

           #[root@mysqlnode ~]# rcmysql stop

    (e) Ange MySQL att starta när uppstart system:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Nästa steg
Mer information om MySQL och användning finns [här](https://www.mysql.com/).

