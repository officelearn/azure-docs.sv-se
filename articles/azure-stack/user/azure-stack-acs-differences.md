---
title: "Azure Storage för stacken: Skillnader och överväganden"
description: "Förstå skillnaderna mellan Azure Stack Storage och Azure Storage, tillsammans med överväganden vid distribution av Azure-stacken."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2017
ms.author: jeffgilb
ms.openlocfilehash: 1dc099fa234e217b682c88f2214fe271c916eec2
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Storage för stacken: Skillnader och överväganden

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure-stacken Storage är en uppsättning lagring molntjänster i Microsoft Azure-stacken. Azure-stacken Storage tillhandahåller blob, tabell, kö och hanteringsfunktioner för konto med Azure-konsekvent semantik.

Den här artikeln sammanfattar kända Azure-stacken Storage skillnaderna från Azure Storage. Det sammanfattar också andra överväganden att tänka på när du distribuerar Azure stacken. Mer information om övergripande skillnader mellan Azure-stacken och Azure, finns det [nyckeln överväganden](azure-stack-considerations.md) avsnittet.

## <a name="cheat-sheet-storage-differences"></a>Cheat blad: lagring skillnader

| Funktion | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Molnbaserade SMB-filresurser som stöds|Stöds inte än
|Azure Storage-tjänstens kryptering av vilande data|256-bitars AES-kryptering|Stöds inte än
|Storage Account-typ|Allmänna och Azure Blob storage-konton|Allmänna endast
|Alternativ för datareplikering|Lokalt redundant lagring, geo-redundant lagring, geo-redundant lagring med läsbehörighet och zonredundant lagring|Lokalt redundant lagring
|Premium Storage|Fullt stöd|Kan etableras, men det finns ingen gräns för prestanda eller garantera
|Hanterade diskar|Premium- och standard som stöds|Stöds inte än
|Blobbnamnet|1 024 tecken (2 048 byte)|880 tecken (1,760 byte)
|Max storlek för block-blob|4,75 TB (100 MB × 50 000 block)|50 000 x 4 MB (uppskattat 195 GB)
|Sidan blob ögonblicksbild kopia|Säkerhetskopiering Azure ohanterade Virtuella diskar som är anslutna till en aktiv virtuell dator stöds|Stöds inte än
|Sidan blob inkrementell ögonblicksbild kopia|Premium- och standard Azure sidblobbar som stöds|Stöds inte än
|Sidan blob maxstorlek|8 TB|1 TB
|Sidstorleken för blob-sida|512 byte|4 KB
|Tabell partitionsnyckel och rad nyckelstorlek|1 024 tecken (2 048 byte)|400 tecken (800 byte)

### <a name="metrics"></a>Mått
Det finns vissa skillnader i jämförelse med storage-mätvärden:
* Transaktionsdata i storage-mätvärden görs ingen åtskillnad interna eller externa nätverksbandbredd.
* Transaktionsdata i storage-mätvärden inkluderar inte åtkomst till de monterade diskarna virtuella datorn.

## <a name="api-version"></a>API-version
Följande versioner stöds med Azure Storage för Stack:

* Azure Storage data services: [2015-04-05 REST API-version](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Azure Storage management-tjänster: [2015-05-01-preview 2015-06-15 och 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure-stacken Storage utvecklingsverktyg](azure-stack-storage-dev.md)
* [Introduktion till Azure-stacken Storage](azure-stack-storage-overview.md)

