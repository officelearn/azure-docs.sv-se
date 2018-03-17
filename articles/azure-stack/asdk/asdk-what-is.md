---
title: En introduktion till Azure Stack Development Kit (ASDK) | Microsoft Docs
description: "Beskriver ASDK och vanliga användningsområden för utvärdering av Microsoft Azure-stacken."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5a268a29c7a767084049bf56270aa8bc9d2ccc3f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="what-is-the-azure-stack-development-kit"></a>Vad är Azure Stack Development Kit?
[Microsoft Azure-stacken integrerat system](.\.\azure-stack-poc.md) i intervallet från 4 – 12 noder och gemensamt stöds av en partner för maskinvara och Microsoft. Använd Azure-stacken integrerat system för att aktivera nya scenarier för din produktionsarbetsbelastningar. Om du är operatör Azure Stack som hanterar den integrerade system-infrastrukturen och erbjuder tjänster, finns våra [operatorn dokumentationen](https://docs.microsoft.com/azure/azure-stack).

Azure Stack Development Kit (ASDK) är en nod distribution av Azure-stacken som du kan hämta och använda **gratis**. Alla ASDK komponenter är installerade på virtuella datorer som körs på en enda värddator på en server som måste uppfylla eller överträffa den [maskinvarukraven](asdk-deploy-considerations.md#hardware). ASDK är avsedd att tillhandahålla en miljö där du kan utvärdera Azure Stack och utveckla moderna applikationer med API: er och tooling konsekvent med Azure i en *icke-produktion* miljö. 

> [!IMPORTANT]
> ASDK är inte avsedd att användas eller användas i en produktionsmiljö.

Eftersom alla ASDK komponenter har distribuerats till en enda development kit värddator, finns det begränsad fysiska resurser. Med ASDK distributioner både Azure-stacken infrastruktur för virtuella datorer och klient VMs fungera på samma server-dator. Den här konfigurationen är inte avsedd för utvärdering av skala eller prestanda.

ASDK är utformad att ge en Azure-hybrid cloud ger konsekvent upplevelse:
- **Administratörer** (Azure Stack operatörer). ASDK är en utmärkt resurs för att utvärdera och lär dig mer om tillgängliga Azure Stack-tjänster.
- **Utvecklare**. ASDK kan användas för att utveckla hybrid eller moderna program lokalt (utveckling och testning miljöer). Det ger dig repeterbara utvecklingsarbetet innan eller tillsammans med Azure-stacken Produktionsdistribution. 

Titta på den här korta videon mer information om ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Azure-stacken skillnaderna i ASDK och flera noder
Nod ASDK distributioner skiljer sig från flera noder Azure Stack distributioner i några viktiga sätt som du bör vara medveten om.

|Beskrivning|ASDK|Multi-node Azure Stack|
|-----|-----|-----|
|**Skalning**|Alla komponenter är installerade på en enda nod server.|Kan variera i storlek från 4-12-noder.|
|**Återhämtning**|Konfiguration nod ge inte hög tillgänglighet|[Hög tillgänglighet](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) funktioner stöds.|
|**Nätverk**|ASDK använder en virtuell dator med namnet AzS BGPNAT01 för att dirigera nätverkstrafik för alla ASDK. Det finns inga ytterligare växeln krav.|AzS BGPNAT01 VM finns inte i distributioner med flera noder. Mer komplexa [routning nätverksinfrastruktur](.\.\azure-stack-network.md#network-infrastructure) krävs inklusive Top-Of-Rack (TOR), Baseboard Management Controller (BMC) och växlar kantlinje (datacenternätverk).|
|**Patch-och uppdateringsprocessen**|Om du vill flytta till en ny version av ASDK, måste du omdistribuera ASDK på värddatorn development kit.|[Korrigera och uppdatera](.\.\azure-stack-updates.md) process som används för att uppdatera den installerade versionen av Azure-stacken.|
|**Support**|Forum för MSDN Azure-stacken. Stöd för Microsofts kundservice och Support (CSS) är *inte* tillgänglig för icke-produktionsmiljöer.|[MSDN Azure-stacken forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) och stöd för fullständig CSS.|
| | |

## <a name="learn-about-available-services"></a>Lär dig mer om tillgängliga tjänster
Som operatör Azure Stack som du behöver veta vilka tjänster du kan göra tillgängliga för användarna. Azure-stacken stöder en delmängd av Azure-tjänster och listan över stöds tjänster fortsätter att utvecklas över tid.

### <a name="foundational-services"></a>Grundläggande tjänster
Azure-stacken innehåller som standard följande ”grundläggande tjänster” när du distribuerar ASDK:
- Compute
- Lagring
- Nätverk
- Key Vault

Dessa grundläggande tjänster kan du erbjuda Infrastructure-as-a-Service (IaaS) till användarna med minimal konfiguration.

### <a name="additional-services"></a>Ytterligare tjänster
För närvarande stöds följande ytterligare plattform som en-tjänst (PaaS)-tjänster:
- App Service
- Azure Functions
- SQL- och MySQL-databaser

> [!NOTE]
> Dessa tjänster kräva ytterligare konfiguration innan du kan göra dem tillgängliga för användarna och finns inte som standard när du installerar ASDK.

## <a name="service-roadmap"></a>Översikt över Service
Azure-stacken fortsätter att lägga till stöd för ytterligare Azure-tjänster. Mer information om vad kommer härnäst med Azure-stacken, finns det [översikt över Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Nästa steg
Om du vill komma igång med att utvärdera Azure-stacken, måste du förbereda development kit värden server-datorn och sedan [installera ASDK](asdk-deploy.md). Därefter kan du logga in på portalerna administratörs- och att börja använda Azure-stacken.