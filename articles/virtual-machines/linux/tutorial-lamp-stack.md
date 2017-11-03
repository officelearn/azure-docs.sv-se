---
title: "Distribuera LYKTA på en virtuell Linux-dator i Azure | Microsoft Docs"
description: "Självstudiekurs – installera LYKTA stacken på en Linux-VM i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: c00e6a190633348411f47490808739d570cafd69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Installera en LYKTA webbserver på en Azure VM
Den här artikeln får du veta hur du distribuerar en Apache-webbservern, MySQL och PHP (LYKTA stack) på en Ubuntu VM i Azure. Om du föredrar den NGINX-servern finns i [LEMP stack](tutorial-lemp-stack.md) kursen. Om du vill se LYKTA servern i praktiken du om du vill installera och konfigurera en WordPress-webbplats. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Ubuntu VM (den ”L” i LYKTA stack)
> * Öppna port 80 för webbtrafik
> * Installera Apache, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på servern LYKTA


Mer information om LYKTA-stacken, inklusive rekommendationer för en produktionsmiljö, finns det [Ubuntu dokumentationen](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Installera Apache, MySQL och PHP

Kör följande kommando för att uppdatera Ubuntu paketet källor och installera Apache, MySQL och PHP. Observera hatt (^) i slutet av kommandot.


```bash
sudo apt update && sudo apt install lamp-server^
```



Du uppmanas att installera paket och andra beroenden. När du uppmanas, anger du ett rotlösenord för MySQL, och sedan [Retur] för att fortsätta. Följ anvisningarna för återstående. Den här processen installerar de minsta nödvändiga PHP-tillägg för att kunna använda PHP med MySQL. 

![MySQL-rot lösenordssidan][1]

## <a name="verify-installation-and-configuration"></a>Verifiera installation och konfiguration


### <a name="apache"></a>Apache

Kontrollera vilken version av Apache med följande kommando:
```bash
apache2 -v
```

Med Apache installerad, och port 80 är öppen för den virtuella datorn, kan du nu åt webbservern från internet. Öppna en webbläsare för att visa sidan Apache2 Ubuntu-standard, och ange offentliga IP-adressen för den virtuella datorn. Använd den offentliga IP-adressen som du använde för att SSH till den virtuella datorn:

![Apache standardsida][3]


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

Om du vill testa ytterligare skapar du en snabb PHP info sida för att visa i en webbläsare. Följande kommando skapar sidan PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Nu kan du kontrollera PHP Infosidan som du skapat. Öppna en webbläsare och gå till `http://yourPublicIPAddress/info.php`. Ersätt offentliga IP-adressen för den virtuella datorn. Det bör likna den här avbildningen.

![Information om PHP-sida][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Nästa steg

I kursen får distribuerat du en LYKTA server i Azure. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator
> * Öppna port 80 för webbtrafik
> * Installera Apache, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på servern LYKTA

Gå vidare till nästa kurs att lära dig säker webbserver med SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker webbserver med SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png