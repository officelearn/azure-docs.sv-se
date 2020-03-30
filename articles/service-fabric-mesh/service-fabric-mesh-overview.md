---
title: Översikt över Azure Service Fabric Mesh
description: Lär dig mer om Azure Service Fabric Mesh. Med Service Fabric Mesh kan du distribuera och skala ditt program utan att behöva bekymra dig om programmets infrastrukturbehov.
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: d6522d417556104a1ece703c725f3fbeab49d683
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75458983"
---
# <a name="what-is-service-fabric-mesh"></a>Vad är Service Fabric Mesh?

Den här videon ger en snabb översikt över Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Program som finns i Service Fabric Mesh körs och skalas utan att du att behöver bekymra dig om den infrastruktur som driver det.  Service Fabric Mesh består av kluster med tusentals datorer.  Alla klusteråtgärder är dolda från utvecklaren. Ladda upp koden och ange resurser du behöver, tillgänglighetskrav och resursgränser.  Service Fabric Mesh allokerar automatiskt infrastrukturen och hanterar infrastrukturfel, så att dina program har hög tillgänglighet. Du behöver bara bry dig om programmets hälsotillstånd och svarstid, inte infrastrukturen.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Den här artikeln innehåller en översikt över de främsta fördelarna med Service Fabric Mesh.

## <a name="great-developer-experience"></a>Utmärkta funktioner för utvecklare

Service Fabric Mesh stöder alla programmeringsspråk och ramverk som kan köras i en container. Stöd för verktyg för Visual Studio 2019 och Visual Studio Code ger en kraftfull redigerings- och felsökningsupplevelse för .NET- och .NET Core-program. 

Med Service Fabric Mesh kan du:

- ”Lift and shift” befintliga program till containrar för att modernisera och köra dina aktuella program i stor skala.
- Skapa och distribuera nya mikrotjänstprogram skalenligt i Azure.  Integrera med andra Azure-tjänster eller befintliga program som körs i containrar. Varje mikrotjänst är en del av ett säkert, nätverks isolerat program. Mikrotjänsten har principer för resursstyrning som definierats för PROCESSORKÄRNOR, minne, diskutrymme med mera.
- Integrera med och utöka befintliga program utan att ändra dessa program. Använda ditt egna virtuella nätverk för att ansluta befintliga program till det nya programmet.  
- Modernisera dina befintliga Cloud Services-program genom att migrera till Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Enkel driftslivscykel

Hantera enkelt program som körs, övervaka program och felsöka i produktionsmiljöer. Den här hanteringen omfattar programuppgraderingar och versionshantering. Dessa program kan bestå av en enda mikrotjänst eller flera mikrotjänster som är isolerade inom sina egna nätverk. Program körs effektivt med snabb distribution, placering och redundanstid.

Med Service Fabric Mesh kan du:

- Distribuera och hantera program utan att explicit etablera och hantera infrastruktur.  Service Fabric Mesh etablerar, uppgraderar, korrigeringar och underhåller den underliggande infrastrukturen åt dig.
- Konfigurera kontinuerlig integrering med integrerade verktyg för att enkelt paketera och distribuera program.
- Utnyttja alla funktioner i Azure Resource Manager-resurser. Exempel på dessa funktioner är granskningsspår och [rollbaserad åtkomstkontroll](/azure/role-based-access-control/overview)). Alla resurser som du distribuerar till Tjänsten Fabric Mesh-tjänsten i Azure är Azure Resource Manager-resurser. Dessa resurser omfattar program, tjänster, hemligheter och så vidare.
- Distribuera och hantera resurser med hjälp av [Azure-portalen](https://portal.azure.com), Resource Manager-mallar eller Azure CLI/PowerShell-bibliotek.
- Konfigurera driftsövervakning och -avisering med hjälp av [Application Insights](/azure/application-insights/) (eller valfritt verktyg) för att registrera spårningar för drifts- och diagnostikdata från plattformen.
- Få diagnostikinformation för program som skickas ut från programmodellen med hjälp av [Application Insights](/azure/application-insights/) eller valfritt verktyg.
- Optimera resursanvändningen genom att ange regler för automatisk skalning för tjänster i programdefinitionen.

## <a name="mission-critical-platform-capabilities"></a>Uppdragskritiska plattformsfunktioner

Service Fabric Mesh skapar en samling av kluster som sträcker sig över [Azure-tillgänglighetszoner](/azure/availability-zones/az-overview) och/eller geopolitiska regionala gränser. Service Fabric Mesh beskriver program med en uppsättning avsikter som skala, maskinvarukrav, hållbarhetskrav och säkerhetsprinciper.  När programmet distribueras hittar Service Fabric Mesh den optimala platsen för att köra det.

Med Service Fabric Mesh kan du:

- Dra nytta av hög tillgänglighet, skalning in och ut, orkestrering, meddelandehantering, tillförlitliga meddelanden, uppgraderingar utan nedtid, hantering av säkerhet/hemligheter, katastrofåterställning, tillståndshantering, konfigurationshantering och distribuerade transaktioner.
- Välj mellan flera programmodeller när du skapar program.
- Använd plattformsfunktioner som exponeras via REST-slutpunkter genom att förbruka språkspecifika bindningar som genereras med Swagger.
- Distribuera program över [tillgänglighetszoner](/azure/availability-zones/az-overview) och flera regioner för geotillförlitlighet.
- Använd alla säkerhets- och efterlevnadsfunktioner som Azure tillhandahåller.

## <a name="next-steps"></a>Nästa steg

Det behövs bara några få steg för att distribuera ett exempelprojekt med Visual Studio. Mer information finns i på sidan om att [skapa en ASP.NET Core-webbplats](service-fabric-mesh-quickstart-dotnet-core.md). 

Få svar på [vanliga frågor](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
