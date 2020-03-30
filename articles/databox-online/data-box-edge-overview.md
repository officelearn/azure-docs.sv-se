---
title: Översikt över Microsoft Azure Stack Edge | Microsoft-dokument
description: Beskriver Azure Stack Edge, en lagringslösning som använder en fysisk enhet för nätverksbaserad överföring till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: f463e8883efd5e2dfc4d7fff80912c193665b850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399796"
---
# <a name="what-is-azure-stack-edge"></a>Vad är Azure Stack Edge?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge är en AI-aktiverad edge computing-enhet med funktioner för överföring av nätverksdata. Den här artikeln innehåller en översikt över Azure Stack Edge-lösningen, fördelar, nyckelfunktioner och scenarier där du kan distribuera den här enheten.

Azure Stack Edge är en lösning för maskinvara som en tjänst. Microsoft skickar en molnhanterad enhet med en inbyggd FPGA (Field Programmemable Gate Array) som möjliggör accelererad AI-inferencing och har alla funktioner i en nätverkslagringsgateway.

## <a name="use-cases"></a>Användningsfall

Här är de olika scenarier där Azure Stack Edge kan användas för snabb Machine Learning (ML) inferencing vid kanten och förbearbetning data innan du skickar den till Azure.

- **Slutsats med Azure Machine Learning** - Med Azure Stack Edge kan du köra ML-modeller för att få snabba resultat som kan hanteras innan data skickas till molnet. Den fullständiga datauppsättningen kan eventuellt överföras för att fortsätta att omskola och förbättra dina ML-modeller. Mer information om hur du använder Azure ML-maskinvaruaccelererad modell på Azure Stack Edge-enheten finns i [Distribuera Azure ML-maskinvaruaccelererad modell på Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Förbehandla data** - Omvandla data innan du skickar dem till Azure för att skapa en mer användbar datauppsättning. Förbearbeta kan användas för att: 

    - Aggregera data.
    - Ändra data, till exempel för att ta bort personuppgifter.
    - Delmängdsdata för att optimera lagring och bandbredd, eller för vidare analys.
    - Analysera och reagera på IoT-händelser. 

- **Överför data via nätverk till Azure** - Använd Azure Stack Edge för att enkelt och snabbt överföra data till Azure för att möjliggöra ytterligare beräkning och analys eller för arkivering. 

## <a name="key-capabilities"></a>De viktigaste funktionerna

Azure Stack Edge har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Accelererad AI inferencing| Aktiverad av den inbyggda FPGA.|
|Databehandling       |Möjliggör analys, bearbetning och filtrering av data.|
|Höga prestanda | Beräknings- och dataöverföringar med hög prestanda.|
|Dataåtkomst     | Direktåtkomst till data från Azure Storage-blobar och Azure Files med moln-API:er för ytterligare databehandling i molnet. Lokal cache på enheten används för snabb åtkomst av senast använda filer.|
|Molnhanterad     |Enhet och tjänst hanteras via Azure-portalen.  |
|Offlineuppladdning     | Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Protokoll som stöds     | Stöd för SMB- och NFS-standardprotokoll för datainmatning. <br> Mer information om versioner som stöds finns i [Systemkraven för Azure Stack Edge](data-box-edge-system-requirements.md).|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Kryptering    | BitLocker-stöd för lokal kryptering av data och skydda dataöverföring till molnet via *https*.|
|Bandbreddsbegränsning| Begränsa för att begränsa bandbreddsanvändningen under rusningstid.|
|ExpressRoute | Lade till säkerhet via ExpressRoute. Använd peering-konfiguration där trafik från lokala enheter till molnlagringsslutpunkterna färdas över ExpressRoute. Mer information finns i [Översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Komponenter

Azure Stack Edge-lösningen består av Azure Stack Edge-resurs, azure Stack Edge fysisk enhet och ett lokalt webbgränssnitt.

* **Azure Stack Edge fysisk enhet** - En 1U rackmonterad server som tillhandahålls av Microsoft som kan konfigureras för att skicka data till Azure.
    
* **Azure Stack Edge-resurs** – en resurs i Azure-portalen som låter dig hantera en Azure Stack Edge-enhet från ett webbgränssnitt som du kan komma åt från olika geografiska platser. Använd Azure Stack Edge-resursen för att skapa och hantera resurser, visa och hantera enheter och aviseringar och hantera resurser.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Mer information finns i [Skapa en order för din Azure Stack Edge-enhet](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Azure Stack Edge lokalt webbgränssnitt** - Använd det lokala webbgränssnittet för att köra diagnostik, stänga av och starta om Azure Stack Edge-enheten, visa kopieringsloggar och kontakta Microsoft Support för att lämna in en tjänstbegäran.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Information om hur du använder det webbaserade användargränssnittet finns i [Använda det webbaserade användargränssnittet för att administrera ditt Azure Stack Edge](data-box-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Regional tillgänglighet

Azure Stack Edge fysisk enhet, Azure-resurs och mållagringskonto som du överför data till behöver inte alla vara i samma region.

- **Resurstillgänglighet** – För en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig finns i [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge kan också distribueras i Azure Government Cloud. Mer information finns i [Vad är Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner. De regioner där lagringskontona lagrar Azure Stack Edge-data bör finnas nära där enheten finns för optimal prestanda. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda.

## <a name="next-steps"></a>Nästa steg

- Granska [systemkraven](data-box-edge-system-requirements.md)för Azure Stack Edge .
- Förstå [Azure Stack Edge-gränserna](data-box-edge-limits.md).
- Distribuera [Azure Azure Stack Edge](data-box-edge-deploy-prep.md) i Azure-portalen.
