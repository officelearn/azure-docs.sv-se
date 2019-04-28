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
ms.openlocfilehash: 4f2c242e27d8a0ed6687eb4a3510bbce6e07694f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457642"
---
# <a name="download-the-azure-sdk-for-php"></a>Ladda ner Azure SDK för PHP

## <a name="overview"></a>Översikt

Azure SDK för PHP innehåller komponenter som gör att du kan utveckla, distribuera och hantera PHP-program för Azure. Mer specifikt omfattar Azure SDK för PHP följande:

* **PHP-klientbiblioteken för Azure**. Dessa klassbibliotek tillhandahåller ett gränssnitt för åtkomst till Azure-funktioner, till exempel datahanteringstjänster och molntjänster.
* **Azure kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)**. Det här är en uppsättning kommandon för att distribuera och hantera Azure-tjänster, till exempel Azure Websites och Azure Virtual Machines. Azure CLI-arbete på alla plattformar, inklusive Mac, Linux och Windows.
* **Azure PowerShell (endast Windows)**. Det här är en uppsättning PowerShell-cmdletar för att distribuera och hantera Azure-tjänster, till exempel molntjänster och virtuella datorer.
* **Azure-emulatorer (endast Windows)**. Beräknings- och emulatorer är lokala emulatorer molntjänster och datahanteringstjänster så att du kan testa ett program lokalt. Azure-emulatorer Kör endast på Windows.

I avsnitten nedan beskrivs hur du hämtar och installerar de komponenter som beskrivs ovan.

Anvisningarna i det här avsnittet förutsätter att du har [PHP] [ install-php] installerad.

> [!NOTE]
> Du måste ha PHP 5.5 eller senare för att använda PHP-klientbiblioteken för Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>PHP-klientbibliotek för Azure

PHP-Klientbiblioteken för Azure tillhandahåller ett gränssnitt för åtkomst till Azure-funktioner, till exempel datahanteringstjänster och molntjänster från alla operativsystem. Biblioteken kan installeras via Composer.

Information om hur du använder PHP-Klientbiblioteken för Azure finns i [hur du använder Blob-tjänsten][blob-service], [hur du använder Tabelltjänsten] [ table-service]och [hur du använder kötjänsten][queue-service].

### <a name="install-via-composer"></a>Installera via Composer

1. [Installera Git][install-git]. På Windows måste du även att lägga till Git körbara i miljövariabeln PATH.

2. Skapa en fil med namnet **composer.json** i roten av projektet och Lägg till följande kod:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Ladda ned **[composer.phar] [ composer-phar]** i projektroten.

4. Öppna en kommandotolk och kör det i projektroten

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell och Azure-emulatorer

Azure PowerShell är en uppsättning PowerShell-cmdletar för att distribuera och hantera Azure-tjänster (till exempel molntjänster och virtuella datorer). Azure-emulatorer är emulatorer molntjänster och datahanteringstjänster så att du kan testa ett program lokalt. De här komponenterna stöds bara Windows.

Det rekommenderade sättet att installera Azure PowerShell och Azure-emulatorer är att använda den [Microsoft Web Platform Installer][download-wpi]. Observera att du kan också välja att installera andra utvecklingskomponenter, till exempel PHP, SQL Server, Microsoft-Drivers för SQL Server för PHP och WebMatrix.

Information om hur du använder Azure PowerShell finns i [hur du använder Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Azure CLI är en uppsättning kommandon för att distribuera och hantera Azure-tjänster, till exempel Azure Websites och Azure Virtual Machines. Information om hur du installerar Azure CLI finns i [installera Azure CLI](cli-install-nodejs.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i den [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
