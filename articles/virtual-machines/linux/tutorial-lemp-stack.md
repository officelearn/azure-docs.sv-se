---
title: Självstudier – Distribuera LEMP på en virtuell Linux-dator i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du installerar LEMP-stacken på en virtuell Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 0a9d63f4064952adbfedfc3f9656370ef7c4a1cc
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511285"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Självstudier: Installera en LEMP-webbserver på en virtuell Linux-dator i Azure

I den här artikeln får du veta hur du distribuerar en NGINX-webbserver, MySQL och PHP (LEMP-stacken) på en virtuell Ubuntu-dator i Azure. LEMP-stacken är ett alternativ till den populära [LAMP-stacken](tutorial-lamp-stack.md) som du också kan installera i Azure. Om du vill se LEMP-servern i praktiken kan du installera och konfigurera en WordPress-webbplats. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator (L:et i LEMP-stacken)
> * Öppna port 80 för webbtrafik
> * Installera NGINX, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på LEMP-servern

Den här installationen är avsedd för snabbtester och konceptbevis.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Installera NGINX, MySQL och PHP

Kör följande kommandon för att uppdatera Ubuntu-paketkällorna och installera NGINX, MySQL och PHP. 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

Du uppmanas att installera paketen och andra beroenden. Den här processen installerar lägsta nödvändiga PHP-tillägg för användning av PHP med MySQL.  

## <a name="verify-installation-and-configuration"></a>Verifiera installation och konfiguration


### <a name="verify-nginx"></a>Verifiera NGINX

Kontrollera version av NGINX med följande kommando:
```bash
nginx -v
```

När NGINX är installerat och port 80 är öppen för den virtuella datorn kan webbservern nås från internet. När du vill visa NGINX-startsidan öppnar du en webbläsare och anger den virtuella datorns offentliga IP-adress. Använd den offentliga IP-adressen som du använde för SSH till den virtuella datorn:

![NGINX-standardsida][3]


### <a name="verify-and-secure-mysql"></a>Verifiera och skydda MySQL

Kontrollera versionen av MySQL med följande kommando (observera versal i parameter `V`):

```bash
mysql -V
```

Om du vill skydda installationen av MySQL, inklusive att ange ett rotlösenord, kör du skriptet `mysql_secure_installation`. 

```bash
sudo mysql_secure_installation
```

Om du vill kan du även konfigurera plugin-programmet Validate Password (Verifiera lösenord; rekommenderas). Sedan anger du ett lösenord för MySQL-rotanvändaren och konfigurerar återstående säkerhetsinställningar för miljön. Vi rekommenderar att du svarar ”Y” (Ja) på alla frågor.

Om du vill prova MySQL-funktioner (skapa en MySQL-databas, lägga till användare eller ändra konfigurationsinställningar) loggar du in på MySQL. Det här steget krävs inte för att genomföra kursen. 


```bash
sudo mysql -u root -p
```

När du är klar lämnar du mysql-frågan genom att skriva `\q`.

### <a name="verify-php"></a>Verifiera PHP

Kontrollera versionen av PHP med följande kommando:

```bash
php -v
```

Konfigurera NGINX för att använda PHP FastCGI Process Manager (PHP-FPM). Kör följande kommandon för att säkerhetskopiera den ursprungliga NGINX-serverblockkonfigurationsfilen. Redigera sedan den ursprungliga filen i valfritt redigeringsprogram:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

I redigeringsprogrammet byter du ut innehållet i `/etc/nginx/sites-available/default` mot följande. Visa kommentarerna för att få en förklaring av inställningarna. Ersätt den offentliga IP-adressen för den virtuella datorn med *yourPublicIPAddress* (din offentliga IP-adress), bekräfta PHP-versionen i `fastcgi_pass` och låt de övriga inställningarna stå. Spara sedan filen.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

Sök i NGINX-konfigurationen efter syntaxfel:

```bash
sudo nginx -t
```

Om syntax stämmer startar du om NGINX med följande kommando:

```bash
sudo service nginx restart
```

Om du vill testa ytterligare skapar du en snabb PHP-informationssida som visas i en webbläsare. Följande kommando skapar PHP-informationssidan:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Nu kan du kontrollera PHP-informationssidan som du har skapat. Öppna en webbläsare och navigera till `http://yourPublicIPAddress/info.php`. Ersätt den offentliga IP-adressen för den virtuella datorn. Det bör se ut ungefär som den här bilden:

![PHP-informationssida][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Nästa steg

Under den här kursen distribuerade du en LEMP-server i Azure. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator
> * Öppna port 80 för webbtrafik
> * Installera NGINX, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på LEMP-stacken

Gå vidare till nästa självstudiekurs där du får lära dig att skydda webbservrar med SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker webbserver med SSL](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
