---
title: Regions tillgänglighet och data placering för Azure Communication Services
description: Lär dig mer om data placering och sekretess relaterade frågor om Azure Communication Services
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6b8145666ab92432d01b2f039685afe5534f0958
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043590"
---
# <a name="region-availability-and-data-residency"></a>Regionstillgänglighet och datahemvist

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services värnar om att hjälpa våra kunder att uppfylla sina sekretess-och personliga data krav. Som utvecklare som använder kommunikations tjänster med en direkt relation med människor som använder programmet, är det möjligt att du har en kontroll enhets data. Eftersom Azure Communication Services lagrar dessa data för din räkning är vi förmodligen en processor med dessa data. Den här sidan sammanfattar hur tjänsten behåller data och hur du kan identifiera, exportera och ta bort dessa data.

## <a name="data-residency"></a>Dataplacering

När du skapar en kommunikations tjänst resurs kan du ange ett **geografiskt** objekt (inte ett Azure-datacenter). Alla data som lagras av kommunikations tjänster i vila kommer att behållas i det geografiet i ett Data Center som valts internt av kommunikations tjänsterna. Data kan dock transporteras eller bearbetas i andra geografiska områden. dessa globala slut punkter är nödvändiga för att tillhandahålla en högpresterande upplevelse med låg latens för slutanvändare oavsett var de befinner sig.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Relaterad till människa till Azure Communication Services-identiteter

Ditt program hanterar relationen mellan mänskliga användare och kommunikations tjänst identiteter. När du vill ta bort data för en mänsklig användare måste du ta bort data som berör alla kommunikations tjänst identiteter som är korrelerade för användaren.

Det finns två kategorier av kommunikations tjänst data:
- **API-data.** Dessa data skapas och hanteras av kommunikations tjänstens API: er, ett typiskt exempel är att chatta meddelanden som hanteras via chatt-API: er.
- **Azure Monitor loggar** Dessa data skapas av tjänsten och hanteras via Azure Monitor data plattform. Dessa data innehåller telemetri och mått som hjälper dig att förstå användningen av kommunikations tjänsterna. Detta hanteras inte av kommunikations tjänstens API: er.

## <a name="api-data"></a>API-data

### <a name="identities"></a>Identiteter

Azure Communication Services hanterar en katalog med identiteter, använder [DeleteIdentity](https://docs.microsoft.com/rest/api/communication/communicationidentity/delete) -API: et för att ta bort dem. Om du tar bort en identitet återkallas alla tillhör ande åtkomsttoken och deras Chat-meddelanden tas bort. Mer information om hur du tar bort en identitet [finns på den här sidan](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Med hjälp av Azure Portal-eller Azure Resource Manager-API: er med kommunikations tjänster kan du skapa personliga data. [Använd den här sidan om du vill lära dig hur du hanterar person uppgifter i Azure Resource Manager system.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-manager-personal-data)

### <a name="telephone-number-management"></a>Hantering av telefonnummer

Azure Communication Services underhåller en katalog med telefonnummer som är associerade med en kommunikations tjänst resurs. Använd dessa API: er för att hämta telefonnummer och ta bort dem:
- `Release Phone Number`

### <a name="chat"></a>Chatt

Chatt-trådar och meddelanden behålls tills de tas bort explicit. En helt inaktiv tråd tas bort automatiskt efter 30 dagar. Använd [chatt-API: er](https://docs.microsoft.com/rest/api/communication/chat/deletechatmessage/deletechatmessage) för att hämta, Visa, uppdatera och ta bort meddelanden.

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

Skickade och mottagna SMS-meddelanden bearbetas ephemerally av tjänsten och bevaras inte. 

### <a name="pstn-voice-calling"></a>PSTN röst samtal

Ljud-och videokommunikation är ephemerally som bearbetas av tjänsten och inga data behålls i din resurs förutom Azure Monitor loggar.

### <a name="internet-voice-and-video-calling"></a>Internet röst-och video samtal

Ljud-och videokommunikation är ephemerally som bearbetas av tjänsten och inga data behålls i din resurs förutom Azure Monitor loggar.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor och Log Analytics

Azure Communication Services kommer att mata in Azure Monitor loggnings information för att förstå drifts hälsan och användningen av tjänsten. Några av dessa loggar är identiteter och telefonnummer för kommunikations tjänsten som fält data. För att ta bort potentiellt personliga data [använder du dessa procedurer för Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt). Du kanske också vill konfigurera [standard kvarhållningsperioden för Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

## <a name="additional-resources"></a>Ytterligare resurser

- [Azure Data subject-begäranden för GDPR och CCPA](https://docs.microsoft.com/microsoft-365/compliance/gdpr-dsr-azure?view=o365-worldwide&preserve-view=true)
- [Microsoft Säkerhetscenter](https://www.microsoft.com/trust-center/privacy/data-location)
- [Azure-interaktiv karta – var är mina kund data?](https://azuredatacentermap.azurewebsites.net/)