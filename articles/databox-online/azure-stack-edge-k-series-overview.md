---
title: Översikt över Azure Stack Edge Mini R | Microsoft Docs
description: Beskriver Azure Stack Edge Mini R, en lagrings lösning för militära program som använder en bärbar fysisk enhet med ett batteri för överföring via Wi-Fi till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 1ea04633708e866b6f200bef0eb6e331c06873e9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948247"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Vad är Azure Stack Edge Mini R?

Azure Stack Edge Mini R är en lösning för maskin vara som en tjänst. Microsoft levererar en robust, molnbaserad, mycket portabel enhet med en inbyggd VPU (vision Processing Unit) som möjliggör accelererad AI-inferencing och har alla funktioner i en gateway för nätverks lagring. Dessa enheter är lämpliga för användning i de miljöer som är svåra att använda och är optimerade för AI-, analys-och Server lös data behandling.

Den här artikeln innehåller en översikt över Azure Stack Edge Mini R-lösningen, viktiga funktioner och scenarier där du kan distribuera den här enheten.


## <a name="key-capabilities"></a>De viktigaste funktionerna

Azure Stack Edge Mini R har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Robust maskin vara| Robust maskin vara utformad för miljöer med tufft syfte.|
|Ultra portabel| Mycket bärbar, batteri driven form faktor.|
|Molnbaserad hantering|Enhet och tjänst hanteras via Azure Portal.|
|Edge Compute-arbetsbelastningar|Möjliggör analys, bearbetning och filtrering av data.<br>Stöder virtuella datorer och arbets belastningar i behållare. |
|Accelererad AI-inferencing| Aktive rad av Intel Movidius myriaden X-VPU.|
|Kabelanslutna och trådlösa | Tillåter kabelanslutna och trådlösa data överföringar.|
|Dataåtkomst     | Direktåtkomst till data från Azure Storage-blobar och Azure Files med moln-API:er för ytterligare databehandling i molnet. Lokal cache på enheten används för snabb åtkomst till de senast använda filerna.|
|Frånkopplat läge| Enhet och tjänst kan hanteras alternativt via Azure Stack. <br> Distribuera, kör, hantera program i offlineläge. <br> Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Protokoll som stöds     |Stöder standard-SMB-, NFS-och REST-protokoll för data inmatning. <br> Mer information om versioner som stöds finns i [Azure Stack Edge Mini R system krav](azure-stack-edge-gpu-system-requirements.md).|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Dubbel kryptering    | Med hjälp av Självkrypterande enhet får du det första krypterings lagret. VPN tillhandahåller det andra krypterings lagret. BitLocker-stöd för lokal kryptering av data och säker data överföring till molnet via *https* .|
|Bandbredds begränsning| Begränsa bandbredds användningen under hög belastnings tider.|

## <a name="use-cases"></a>Användningsfall

Här följer de olika scenarier där Azure Stack Edge Mini R kan användas för snabb Machine Learning (ML) inferencing vid kant-och förbearbetning av data innan de skickas till Azure.

- **Härledning med Azure Machine Learning** – med Azure Stack Edge Mini R kan du köra ml-modeller för att få snabba resultat som kan åtgärdas innan data skickas till molnet. Den fullständiga data uppsättningen kan alternativt överföras för att fortsätta att träna och förbättra dina ML-modeller. Mer information om hur du använder Azure ML-accelererade maskin varu modeller på Azure Stack Edge Mini R-enheten finns i [Distribuera Azure ml-maskinvarustödd modeller på Azure Stack Edge mini r](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Förbearbeta data** för att transformera data via beräknings alternativ som behållare eller virtuella datorer innan du skickar dem till Azure för att skapa en mer åtgärds bara data uppsättning. Förbearbeta kan användas för att:

    - Aggregera data.
    - Ändra data, till exempel för att ta bort personliga data.
    - Delmängd data för att optimera lagring och bandbredd, eller för ytterligare analys.
    - Analysera och reagera på IoT-händelser.

- **Överföra data över nätverk till Azure** – Använd Azure Stack Edge Mini R för att enkelt och snabbt överföra data till Azure för att möjliggöra ytterligare beräkning och analys eller för arkivering.

## <a name="components"></a>Komponenter

Azure Stack Edge Mini R-lösningen består av en Azure Stack gräns resurs, Azure Stack Edge Mini R robust, ultra portabel fysisk enhet och ett lokalt webb gränssnitt.

* **Azure Stack Edge Mini R fysisk enhet** – en mycket bärbar enhet med ett onboard-batteri och ett robust hölje som tillhandahålls av Microsoft och som kan konfigureras för att skicka data till Azure. Enheten med batteriet väger mindre än 7 kg.

    ![Azure Stack Edge Mini R-enhet](media/azure-stack-edge-k-series-overview/perspective-view-1.png)

* **Azure Stack Edge-resurs** – en resurs i Azure Portal som låter dig hantera en robust, Azure Stack Edge-Mini R-enhet från ett webb gränssnitt som du kan komma åt från olika geografiska platser. Använd Azure Stack Edge-resursen för att skapa och hantera resurser, Visa och hantera enheter och aviseringar och hantera resurser.  

* **Azure Stack Edge mini r lokalt webb gränssnitt** – Använd det lokala webb gränssnittet för inledande enhets konfiguration för att köra diagnostik, stänga av och starta om Azure Stack Edge-mini r-enheten, Visa kopierings loggar och kontakta Microsoft Support för att skicka en tjänstbegäran.


## <a name="region-availability"></a>Regional tillgänglighet

Azure Stack Edge Mini R fysisk enhet, Azure-resurs och mål lagrings konto som du överför data till, behöver inte alla finnas i samma region.

- **Resurs tillgänglighet** – för en lista över alla regioner där Azure Stack Edge-resursen är tillgänglig går du till [Azure-produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner. De regioner där lagrings kontona lagrar Azure Stack Edge-Mini R-data ska ligga nära den plats där enheten befinner sig för optimala prestanda. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda.


## <a name="next-steps"></a>Nästa steg

- Granska [kraven för Azure Stack Edge Mini R-systemet](azure-stack-edge-gpu-system-requirements.md).


