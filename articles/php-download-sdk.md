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
ms.author: yaya
ms.openlocfilehash: 9dd336ef973b603108aad62c90e4dab385d20317
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67873884"
---
# <a name="download-the-azure-sdk-for-php"></a>Ladda ner Azure SDK för PHP

## <a name="overview"></a>Översikt

Azure SDK för PHP innehåller komponenter som gör att du kan utveckla, distribuera och hantera PHP-program för Azure. Mer specifikt innehåller Azure SDK för PHP följande:

* **Php-klientens bibliotek för Azure**. Dessa klass bibliotek tillhandahåller ett gränssnitt för åtkomst till Azure-funktioner, till exempel data hanterings tjänster och moln tjänster.
* **Azures kommando rads gränssnitt för Mac, Linux och Windows (Azure CLI)** . Detta är en uppsättning kommandon för att distribuera och hantera Azure-tjänster, till exempel Azure Websites och Azure Virtual Machines. Azure CLI fungerar på alla plattformar, inklusive Mac, Linux och Windows.
* **Azure PowerShell (endast Windows)** . Detta är en uppsättning PowerShell-cmdletar för att distribuera och hantera Azure-tjänster, till exempel Cloud Services och Virtual Machines.
* **Azure-emulatorer (endast Windows)** . Beräknings-och lagrings emulatorn är lokala emulatorer för moln tjänster och data hanterings tjänster som gör att du kan testa ett program lokalt. Azure-emulatorer körs endast på Windows.

I avsnitten nedan beskrivs hur du hämtar och installerar de komponenter som beskrivs ovan.

Anvisningarna i det här avsnittet förutsätter att du har installerat [php][install-php] .

> [!NOTE]
> Du måste ha PHP 5,5 eller högre för att kunna använda PHP-klientprogrammet för Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>PHP-klientbibliotek för Azure

PHP-klientprogrammet för Azure tillhandahåller ett gränssnitt för åtkomst till Azure-funktioner, till exempel data hanterings tjänster och moln tjänster, från alla operativ system. Dessa bibliotek kan installeras via Composer.

Information om hur du använder PHP-klientcertifikat för Azure finns i så här [använder du Blob-tjänsten][blob-service], [hur du använder tabell tjänsten][table-service] och [hur du använder Queue Service][queue-service].

### <a name="install-via-composer"></a>Installera via Composer

1. [Installera Git][install-git]. I Windows måste du också lägga till den git-körbara filen i din PATH-miljö variabel.

2. Skapa en fil med namnet **Compose. JSON** i roten i projektet och Lägg till följande kod i den:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Hämta **[Composer. Phar][composer-phar]** i projekt roten.

4. Öppna en kommando tolk och kör det i projekt roten

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell och Azure-emulatorer

Azure PowerShell är en uppsättning PowerShell-cmdletar för att distribuera och hantera Azure-tjänster (till exempel Cloud Services och Virtual Machines). Azure-emulatorer är emulatorer för moln tjänster och data hanterings tjänster som gör att du kan testa ett program lokalt. Dessa komponenter stöds endast i Windows.

Det rekommenderade sättet att installera Azure PowerShell och Azure-emulatorer är att använda [installations programmet för Microsoft Web Platform][download-wpi]. Observera att du även kan välja att installera andra utvecklings komponenter, till exempel PHP, SQL Server, Microsoft-drivrutinerna för SQL Server för PHP och WebMatrix.

Information om hur du använder Azure PowerShell finns i [så här använder du Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

Azure CLI är en uppsättning kommandon för att distribuera och hantera Azure-tjänster, till exempel Azure Websites och Azure Virtual Machines. Information om hur du installerar Azure CLI finns i [Installera Azure CLI](cli-install-nodejs.md).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [php Developer Center](https://azure.microsoft.com/develop/php/).

[install-php]: https://www.php.net/manual/en/install.php
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
