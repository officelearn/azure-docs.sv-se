---
title: Microsoft Azure Data Box Disk – översikt| Microsoft Docs i data
description: Beskriver Azure Data Box Disk, en molnlösning som gör det möjligt att överföra stora mängder data till Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/04/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: b1beb0e9a5a0435bdf298eddbefc230b2f95ed0a
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698510"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Vad är Azure Data Box Disk? (Förhandsversion)

Med Microsoft Azure Data Box Disk-lösningen kan du skicka terabyte med lokala data till Azure på ett snabbt, kostnadseffektivt och tillförlitligt sätt. Den säkra dataöverföringen görs snabbare eftersom 1 till 5 SSD-diskar (solid state-hårddisk) levereras till dig. Dessa krypterade diskar på 8 TB skickas till ditt datacenter via regionalt flyg. 

Du kan snabbt konfigurera, ansluta och låsa upp diskarna via Data Box-tjänsten i Azure-portalen. Kopiera dina data till diskar och skicka tillbaka diskarna till Azure. I Azure-datacentret laddas dina data automatiskt upp från enheter till molnet med en snabb, privat nätverksöverföringslänk.


> [!IMPORTANT]
> - Data Box Disk är tillgänglig som en förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen. 
> - Du behöver registrera dig för den här tjänsten. Om du vill registrera dig går du till [Preview-portalen](http://aka.ms/azuredataboxfromdiskdocs).
> - Under förhandsversionen kan Data Box Disk levereras till kunder i USA och EU. Mer information finns i [Regional tillgänglighet](#region-availability).

## <a name="use-cases"></a>Användningsfall

Använd Data Box Disk för att överföra flera TB med data i scenarier med ingen eller begränsad nätverksanslutning. Dataflytten kan vara enstaka, periodisk eller en inledande massdataöverföring följt av periodiska överföringar. 

- **Engångsmigrering** – när stora mängder lokala data flyttas till Azure. Till exempel flyttning av data från offlineband till arkiveringsdata i lågfrekvent Azure-lagring.
- **Inkrementell överföring** – när en inledande massöverföring utförs med hjälp av Data Box-Disk (startvärde) följt av inkrementella överföringar över nätverket. Till exempel används Commvault och Data Box Disk för att flytta säkerhetskopior till Azure. Den här migreringen följs av kopiering av inkrementella data via nätverket till Azure-lagring. 
- **Periodiska uppladdningar** – när stora mängder data genereras med jämna mellanrum och behöver flyttas till Azure. Till exempel i energiutforskning, där videoinnehåll genereras på oljeplattformar och vindkraftsparker.

## <a name="the-workflow"></a>Arbetsflödet

Ett typiskt flöde omfattar följande steg:

1. **Beställning** – skapa en beställning i Azure-portalen och ange leveransinformation och Azure-mållagringskonto för dina data. Om diskar är tillgängliga krypterar, förbereder och levererar Azure diskarna med ett leveransspårnings-ID.

2. **Ta emot** – när diskarna har levererats packar du upp och ansluter diskarna till den dator som har data som ska kopieras. Lås upp diskarna.
    
3. **Kopiera data** – dra och släpp för att kopiera data på diskarna.

4. **Returnera** – förbered och skicka tillbaka diskarna till Azure-datacentret.

5. **Ladda upp** – data kopieras automatiskt från diskarna till Azure. Diskarna raderas på ett säkert sätt enligt riktlinjerna från National Institute of Standards and Technology (NIST).

Under den här processen meddelas du via e-post om alla statusändringar. Mer information om det detaljerade flödet finns på sidan om att [distribuera Data Box Disks i Azure-portalen](data-box-disk-quickstart-portal.md).


## <a name="benefits"></a>Fördelar

Data Box Disk har utformats för att flytta stora mängder data till Azure utan att påverka nätverket. Lösningen har följande fördelar:

- **Hastighet** – Data Box Disk använder en USB 3.0-anslutning för att flytta upp till 35 TB data till Azure på mindre än en vecka.   

- **Lätt att använda** – Data Box är en lättanvänd lösning.

    - Diskarna använder USB-anslutning knappt kräver någon installationstid.
    - Diskarna har en liten formfaktor som gör dem enklare att hantera.
    - Diskarna kräver ingen extern ström.
    - Diskarna kan användas med en datacenterserver, stationär dator eller bärbar dator.
    - Lösningen ger slutpunkt till slutpunkt-spårning via Azure-portalen.    

- **Skydd** – Data Box Disk har inbyggt säkerhetsskydd för diskarna, data och tjänsten. 
    - Diskarna är manipulationsresistenta och stöder säker uppdateringsfunktion. 
    - Data på diskarna skyddas alltid med AES 128-bitars kryptering. 
    - Diskarna kan bara låsas upp med en nyckel som tillhandahålls i Azure-portalen. 
    - Tjänsten skyddas av säkerhetsfunktionerna i Azure. 
    - När dina data har laddats upp till Azure rensas diskarna i enlighet med standarderna NIST 800-88r1.  
    
Mer information finns i [Säkerhet och dataskydd i Azure Data Box Disk](data-box-disk-security.md).


## <a name="features-and-specifications"></a>Funktioner och specifikationer


| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Vikt                                                  | < 1 kg per låda. Upp till 5 diskar i lådan                |
| Dimensioner                                              | Disk – 2,5-tums SSD |            
| Kablar                                                  | 1 USB 3.1-kabel per disk|
| Lagringskapacitet per beställning                              | 40 TB (ca 35 TB användbart)|
| Disklagringskapacitet                                   | 8 TB (ca 7 TB användbart)|
| Datagränssnitt                                          | USB   |
| Säkerhet                                                | Förkrypterat med BitLocker och säker uppdatering <br> Nyckelskyddade diskar <br> Data hålls alltid krypterade  |
| Dataöverföringshastighet                                      | upp till 430 MB/s beroende på filstorlek      |
|Hantering                                               | Azure Portal |


## <a name="region-availability"></a>Regional tillgänglighet

Under förhandsversionen kan Data Box Disk överföra data till följande Azure-regioner:


|Azure-region  |Azure-region  |
|---------|---------|
|Västra centrala USA     |Centrala Kanada       |        
|USA, västra 2     |Östra Kanada         |     
|Västra USA     | Västra Europa        |      
|Södra centrala USA   |Norra Europa     |         
|Centrala USA     |Östra Australien|
|Norra centrala USA  |Sydöstra Australien   |
|Östra USA      |Australien, centrala |
|USA, östra 2     |Australien, centrala 2|


## <a name="pricing"></a>Prissättning

Under förhandsversionen är Data Box Disk tillgängligt kostnadsfritt. Detta ändras när Data Box Disk blir allmänt tillgängligt.

## <a name="next-steps"></a>Nästa steg

- Gå igenom [kraven för Data Box Disk](data-box-disk-system-requirements.md).
- Förstå [Data Box Disk-begränsningarna](data-box-disk-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-disk-quickstart-portal.md) på Azure-portalen.
