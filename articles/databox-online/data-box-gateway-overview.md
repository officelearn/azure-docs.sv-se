---
title: Översikt över Microsoft Azure Data Box Gateway | Microsoft Docs
description: Beskriver Azure Data Box Gateway, en lagringslösning med virtuell installation som gör det möjligt att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 01/18/2019
ms.author: alkohli
ms.openlocfilehash: 9670d67fa1eb79e9e5e8c81726c10cc78767fb74
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435472"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Vad är Azure Data Box Gateway (förhandsversion)? 

Azure Data Box Gateway är en lagringslösning som gör att du smidigt kan skicka data till Azure. Den här artikeln ger dig en översikt över Azure Data Box Gateway-lösningen, dess fördelar, viktiga funktioner och scenarier där du kan distribuera den här enheten. 

Data Box Gateway är en virtuell enhet baserad på en virtuell dator som etablerats i din virtualiserade miljö eller i hypervisor-programmet. Den virtuella enheten finns lokalt hos dig och du skriver data till den med hjälp av NFS- och SMB-protokoll. Enheten överför sedan dina data till Azure blockblob, sidblob eller Azure Files. 

> [!IMPORTANT]
> Data Box Gateway är en förhandsversion. Läs [användningsvillkoren för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen.

## <a name="use-cases"></a>Användningsfall

Data Box Gateway kan utnyttjas för att överföra data till molnet, som molnarkivering, haveriberedskap, eller om det är nödvändigt att bearbeta dina data i molnskala. Här följer olika scenarier där Data Box Gateway kan användas för dataöverföring.

- **Molnarkivering** – Kopiera hundratals TB data till Azure Storage med hjälp av Data Box Gateway på ett säkert och effektivt sätt. Data kan matas in en gång eller kontinuerligt för arkiveringsscenarier.

- **Datasammanställning** – Samla ihop data från flera källor på en enda plats i Azure Storage för bearbetning och analys.  

- **Integrering med lokala arbetsbelastningar** – Integrera med lokala arbetsbelastningar, som säkerhetskopiering och återställning som använder molnlagring och behöver lokal åtkomst för ofta använda filer. 

## <a name="benefits"></a>Fördelar

Data Box Gateway har följande fördelar:

- **Enkel dataöverföring** – Gör det enkelt att flytta data till och från Azure Storage, lika enkelt som att arbeta med en lokal nätverksresurs.  
- **Höga prestanda** – Tar bort problemen med nätverksdatatransport med överföringar med höga prestanda till och från Azure. 
- **Snabb åtkomst och höga datainmatningsfrekvenser under kontorstid** – Data Box Gateway har en lokal cache som du definierar som lokal kapacitet när den virtuella enheten etableras. Datadiskstorleken ska anges enligt [minimikraven för virtuella enheter](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). Den lokala cachen ger följande fördelar:
    - Den lokala cachen möjliggör datainmatning med hög hastighet. När stora mängder data matas in vid tider med hög belastning kan cachen förvara data och överför dem till molnet.
    - Den lokala cachen möjliggör snabb läsåtkomst upp till ett visst tröskelvärde. Tills enheten är 50–60 % full används alla läsningar från enheten från cachen, vilket gör att de blir snabbare. När det använda utrymmet på enheten överskrider det här tröskelvärdet börjar enheten att ta bort lokala filer. 
 
- **Begränsad bandbreddsanvändning** – data kan skrivas till Azure även om nätverket begränsar användningen vid tider med hög belastning.  

## <a name="key-capabilities"></a>De viktigaste funktionerna

Data Box Gateway har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Hastighet     | Helt automatiserad och optimerad dataöverföring och bandbredd.|
|Protokoll som stöds     | Stöd för SMB- och NFS-standardprotokoll för datainmatning. <br> Mer information om versioner som stöds finns i [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).|
|Dataåtkomst     | Direktåtkomst till data från Azure Storage-blobar och Azure Files med moln-API:er för ytterligare databehandling i molnet.|
|Snabb åtkomst     | Lokal cache på enheten för snabb åtkomst till de senast använda filerna.|
|Offlineuppladdning     | Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Kryptering    | BitLocker-stöd för lokal kryptering av data och skydda dataöverföring till molnet via *https*       |
|Återhämtning     | Inbyggd nätverksåterhämtning        |


## <a name="specifications"></a>Specifikationer

Den virtuella Data Box-Gateway-enheten har följande specifikationer:

| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Virtuella processorer (kärnor)   | Minst 4 |            
| Minne  | Minst 8 GB|
| Tillgänglighet|Enkel nod|
| Diskar| OS-disk: 250 GB <br> Datadisk: minst 2 TB, tunt allokerad och måste backas upp av SSD-enheter|
| Nätverksgränssnitt|Minst 1 virtuellt nätverksgränssnitt|
| Interna fildelningsprotokoll|SMB och NFS  |
| Säkerhet| Autentisering för att låsa upp åtkomst till enhet och data <br> Data-in-flight-krypterad med AES-256-bitarskryptering|
| Hantering| Lokalt webbgränssnitt – inledande konfiguration, diagnostik och energisparfunktioner för enheten <br> Azure-portalen – daglig hantering av Data Box Gateway-enheter       |


## <a name="components"></a>Komponenter

Data Box Gateway-lösningen består av Data Box Gateway-resurs, virtuell Data Box Gateway-enhet och ett lokalt webbgränssnitt.

* **Virtuell Data Box Gateway-enhet** – En enhet baserad på en virtuell dator som har etablerats i din virtualiserade miljö eller i hypervisor-programmet och gör att du kan skicka data till Azure. 
    
* **Data Box Gateway-resurs** – En resurs i Azure-portalen där du kan hantera en Data Box Gateway-enhet via ett webbgränssnitt som du kommer åt på olika geografiska platser. Använd Data Box Gateway-resursen till att skapa och hantera resurser, visa och hantera enheter och aviseringar samt hantera resurser.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Lokalt Data Box-webbgränssnitt** – Använd det lokala webbgränssnittet till att köra diagnostik, stänga och starta om Data Box Gateway-enheten, visa kopieringsloggar och kontakta Microsoft Support för att skicka en tjänstbegäran.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>Regional tillgänglighet

Den fysiska Data Box Edge-enheten, Azure-resursen och mållagringskontot som du vill överföra data till behöver inte alla finnas i samma region.

- **Resurstillgänglighet** – för den här versionen är Data Box Edge-resursen tillgänglig i följande regioner:
    - **USA** – USA, västra 2 och USA, östra
    - **EU** – Europa, västra
    - **Asien och stillahavsområdet** – Sydostasien

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner. 

    För optimala prestanda bör de regioner där lagringskonton lagrar Data Box-data ligga nära den plats där enheten finns. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda. 


## <a name="next-steps"></a>Nästa steg

- Se [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).
- Förstå [begränsningarna för Data Box Gateway](data-box-gateway-limits.md).
- Distribuera [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i Azure-portalen.




