---
title: "Använda CustomScript-tillägg på en virtuell Linux-dator | Microsoft Docs"
description: "Lär dig hur du använder CustomScript-tillägg för att distribuera program på Linux-datorer i Azure som skapats med hjälp av den klassiska distributionsmodellen."
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: guybo
ms.openlocfilehash: cb1fc9a44dc9e57d9cc9f1c546ad937d67e63c2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Distribuera ett LAMP-program med Azure CustomScript-tillägget för Linux
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om hur du distribuerar en LYKTA stack med hjälp av Resource Manager-modellen finns [här](../tutorial-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Microsoft Azure CustomScript-tillägg för Linux är ett sätt att anpassa dina virtuella datorer (VM) genom att köra skadlig kod som skrivs i valfritt skriptspråk som stöds av den virtuella datorn (till exempel Python och Bash). Detta ger ett mycket flexibelt sätt att automatisera distribution till flera datorer.

Du kan distribuera CustomScript-tillägg som använder Azure portal, Windows PowerShell eller Azure-kommandoradsgränssnittet (Azure CLI).

I den här artikeln använder vi skapade Azure CLI för att distribuera ett enkelt LYKTA program för en Ubuntu VM med hjälp av den klassiska distributionsmodellen.

## <a name="prerequisites"></a>Krav
För det här exemplet skapar du två virtuella Azure-datorer kör Ubuntu 14.04 eller senare. De virtuella datorerna kallas *skript-vm* och *lykta vm*. Använd ett unikt namn när du skapar de virtuella datorerna. En används för att köra CLI-kommandon och en används för att distribuera LYKTA appen.

Du måste också ett Azure Storage-konto och en nyckel för att komma åt den (du kan hämta det från Azure portal).

Om du behöver hjälp med att skapa virtuella Linux-datorer i Azure Se [skapa en virtuell dator kör Linux](createportal.md).

Installera-kommandon förutsätter Ubuntu, men du kan anpassa installationen för alla stöds Linux distro.

Skript-vm VM måste ha Azure CLI som installerats med en fungerande anslutning till Azure. För hjälp med det här avser [installera och konfigurera Azure-kommandoradsgränssnittet](../../../cli-install-nodejs.md).

## <a name="upload-a-script"></a>Ladda upp ett skript
Vi använder CustomScript-tillägg för att köra ett skript på en fjärransluten VM att installera LYKTA stacken och skapa en PHP-sida. För att komma åt skriptet från valfri plats ska vi skicka den som en Azure blob.

### <a name="script-overview"></a>Översikt över skript
Exempel på skript installerar en LYKTA stack till Ubuntu (inklusive hur du konfigurerar en tyst installation av MySQL), skriver en enkel PHP-fil och startar Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Överför skript
Spara skriptet som en textfil, till exempel *install_lamp.sh*, och överföra den till Azure Storage. Du kan göra det enkelt med Azure CLI. I följande exempel överför filen till en lagringsbehållare med namnet ”skript”. Du behöver skapa först om behållaren inte finns.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Även skapa en JSON-fil som beskriver hur du hämtar skriptet från Azure Storage. Spara den som *public_config.json* (i stället för ”mystorage” med namnet på ditt lagringskonto):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Distribuera tillägget
Nu kan du använda nästa kommando för att distribuera Linux CustomScript-tillägg till den fjärranslutna virtuella datorn med hjälp av Azure CLI.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

Föregående kommando hämtar och kör den *install_lamp.sh* skript på den virtuella datorn kallas *lykta vm*.

Eftersom appen innehåller en webbserver, Kom ihåg att öppna en lyssnande HTTP-porten på den fjärranslutna virtuella datorn med nästa kommando.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Övervakning och felsökning
Du kan kontrollera hur väl anpassat skript körs genom att titta i loggfilen på den fjärranslutna virtuella datorn. SSH till *lykta vm* och stjärt loggfilen med nästa kommando.

    cd /var/log/azure/customscript
    tail -f handler.log

När du har kört CustomScript-tillägg kan du bläddra till PHP-sidan som du skapade för information. PHP-sidan till exempel i den här artikeln är *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Ytterligare resurser
Du kan använda samma grundläggande steg för att distribuera mer komplexa appar. I det här exemplet sparades installationsskriptet som en offentlig blob i Azure Storage. Ett säkrare alternativ är att lagra installationsskriptet som en säker blob med en [säker Åtkomstsignatur](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Ytterligare resurser för Azure CLI, Linux och CustomScript-tillägg visas bredvid.

[Automatisera anpassningsuppgifter i virtuella Linux-datorer med CustomScript-tillägg](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux-tillägg (GitHub)](https://github.com/Azure/azure-linux-extensions)