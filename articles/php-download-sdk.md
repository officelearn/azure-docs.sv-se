---
title: Ladda ner Azure SDK för PHP
description: Lär dig hur du hämtar och installerar Azure SDK för PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: cfcf908145e8a384782953e045f9e10fd3c0e8f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639477"
---
# <a name="download-the-azure-sdk-for-php"></a>Ladda ner Azure SDK för PHP

## <a name="overview"></a>Översikt

Azure SDK för PHP innehåller komponenter som gör det möjligt att utveckla, distribuera och hantera PHP-program för Azure. Mer specifikt omfattar Azure SDK för PHP följande:

* **PHP-klientbibliotek för Azure**. Dessa klassbibliotek tillhandahåller ett gränssnitt för att komma åt Azure funktioner, till exempel datatjänster och molntjänster.
* **Azure-kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)**. Det här är en uppsättning kommandon för att distribuera och hantera Azure-tjänster, till exempel Azure Websites och Azure Virtual Machines. Azure CLI fungerar på alla plattformar, inklusive Mac, Linux och Windows.
* **Azure PowerShell (Windows)**. Det här är en uppsättning PowerShell-cmdlets för att distribuera och hantera Azure-tjänster, till exempel molntjänster och virtuella datorer.
* **(Endast Windows) Azure Emulatorerna**. Emulatorerna beräkning och lagring är lokala emulatorerna för molntjänster och tjänster för data som gör att du testar ett program lokalt. Azure-Emulatorerna körs bara i Windows.

I avsnitten nedan beskrivs hur du hämtar och installerar de komponenter som beskrivs ovan.

Anvisningarna i det här avsnittet förutsätter att du har [PHP] [ install-php] installerad.

> [!NOTE]
> Du måste ha PHP 5.5 eller högre för att använda PHP-klientbibliotek för Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>PHP-klientbibliotek för Azure

PHP-klientbibliotek för Azure tillhandahåller ett gränssnitt för att komma åt Azure funktioner, till exempel datatjänster och molntjänster, från alla operativsystem. Dessa bibliotek kan installeras via Composer.

Information om hur du använder PHP-klientbibliotek för Azure finns [hur du använder Blob-tjänsten][blob-service], [använda Tabelltjänsten] [ table-service] och [använda kötjänsten][queue-service].

### <a name="install-via-composer"></a>Installera via Composer

1. [Installera Git][install-git]. I Windows behöver du också lägga till Git körbara i PATH-miljövariabeln.

2. Skapa en fil med namnet **composer.json** i roten av projektet och Lägg till följande kod:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Hämta **[composer.phar] [ composer-phar]** i projektroten.

4. Öppna en kommandotolk och kör det i projektroten

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell och Azure Emulatorerna

Azure PowerShell är en uppsättning PowerShell-cmdlets för att distribuera och hantera Azure-tjänster (till exempel molntjänster och virtuella datorer). Azure-Emulatorerna är emulatorerna för molntjänster och tjänster för data som gör att du testar ett program lokalt. Dessa komponenter stöds endast Windows.

Det rekommenderade sättet att installera Azure PowerShell och Azure Emulatorerna är att använda den [Microsoft Web Platform Installer][download-wpi]. Observera att du kan också välja att installera andra utvecklingskomponenter, till exempel PHP, SQL Server, Drivers Microsoft för SQL Server för PHP och WebMatrix.

Information om hur du använder Azure PowerShell finns i [hur du använder Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Azure CLI är en uppsättning kommandon för att distribuera och hantera Azure-tjänster, till exempel Azure Websites och Azure Virtual Machines. Information om hur du installerar Azure CLI finns [installerar Azure CLI](cli-install-nodejs.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
