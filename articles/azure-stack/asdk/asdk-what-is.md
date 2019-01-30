---
title: En introduktion till Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Beskriver vad som är ASDK och vanliga användningsområden för utvärdering av Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 9cfa06dfd0b38aa288502afbee30fb48ee7dd4e1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247671"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Vad är Azure Stack Development Kit?
[Integrerade Microsoft Azure Stack-system](../azure-stack-poc.md) i intervallet från 4 – 16 noder och gemensamt stöds av en partner för maskinvara och Microsoft. Du kan använda integrerade Azure Stack-systemen för att aktivera nya scenarier för produktionsarbetsbelastningar. Om du är en Azure Stack-operatör som hanterar den integrerade system-infrastrukturen och erbjuder tjänster, se vår [operatorn dokumentation](https://docs.microsoft.com/azure/azure-stack).

Azure Stack Development Kit (ASDK) är en nod-distribution av Azure Stack som du kan hämta och använda **kostnadsfritt**. Alla ASDK komponenter är installerade på virtuella datorer som körs på en enda värddator för server och som måste uppfylla eller överträffa de [maskinvarukraven](asdk-deploy-considerations.md#hardware). ASDK är avsedd att tillhandahålla en miljö där du kan utvärdera Azure Stack och utveckla moderna program med API: er och verktyg konsekvent med Azure i en *icke-produktion* miljö. 

> [!IMPORTANT]
> ASDK är inte avsedd att användas eller som stöds i en produktionsmiljö.

Eftersom alla ASDK komponenter har distribuerats till en enda development kit-värddator, finns det begränsad fysiska resurser. Med ASDK distributioner, både Azure Stack-infrastrukturens virtuella datorer och klienten virtuella datorer finnas på samma server-dator. Den här konfigurationen är inte avsedd för utvärdering av skalbarhet eller prestanda.

ASDK har utformats för att skapa en Azure-konsekventa hybrid cloud-upplevelse för:
- **Administratörer** (Azure Stack-operatörer). ASDK är en bra resurs att utvärdera och lär dig mer om de tillgängliga Azure Stack-tjänsterna.
- **Utvecklare**. ASDK kan användas för att utveckla hybrid eller moderna program lokalt (miljöer för utveckling/testning). Det ger repeterbarhet av utvecklingsupplevelse före eller tillsammans med Azure Stack-Produktionsdistribution. 

Den här korta videon om du vill veta mer om ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK och flera noder Azure Stack-skillnader
Nod ASDK distributioner skiljer sig från flera noder Azure Stack-distributioner på flera viktiga sätt som du bör känna till.

|Beskrivning|ASDK|Flera noder Azure Stack|
|-----|-----|-----|
|**Skalning**|Alla komponenter är installerade på en nod serverdator.|Kan variera i storlek från 4 – 16 noder.|
|**Återhämtning**|Konfiguration av en nod ge inte hög tillgänglighet|[Hög tillgänglighet](../azure-stack-key-features.md#high-availability-for-azure-stack) funktioner som stöds.|
|**Nätverk**|ASDK använder en virtuell dator med namnet AzS-BGPNAT01 för att dirigera all ASDK nätverkstrafik. Det finns inga krav på ytterligare växel.|AzS-BGPNAT01 VM finns inte i flernodiga distributioner. Mer komplexa [network routningsinfrastruktur](../azure-stack-network.md#network-infrastructure) krävs inklusive Top-Of-Rack (TOR), Hanteringsstyrenheten för baskort (BMC) och kantlinje (datacenternätverk) växlar.|
|**Patch- och uppdateringsprocessen**|Om du vill flytta till en ny version av ASDK, måste du distribuera om ASDK på värddatorn för development kit.|[Korrigera och uppdatera](../azure-stack-updates.md) process som används för att uppdatera den installerade versionen av Azure Stack.|
|**Support**|MSDN Azure Stack-forum. Microsofts kundservice och Support (CSS) support är *inte* tillgänglig för icke-produktionsmiljöer.|[MSDN Azure Stack-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) och stöd för fullständig CSS.|
| | |

## <a name="learn-about-available-services"></a>Lär dig mer om tillgängliga tjänster
Som operatör Azure Stack som du behöver veta vilka tjänster kan du göra tillgängliga för användarna. Azure Stack stöd för en delmängd av Azure-tjänster och listan över tjänster som stöds fortsätter att utvecklas över tid.

### <a name="foundational-services"></a>Grundläggande tjänster
Som standard Azure Stack innehåller följande ”grundläggande” när du distribuerar ASDK:
- Compute
- Storage
- Nätverk
- Key Vault

Med dessa grundläggande tjänster, kan du erbjuda användarna med minimal konfiguration Infrastructure-as-a-Service (IaaS).

### <a name="additional-services"></a>Ytterligare tjänster
För närvarande stöds följande ytterligare Platform-as-a-Service (PaaS)-tjänster:
- App Service
- Azure Functions
- SQL- och MySQL-databaser

> [!NOTE]
> De här tjänsterna kräver ytterligare konfiguration innan du kan göra dem tillgängliga för användarna och är inte tillgängliga som standard när du installerar ASDK.

## <a name="service-roadmap"></a>Planerna för tjänsten
Azure Stack kommer att fortsätta att lägga till stöd för ytterligare Azure-tjänster. Läs mer om vad som kommer härnäst med Azure Stack, i den [Azure Stack-översikt](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Nästa steg
För att komma igång med att utvärdera Azure Stack, måste du först [ladda ned den senaste ASDK](asdk-download.md) och Förbered ASDK värddatorn. När du har förberett development kit värden kan du installera ASDK och logga in på portalerna administratörs- och att börja använda Azure Stack.