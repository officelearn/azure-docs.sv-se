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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 11736b978242416bcfb95d3025975028e4148e98
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486546"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack-lagring: Skillnader och överväganden

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack storage är en uppsättning lagringstjänster för molnet i Microsoft Azure Stack. Azure Stack-storage tillhandahåller blob, tabell, kö och hanteringsfunktioner för konto med Azure-konsekventa semantik.

Den här artikeln sammanfattas de kända Azure Stack Storage skillnaderna från Azure Storage-tjänster. Här visas även saker att tänka på när du distribuerar Azure Stack. Läs om övergripande skillnader mellan globala Azure och Azure Stack i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

## <a name="cheat-sheet-storage-differences"></a>Lathund: Storage-skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Molnbaserade SMB-filresurser som stöds|Stöds inte än
|Azure storage service encryption för vilande data|256-bitars AES-kryptering. Stöd för kryptering med Kundhanterade nycklar i Key Vault.|BitLocker 128-bitars AES-kryptering. Kryptering med Kundhanterade nycklar stöds inte.
|Storage Account-typ|General-Purpose V1, V2 och Blob storage-konton|General-Purpose V1.
|Replikeringsalternativ|Lokalt redundant lagring, geo-redundant lagring, läsåtkomst till geografiskt redundant lagring och zonredundant lagring|Lokalt redundant lagring.
|Premium Storage|Stöds fullt ut|Kan tillhandahållas, men ingen prestandagräns eller garanterar.
|Hanterade diskar|Premium och standard som stöds|Stöds när du använder version 1808 eller senare.
|Blobnamn|1 024 tecken (2 048 byte)|880 tecken (1,760 byte)
|Maxstorlek för block blob|4,75 TB (100 MB X 50 000 block)|4,75 TB (100 MB x 50 000 block) för 1802 update eller senare version. 50 000 x 4 MB (cirka 195 GB) för tidigare versioner.
|Kopiering av sidan blob-ögonblicksbild|Säkerhetskopiering Azure ohanterade Virtuella diskar som är anslutna till en aktiv virtuell dator stöds|Stöds inte än.
|Sidan kopiering av blob inkrementell ögonblicksbild|Premium och standard Azure sidblobar som stöds|Stöds inte än.
|Lagringsnivåer för bloblagring|Frekvent, lågfrekvent och arkivlagringsnivå.|Stöds inte än.
|Mjuk borttagning för blob storage|Allmänt tillgänglig|Stöds inte än.
|Maximal sidstorlek för blob|8 TB|1 TB
|Sidan blob sidstorlek|512 byte|4 KB
|Tabellen partition och radnyckel nyckelstorlek|1 024 tecken (2 048 byte)|400 tecken (800 byte)
|Blobögonblicksbilden|Det maximala antalet ögonblicksbilder av en blob är inte begränsat.|Det maximala antalet ögonblicksbilder av en blob är 1 000.
|Azure AD-autentisering för lagring|Förhandsversion|Stöds inte än.
|Oföränderlig Blobar|Allmänt tillgänglig|Stöds inte än.
|Brandvägg och virtuella Nätverksregler för lagring|Allmänt tillgänglig|Stöds inte än.|

Det finns också skillnader med mätvärden i storage:

* Transaktionsdata i lagringsmått görs ingen åtskillnad interna eller externa nätverksbandbredd.
* Transaktionsdata i mätvärden i storage inkluderar inte VM-åtkomst till de monterade diskarna.

## <a name="api-version"></a>API-version

Följande versioner stöds med Azure Stack-lagring:

Azure Storage services-API: er:

1811 uppdatering eller nyare versioner:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Tidigare versioner:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure Storage Service management API: er:

1811 uppdatering eller nyare versioner:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Tidigare versioner:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Mer information om Azure Stack stöds lagringsklientbiblioteken finns: [Kom igång med Azure Stack verktyg för lagringsutveckling](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Stack verktyg för lagringsutveckling](azure-stack-storage-dev.md)
* [Använd verktyg för överföring av data för Azure Stack-lagring](azure-stack-storage-transfer.md)
* [Introduktion till Azure Stack-lagring](azure-stack-storage-overview.md)
