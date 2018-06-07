---
title: Azure-stacken lagring skillnader och överväganden | Microsoft Docs
description: Förstå skillnaderna mellan Azure stack lagring och Azure storage, tillsammans med överväganden vid distribution av Azure-stacken.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviwer: xiaofmao
ms.openlocfilehash: 2a6cb3f1a1f8009af411ba4d97a23194f6f089ae
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604468"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Stacken för Azure storage: skillnader och överväganden

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure-stacken storage är en uppsättning lagring molntjänster i Microsoft Azure-stacken. Azure-stacken lagring ger blob, tabell, kö och hanteringsfunktioner för konto med Azure-konsekvent semantik.

Den här artikeln sammanfattar kända Azure-stacken Storage skillnaderna från Azure Storage-tjänster. Dessutom visas saker att tänka på när du distribuerar Azure stacken. Läs om övergripande skillnader mellan globala Azure och Azure-stacken i den [nyckeln överväganden](azure-stack-considerations.md) avsnittet.

## <a name="cheat-sheet-storage-differences"></a>Cheat blad: lagring skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Molnbaserade SMB-filresurser som stöds|Stöds inte än
|Azure storage service-kryptering för data i vila|256-bitars AES-kryptering|BitLocker 128-bitars AES-kryptering
|Storage Account-typ|Allmänna och Azure blob storage-konton|Allmänna endast.
|Alternativ för datareplikering|Lokalt redundant lagring, geo-redundant lagring, geo-redundant lagring med läsbehörighet och zonredundant lagring|Lokalt redundant lagring.
|Premium Storage|Fullt stöd|Kan etableras, men ingen gräns för prestanda eller säkerhet.
|Hanterade diskar|Premium- och standard som stöds|Stöds inte än.
|Blobnamn|1 024 tecken (2 048 byte)|880 tecken (1,760 byte)
|Max storlek för block-blob|4,75 TB (100 MB × 50 000 block)|4,75 TB (100 MB × 50 000 block) för den 1802 uppdateringen eller en senare version. 50 000 x 4 MB (uppskattat 195 GB) för tidigare versioner.
|Sidan blob ögonblicksbild kopia|Säkerhetskopiering Azure ohanterade Virtuella diskar som är anslutna till en aktiv virtuell dator stöds|Stöds inte än.
|Sidan blob inkrementell ögonblicksbild kopia|Premium- och standard Azure sidblobbar som stöds|Stöds inte än.
|Lagringsnivåer för bloblagring|Hot, kall och arkivera lagringsnivåer.|Stöds inte än.
Mjuk borttagning för blob-lagring|Förhandsversion|Stöds inte än.
|Sidan blob maxstorlek|8 TB|1 TB
|Sidstorleken för blob-sida|512 byte|4 KB
|Tabell partitionsnyckel och rad nyckelstorlek|1 024 tecken (2 048 byte)|400 tecken (800 byte)
|BLOB-ögonblicksbild|Max antal ögonblicksbilder av en blob är begränsad.|Max antal ögonblicksbilder av en blob är 1 000.|

Det finns också skillnader i jämförelse med storage-mätvärden:

* Transaktionsdata i storage-mätvärden görs ingen åtskillnad interna eller externa nätverksbandbredd.
* Transaktionsdata i storage-mätvärden inkluderar inte åtkomst till de monterade diskarna virtuella datorn.

## <a name="api-version"></a>API-version

Följande versioner stöds med Azure Storage för Stack:

Azure Storage services API: er:

Uppdatera 1802 eller senare:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Tidigare versioner:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure Storage services management API: er:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>SDK-versioner

Azure Stack-lagring stöder följande klientbiblioteken:

| Klientbibliotek | Azure-stacken en version som stöds | Länk                                                                                                                                                                                                                                                                                                                                     | Specifikation för slutpunkten       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Från 6.2.0 till 8.7.0.          | Nuget-paketet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub-versionen:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | filen App.config              |
| Java           | Från 4.1.0 till 6.1.0           | Maven-paket:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub-versionen:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Anslutningsinställningar för sträng      |
| Node.js        | Från 1.1.0 till 2.7.0           | NPM länk:<br>https://www.npmjs.com/package/azure-storage<br>(Till exempel: kör ”installera npm azure-storage@2.7.0”)<br> <br>Github-versionen:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Instansen tjänstedeklaration |
| C++            | Från 2.4.0 till 3.1.0           | Nuget-paketet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub-versionen:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Anslutningsinställningar för sträng      |
| PHP            | Från 0.15.0 till 1.0.0          | GitHub-versionen:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Installera via Composer (Mer information finns nedan)                                                                                                                                                                                                                  | Anslutningsinställningar för sträng      |
| Python         | Från 0.30.0 till 1.0.0          | GitHub-versionen:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Instansen tjänstedeklaration |
| Ruby           | Från 0.12.1 till 1.0.1          | RubyGems paket:<br>Vanliga:<br>https://rubygems.org/gems/azure-storage-common/<br>BLOB: https://rubygems.org/gems/azure-storage-blob/<br>Kö: https://rubygems.org/gems/azure-storage-queue/<br>Tabell: https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub-versionen:<br>https://github.com/Azure/azure-storage-ruby/releases | Anslutningsinställningar för sträng      |

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure-stacken Storage utvecklingsverktyg](azure-stack-storage-dev.md)
* [Introduktion till Azure-stacken Storage](azure-stack-storage-overview.md)
