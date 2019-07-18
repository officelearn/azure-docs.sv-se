---
title: Översikt över Microsoft Azure Data Box Edge | Microsoft Docs
description: Beskriver Azure Data Box Edge, en lagrings lösning som använder en fysisk enhet för nätverks baserad överföring till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305427"
---
# <a name="what-is-azure-data-box-edge"></a>Vad är Azure Data Box Edge? 

Azure Data Box Edge är en AI-aktiverad edge-beräkningsenhet med funktioner för nätverksdataöverföring. Den här artikeln ger dig en översikt över Data Box Edge-lösningen, dess fördelar, viktiga funktioner och scenarier där du kan distribuera den här enheten. 

Data Box Edge är en maskinvaru-som-tjänst-lösning. Microsoft levererar en molnbaserad enhet med en inbyggd fält programmerbar grind mat ris (FPGA) som aktiverar accelererad AI-inferencing och har alla funktioner i en Storage Gateway. 

## <a name="use-cases"></a>Användningsfall

Här följer de olika scenarier där Data Box Edge kan användas för snabb Machine Learning (ML) inferencing vid kant-och förbehandling av data innan de skickas till Azure.

- **Härledning med Azure Machine Learning** – med data Box Edge kan du köra ml-modeller för att få snabba resultat som kan åtgärdas innan data skickas till molnet. Den fullständiga data uppsättningen kan alternativt överföras för att fortsätta att träna och förbättra dina ML-modeller. Mer information om hur du använder de accelererade modellerna för Azure ML-maskin vara på Data Box Edge-enheten finns i [Distribuera Azure ml-accelererade modeller på data Box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Förbearbeta data** för datatransformering innan du skickar dem till Azure för att skapa en mer åtgärds bara data uppsättning. Förbearbeta kan användas för att: 

    - Aggregera data.
    - Ändra data, till exempel för att ta bort personliga data.
    - Delmängd data för att optimera lagring och bandbredd, eller för ytterligare analys.
    - Analysera och reagera på IoT-händelser. 

- **Överföra data över nätverket till Azure** – använd Data Box Edge för att snabbt och enkelt överföra data till Azure för att möjliggöra ytterligare beräkning och analys eller i arkiveringssyfte. 


## <a name="key-capabilities"></a>De viktigaste funktionerna

Data Box Edge har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Accelererad AI-inferencing| Aktive rad av de inbyggda FPGA.|
|Databehandling       |Möjliggör analys, bearbetning och filtrering av data.|
|Höga prestanda | Beräkning och data överföringar med höga prestanda.|
|Dataåtkomst     | Direktåtkomst till data från Azure Storage-blobar och Azure Files med moln-API:er för ytterligare databehandling i molnet. Lokal cache på enheten används för snabb åtkomst till de senast använda filerna.|
|Molnbaserad hantering     |Enhet och tjänst hanteras via Azure Portal.  |
|Offlineuppladdning     | Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Protokoll som stöds     | Stöd för SMB- och NFS-standardprotokoll för datainmatning. <br> Mer information om versioner som stöds finns i [systemkraven för Data Box Edge](data-box-edge-system-requirements.md).|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Kryptering    | BitLocker-stöd för lokal kryptering av data och skydda dataöverföring till molnet via *https*.|
|Bandbredds begränsning| Begränsa bandbredds användningen under hög belastnings tider.|


## <a name="components"></a>Komponenter

Data Box Edge-lösningen består av Data Box Edge-resursen, en fysisk Data Box Edge-enhet och ett lokalt webbgränssnitt.

* **Fysisk Data Box Edge-enhet** – en 1U rackmonterad server som kan konfigureras för att skicka data till Azure tillhandahålls av Microsoft. 
    
* **Data Box Gateway-resurs** – en resurs i Azure-portalen där du kan hantera en Data Box Edge-enhet via ett webbgränssnitt som du kan komma åt på olika geografiska platser. Använd Data Box Edge-resursen till att skapa och hantera resurser, visa och hantera enheter och aviseringar samt hantera resurser.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Mer information finns i [skapa en order för din data Box Edge-enhet](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Lokalt Data Box-webbgränssnitt** – Använd det lokala webbgränssnittet till att köra diagnostik, stänga och starta om Data Box Edge-enheten, visa kopieringsloggar och kontakta Microsoft Support för att skicka en tjänstbegäran.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Information om hur du använder det webbaserade gränssnittet finns i [Använda det webbaserade gränssnittet för att administrera din Data Box](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Regional tillgänglighet

Den fysiska Data Box Edge-enheten, Azure-resursen och mållagringskontot som du vill överföra data till behöver inte alla finnas i samma region.

- **Resurs tillgänglighet** – en lista över alla regioner där data Box Edges resursen är tillgänglig finns i [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Edge kan också distribueras i Azure Government molnet. Mer information finns i [Vad är Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner. De regioner där lagrings kontona lagrar Data Box Edge data finns nära den plats där enheten finns för bästa prestanda. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda. 


## <a name="next-steps"></a>Nästa steg

- Läs [systemkraven för Data Box Edge](data-box-edge-system-requirements.md).
- Förstå [begränsningarna med Data Box Edge](data-box-edge-limits.md).
- Distribuera [Azure Data Box Edge](data-box-edge-deploy-prep.md) på Azure-portalen.




