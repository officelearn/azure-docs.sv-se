---
title: Självstudier – Distribuera LAMP på en virtuell Linux-dator i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du installerar LAMP-stacken på en virtuell Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 5caed6fba607cb93a6168bded7531bc8bf63b9da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970693"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Självstudier: Installera en LAMP-webbserver på en virtuell Linux-dator i Azure

I den här artikeln får du veta hur du distribuerar en Apache-webbserver, MySQL och PHP (LAMP-stacken) på en virtuell Ubuntu-dator i Azure. Om du föredrar NGINX-webbservern finns mer information i självstudierna om [LEMP-stacken](tutorial-lemp-stack.md). Om du vill se LAMP-servern i praktiken kan du installera och konfigurera en WordPress-webbplats. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator (L:et i LAMP-stacken)
> * Öppna port 80 för webbtrafik
> * Installera Apache, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på LAMP-servern

Den här installationen är avsedd för snabbtester och konceptbevis. Mer information om LAMP-stacken samt rekommendationer kring produktionsmiljön finns i [dokumentationen om Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Installera Apache, MySQL och PHP

Kör följande kommando för att uppdatera Ubuntu-paketkällorna och installera Apache, MySQL och PHP. Observera textmarkören (^) i slutet av kommandot, som är en del av paketnamnet `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```


Du uppmanas att installera paketen och andra beroenden. När du uppmanas anger du ett rotlösenord för MySQL. Välj [Retur] för att fortsätta. Följ återstående anvisningar. Den här processen installerar lägsta nödvändiga PHP-tillägg för användning av PHP med MySQL. 

![MySQL-rotlösenordssida][1]

## <a name="verify-installation-and-configuration"></a>Verifiera installation och konfiguration


### <a name="apache"></a>Apache

Kontrollera versionen av Apache med följande kommando:
```bash
apache2 -v
```

När Apache är installerat och port 80 är öppen för den virtuella datorn kan webbservern nås från internet. När du vill visa standardsidan för Apache2 Ubuntu öppnar du en webbläsare och anger den virtuella datorns offentliga IP-adress. Använd den offentliga IP-adressen som du använde för SSH till den virtuella datorn:

![Apache-standardsida][3]


### <a name="mysql"></a>MySQL

Kontrollera versionen av MySQL med följande kommando (observera versal i parameter `V`):

```bash
mysql -V
```

Kör skriptet `mysql_secure_installation` för att skydda installationen av MySQL. Om du bara konfigurerar en tillfällig server kan du hoppa över det här steget.

```bash
mysql_secure_installation
```

Ange ett rotlösenord för MySQL och konfigurera säkerhetsinställningarna för miljön.

Om du vill prova MySQL-funktioner (skapa en MySQL-databas, lägga till användare eller ändra konfigurationsinställningar) loggar du in på MySQL. Det här steget krävs inte för att genomföra kursen.

```bash
mysql -u root -p
```

När du är klar lämnar du mysql-frågan genom att skriva `\q`.

### <a name="php"></a>PHP

Kontrollera versionen av PHP med följande kommando:

```bash
php -v
```

Om du vill testa ytterligare skapar du en snabb PHP-informationssida som visas i en webbläsare. Följande kommando skapar PHP-informationssidan:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Nu kan du kontrollera PHP-informationssidan som du har skapat. Öppna en webbläsare och navigera till `http://yourPublicIPAddress/info.php`. Ersätt den offentliga IP-adressen för den virtuella datorn. Det bör se ut ungefär som den här bilden:

![PHP-informationssida][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Nästa steg

Under den här kursen distribuerade du en LAMP-server i Azure. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator
> * Öppna port 80 för webbtrafik
> * Installera Apache, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på LAMP-servern

Gå vidare till nästa självstudiekurs där du får lära dig att skydda webbservrar med SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker webbserver med SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png