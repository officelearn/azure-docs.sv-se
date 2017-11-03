---
title: "Distribuera LEMP på en Linux-dator i Azure | Microsoft Docs"
description: "Självstudiekurs – installera LEMP stacken på en Linux-VM i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: 87d60ae51aaa33b709d272605419fd85eeb5d93d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lemp-web-server-on-an-azure-vm"></a>Installera en LEMP webbserver på en Azure VM
Den här artikeln får du veta hur du distribuerar en NGINX-webbservern, MySQL och PHP (LEMP stack) på en Ubuntu VM i Azure. LEMP-stacken är ett alternativ till att den populära [LYKTA stack](tutorial-lamp-stack.md), där du kan också installera i Azure. Om du vill se LEMP servern i praktiken du om du vill installera och konfigurera en WordPress-webbplats. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Ubuntu VM (den ”L” i LEMP stack)
> * Öppna port 80 för webbtrafik
> * Installera NGINX, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på servern LEMP


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Installera NGINX, MySQL och PHP

Kör följande kommando för att uppdatera Ubuntu paketet källor och installera NGINX, MySQL och PHP. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

Du uppmanas att installera paket och andra beroenden. När du uppmanas, anger du ett rotlösenord för MySQL, och sedan [Retur] för att fortsätta. Följ anvisningarna för återstående. Den här processen installerar de minsta nödvändiga PHP-tillägg för att kunna använda PHP med MySQL. 

![MySQL-rot lösenordssidan][1]

## <a name="verify-installation-and-configuration"></a>Verifiera installation och konfiguration


### <a name="nginx"></a>NGINX

Kontrollera vilken version av NGINX med följande kommando:
```bash
nginx -v
```

Med NGINX installerat, och port 80 är öppen för den virtuella datorn, kan du nu åt webbservern från internet. Om du vill visa NGINX välkomstsidan, öppna en webbläsare och ange offentliga IP-adressen för den virtuella datorn. Använd den offentliga IP-adressen som du använde för att SSH till den virtuella datorn:

![NGINX standardsida][3]


### <a name="mysql"></a>MySQL

Kontrollera vilken version av MySQL med följande kommando (Observera kapital `V` parametern):

```bash
mysql -V
```

Vi rekommenderar att du kör följande skript om du vill skydda installationen av MySQL:

```bash
mysql_secure_installation
```

Ange lösenordet för roten MySQL och konfigurera säkerhetsinställningarna för din miljö.

Om du vill skapa en MySQL-databas, lägga till användare eller ändra konfigurationsinställningarna, logga in på MySQL:

```bash
mysql -u root -p
```

När du är klar avsluta mysql-Kommandotolken genom att skriva `\q`.

### <a name="php"></a>PHP

Kontrollera versionen av PHP med följande kommando:

```bash
php -v
```

Konfigurera NGINX om du vill använda PHP FastCGI Process Manager (PHP-: FPM). Kör följande kommandon för att säkerhetskopiera den ursprungliga NGINX server block config-fil och sedan redigera den ursprungliga filen i ett redigeringsprogram:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

I redigeraren, ersätter du innehållet i `/etc/nginx/sites-available/default` med följande. Visa kommentarerna förklaring av inställningarna. Ersätt offentliga IP-adressen för den virtuella datorn för *yourPublicIPAddress*, och lämna de återstående inställningarna. Spara sedan filen.

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
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

Kontrollera NGINX-konfigurationen för Syntaxfel:

```bash
sudo nginx -t
```

Om syntax är korrekt kan du starta om NGINX med följande kommando:

```bash
sudo service nginx restart
```

Om du vill testa ytterligare skapar du en snabb PHP info sida för att visa i en webbläsare. Följande kommando skapar sidan PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Nu kan du kontrollera PHP Infosidan som du skapat. Öppna en webbläsare och gå till `http://yourPublicIPAddress/info.php`. Ersätt offentliga IP-adressen för den virtuella datorn. Det bör likna den här avbildningen.

![Information om PHP-sida][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Nästa steg

I kursen får du har distribuerat en LEMP server i Azure. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator
> * Öppna port 80 för webbtrafik
> * Installera NGINX, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress LEMP-stacken

Gå vidare till nästa kurs att lära dig säker webbserver med SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker webbserver med SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
