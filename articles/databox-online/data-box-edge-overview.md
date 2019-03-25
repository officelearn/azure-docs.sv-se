---
title: Översikt över Microsoft Azure Data Box Edge | Microsoft Docs
description: Beskriver Azure Data Box Edge, en lagringslösning som använder en fysisk enhet för nätverksbaserad överföring till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 696f4fee95926009ebda0be3c28672838d89edda
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400440"
---
# <a name="what-is-azure-data-box-edge"></a>Vad är Azure Data Box Edge? 

Azure Data Box Edge är en lagringslösning som gör det möjligt att bearbeta data och skicka dem via nätverk till Azure. Den här artikeln ger dig en översikt över Data Box Edge-lösningen, dess fördelar, viktiga funktioner och scenarier där du kan distribuera den här enheten. 

Data Box Edge använder en fysisk enhet som tillhandahålls av Microsoft för att påskynda säker dataöverföring. Den fysiska enheten finns lokalt hos dig och du skriver data till den med hjälp av NFS- och SMB-protokoll. 

Data Box Edge har alla gateway-funktioner hos Data Box Gateway. Data Box är även utrustat med AI-aktiverade edge-beräkningsfunktioner som hjälper till att analysera, bearbeta eller filtrera data som flyttas till Azure-blockblob, sidblob eller Azure Files.  

## <a name="use-cases"></a>Användningsfall

Azure Data Box Edge är en AI-aktiverad edge-beräkningsenhet med funktioner för nätverksdataöverföring. Här följer olika scenarier där Data Box Edge kan användas för dataöverföring.

- **Förbearbeta data** – analysera data från lokala enheter eller IoT-enheter för att snabbt få resultat nära det ställe där data genereras. Data Box Edge överför de fullständiga datauppsättningarna till molnet för att kunna utföra mer avancerad bearbetning eller djupare analyser.  Förbearbeta kan användas för att: 

    - Aggregera data.
    - Ändra data, till exempel för att ta bort personligt identifierbar Information (PII).
    - Dela in de data som behövs för djupare analys i molnet i delmängder och överföra dem.
    - Analysera och reagera på IoT-händelser. 

- **Inference Azure Machine Learning** – med Data Box Edge kan du köra ML-modeller (Machine Learning) för att få snabba resultat som kan användas innan data skickas till molnet. Den fullständiga datamängden överförs för att fortsätta att träna om och förbättra dina ML-modeller.

- **Överföra data över nätverket till Azure** – använd Data Box Edge för att snabbt och enkelt överföra data till Azure för att möjliggöra ytterligare beräkning och analys eller i arkiveringssyfte. 

## <a name="benefits"></a>Fördelar

Data Box Edge har följande fördelar:

- **Enkel dataöverföring** – gör det lika enkelt att flytta data till och från Azure Storage som att arbeta med en lokal nätverksresurs.  
- **Höga presteranda** – ger högpresterande dataöverföringar till och från Azure. 
- **Snabb åtkomst** – cachelagrar de senaste filerna för snabb åtkomst av lokala filer.  
- **Begränsad bandbreddsanvändning** – data kan skrivas till Azure även om nätverket begränsar användningen vid tider med hög belastning.  
- **Omvandla data** – möjliggör analys, bearbetar och filtrering av data som flyttas till Azure.

## <a name="key-capabilities"></a>De viktigaste funktionerna

Data Box Edge har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Höga prestanda     | Helt automatiserad och optimerad dataöverföring och bandbredd.|
|Protokoll som stöds     | Stöd för SMB- och NFS-standardprotokoll för datainmatning. <br> Mer information om versioner som stöds finns i [systemkraven för Data Box Edge](https://aka.ms/dbe-docs).|
|Databehandling       |Möjliggör analys, bearbetning och filtrering av data.|
|Dataåtkomst     | Direktåtkomst till data från Azure Storage-blobar och Azure Files med moln-API:er för ytterligare databehandling i molnet.|
|Snabb åtkomst     | Lokal cache på enheten för snabb åtkomst till de senast använda filerna.|
|Offlineuppladdning     | Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Kryptering    | BitLocker-stöd för lokal kryptering av data och skydda dataöverföring till molnet via *https*.       |
|Återhämtning     | Inbyggd nätverksåterhämtning.        |


## <a name="features-and-specifications"></a>Funktioner och specifikationer

Den fysiska Data Box Edge-enheten har följande funktioner:

| Funktioner/specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Dimensioner   | Bredd: 17.25 ”djup: 27.25 ”höjd: 1.75”<br>(exklusive öron och nätaggregatshandtag)  |            
| Rackutrymme|1U vid placering i racket|
| Kablar| 2 x strömkabel<br>2 x 1 Gbit/s RJ45-kablar<br>2 x 10 Gbit/s SFP+-kopparkablar|
| Komponenter|2 inbyggda nätaggregat (PSU:er)|
| Processor|2 Intel Xeon-processorer med 10 kärnor vardera  |
| Minne| 64 GB RAM|
| Diskar| 8 NVMe SSD-enheter med 1,6 TB vardera <br> Systemet kraschar om en NVMe-SSD slutar fungera. |
| Lokal lagringskapacitet| 12,8 TB total kapacitet|
| Nätverksgränssnitt| 2 x 1 GbE-gränssnitt – 1 för hantering som inte kan konfigureras av användaren används för installationen. Det andra gränssnittet kan konfigureras av användaren, kan användas för dataöverföring och är DHCP som standard. <br>2 x 25 GbE-gränssnitt – dessa kan även fungera som 10 GbE-gränssnitt. Dessa datagränssnitt kan konfigureras av användaren som DHCP (standard) eller statiska. <br> 2 x 25 GbE-gränssnitt – dessa datagränssnitt kan konfigureras av användaren som DHCP (standard) eller statiska.|

## <a name="components"></a>Komponenter

Data Box Edge-lösningen består av Data Box Edge-resursen, en fysisk Data Box Edge-enhet och ett lokalt webbgränssnitt.

* **Fysisk Data Box Edge-enhet** – en 1U rackmonterad server som kan konfigureras för att skicka data till Azure tillhandahålls av Microsoft. 
    
* **Data Box Gateway-resurs** – en resurs i Azure-portalen där du kan hantera en Data Box Edge-enhet via ett webbgränssnitt som du kan komma åt på olika geografiska platser. Använd Data Box Edge-resursen till att skapa och hantera resurser, visa och hantera enheter och aviseringar samt hantera resurser.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Mer information finns i [Använda Data Box Edge-tjänsten för att administrera din Data Box Edge-enhet](https://aka.ms/dbe-docs).

* **Lokalt Data Box-webbgränssnitt** – Använd det lokala webbgränssnittet till att köra diagnostik, stänga och starta om Data Box Edge-enheten, visa kopieringsloggar och kontakta Microsoft Support för att skicka en tjänstbegäran.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Information om hur du använder det webbaserade gränssnittet finns i [Använda det webbaserade gränssnittet för att administrera din Data Box](https://aka.ms/dbe-docs).


## <a name="region-availability"></a>Regional tillgänglighet

Den fysiska Data Box Edge-enheten, Azure-resursen och mållagringskontot som du vill överföra data till behöver inte alla finnas i samma region.

- **Resurstillgänglighet** – för den här versionen är Data Box Edge-resursen tillgänglig i följande regioner:
    - **USA** – USA, västra 2 och USA, östra
    - **EU** – Europa, västra
    - **Asien och stillahavsområdet** – Sydostasien

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner. 

    För optimala prestanda bör de regioner där lagringskonton lagrar Data Box-data ligga nära den plats där enheten finns. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda. 


## <a name="sign-up"></a>Registrera dig

Data Box Edge är en förhandsversion och du måste registrera dig. Registrera dig för Data Box Gateway via följande steg:

1. Logga in på Azure-portalen på: [https://aka.ms/databox-edge](https://aka.ms/databox-edge).

2. Välj den prenumeration som du vill använda för förhandsversionen av Data Box Edge. Välj den region där du vill distribuera Data Box Edge-resursen. I alternativet för Data Box Edge klickar du på **Registrera**.

    ![Registrering för Data Box Edge 3](media/data-box-edge-overview/data-box-edge-sign-up3.png)

3.  Svara på frågor om datahemland, tidsperiod, Azure-måltjänst för dataöverföring, nätverkets bandbredd och dataöverföringsfrekvens. Granska **sekretess och villkor** och markera kryssrutan mot att **Microsoft kan använda din e-postadress för att kontakta dig**.

    ![Registrering för Data Box Edge 4](media/data-box-edge-overview/data-box-edge-sign-up4.png)

4. När du är registrerad och aktiverad för förhandsversionen kan du beställa du Data Box Edge.

## <a name="next-steps"></a>Nästa steg

- Läs [systemkraven för Data Box Edge](https://aka.ms/dbe-docs).
- Förstå [begränsningarna med Data Box Edge](https://aka.ms/dbe-docs).
- Distribuera [Azure Data Box Edge](https://aka.ms/dbe-docs) på Azure-portalen.




