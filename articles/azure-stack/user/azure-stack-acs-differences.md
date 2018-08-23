---
title: Azure stack storage skillnader och överväganden | Microsoft Docs
description: Information om skillnaderna mellan Azure stack storage och Azure storage, tillsammans med överväganden vid distribution av Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.openlocfilehash: 6c0c42763ec3d124850555500c3a322073af2479
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139718"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure stack-lagring: skillnader och överväganden

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Azure stack-lagring är uppsättningen lagringstjänster för molnet i Microsoft Azure Stack. Azure stack storage tillhandahåller blob, tabell, kö och hanteringsfunktioner för konto med Azure-konsekventa semantik.

Den här artikeln sammanfattas de kända Azure Stack Storage skillnaderna från Azure Storage-tjänster. Här visas även saker att tänka på när du distribuerar Azure Stack. Läs om övergripande skillnader mellan globala Azure och Azure Stack i den [viktiga överväganden](azure-stack-considerations.md) avsnittet.

## <a name="cheat-sheet-storage-differences"></a>Lathund: Storage skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Molnbaserade SMB-filresurser som stöds|Stöds inte än
|Azure storage service encryption för vilande data|256-bitars AES-kryptering|BitLocker 128-bitars AES-kryptering
|Storage Account-typ|Allmänna och Azure blob storage-konton|Allmänna endast.
|Replikeringsalternativ|Lokalt redundant lagring, geo-redundant lagring, läsåtkomst till geografiskt redundant lagring och zonredundant lagring|Lokalt redundant lagring.
|Premium Storage|Stöds fullt ut|Kan tillhandahållas, men ingen prestandagräns eller garanterar.
|Hanterade diskar|Premium och standard som stöds|Stöds inte än.
|Blobnamn|1 024 tecken (2 048 byte)|880 tecken (1,760 byte)
|Maxstorlek för block blob|4,75 TB (100 MB X 50 000 block)|4,75 TB (100 MB x 50 000 block) för 1802 update eller senare version. 50 000 x 4 MB (cirka 195 GB) för tidigare versioner.
|Kopiering av sidan blob-ögonblicksbild|Säkerhetskopiering Azure ohanterade Virtuella diskar som är anslutna till en aktiv virtuell dator stöds|Stöds inte än.
|Sidan kopiering av blob inkrementell ögonblicksbild|Premium och standard Azure sidblobar som stöds|Stöds inte än.
|Lagringsnivåer för bloblagring|Frekvent, lågfrekvent och arkivlagringsnivå.|Stöds inte än.
Mjuk borttagning för blob storage|Förhandsversion|Stöds inte än.
|Maximal sidstorlek för blob|8 TB|1 TB
|Sidan blob sidstorlek|512 byte|4 KB
|Tabellen partition och radnyckel nyckelstorlek|1 024 tecken (2 048 byte)|400 tecken (800 byte)
|Blobögonblicksbilden|Det maximala antalet ögonblicksbilder av en blob är inte begränsat.|Det maximala antalet ögonblicksbilder av en blob är 1 000.|

Det finns också skillnader med mätvärden i storage:

* Transaktionsdata i lagringsmått görs ingen åtskillnad interna eller externa nätverksbandbredd.
* Transaktionsdata i mätvärden i storage inkluderar inte VM-åtkomst till de monterade diskarna.

## <a name="api-version"></a>API-version

Följande versioner stöds med Azure Stack-lagring:

Azure Storage services-API: er:

Uppdatera 1802 eller senare:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Tidigare versioner:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure Storage Service management API: er:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>SDK-versioner

Azure Stack-storage stöder följande klientbibliotek:

| Klientbibliotek | Azure Stack-version som stöds | Länk                                                                                                                                                                                                                                                                                                                                     | Slutpunkt-specifikation       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Från 6.2.0 till 8.7.0.          | Nuget-paketet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | filen App.config              |
| Java           | Från 4.1.0 till 6.1.0           | Maven-paketet:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Anslutningsinställningar för sträng      |
| Node.js        | Från 1.1.0 till 2.7.0           | NPM-länk:<br>https://www.npmjs.com/package/azure-storage<br>(Till exempel: kör ”npm-installationsprogrammet azure-storage@2.7.0”)<br> <br>Github-version:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Instans tjänstedeklaration |
| C++            | Från 2.4.0 till 3.1.0           | Nuget-paketet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Anslutningsinställningar för sträng      |
| PHP            | Från 0.15.0 till 1.0.0          | GitHub-version:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Installera via Composer (se detaljer nedan)                                                                                                                                                                                                                  | Anslutningsinställningar för sträng      |
| Python         | Från 0.30.0 till 1.0.0          | GitHub-version:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Instans tjänstedeklaration |
| Ruby           | Från 0.12.1 till 1.0.1          | RubyGems paket:<br>Vanliga:<br>https://rubygems.org/gems/azure-storage-common/<br>BLOB: https://rubygems.org/gems/azure-storage-blob/<br>Kö: https://rubygems.org/gems/azure-storage-queue/<br>Tabell: https://rubygems.org/gems/azure-storage-table/<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-ruby/releases | Anslutningsinställningar för sträng      |

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Stack verktyg för lagringsutveckling](azure-stack-storage-dev.md)
* [Introduktion till Azure Stack-lagring](azure-stack-storage-overview.md)
