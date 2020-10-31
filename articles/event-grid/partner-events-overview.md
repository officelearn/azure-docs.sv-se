---
title: Azure Event Grid – partner händelser
description: Skicka händelser från tredjeparts Event Grid SaaS-och PaaS-partner direkt till Azure-tjänster med Azure Event Grid.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 87d1d40b3696229344b0b5c20d06d9d993a514a4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103148"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Partner händelser i Azure Event Grid (förhands granskning)
Med funktionen **partner händelser** kan en SaaS-provider från tredje part publicera händelser från sina tjänster så att de blir tillgängliga för konsumenter som kan prenumerera på dessa händelser. Det ger en första parts upplevelse till händelse källor från tredje part genom att exponera en [ämnes](concepts.md#topics) typ, ett **partner ämne** , som prenumeranter använder för att använda händelser. Den erbjuder även en ren pub-sub-modell genom att åtskilja problem och ägarskap av resurser som används av händelse utgivare och prenumeranter.

> [!NOTE]
> Om du är nybörjare på att använda Event Grid, se [Översikt](overview.md), [begrepp](concepts.md)och [händelse hanterare](event-handlers.md).

## <a name="what-is-partner-events-to-a-publisher"></a>Vad är partner händelser till en utgivare?
I en händelse utgivare gör funktionen partner händelser det möjligt för utgivare att utföra följande uppgifter:

- Publicera sina händelse källor till Event Grid
- Skapa ett namn område (slut punkt) som de kan publicera händelser
- Skapa partner ämnen i Azure som prenumeranter äger och använder för att konsumera händelser

## <a name="what-is-partner-events-to-a-subscriber"></a>Vad är partner händelser till en prenumerant?
Till en prenumerant kan du använda funktionen partner händelser för att skapa partner ämnen i Azure för att konsumera händelser från händelse källor från tredje part. Händelse förbrukningen realiseras genom att skapa händelse prenumerationer som skickar (push) händelser till en prenumerants händelse hanterare.

## <a name="why-should-i-use-partner-events"></a>Varför ska jag använda partner händelser?
Du kanske vill använda partner händelser om du har ett eller flera av följande krav.

### <a name="for-publishers"></a>För utgivare

- Du vill ha en mekanism för att göra dina händelser tillgängliga på Azure. Användarna kan filtrera och dirigera dessa händelser med hjälp av partner ämnen och händelse prenumerationer som de äger och hanterar. Du kan använda andra integrerings metoder, till exempel [ämnen](custom-topics.md) och [domäner](event-domains.md). Men de tillåter inte en ren uppdelning av resurs (partner ämnen) ägande, hantering och fakturering mellan utgivare och prenumeranter. Den här metoden ger också mer intuitiv användar upplevelse som gör det enkelt att identifiera och använda partner ämnen.
- Du vill publicera händelser till en enda slut punkt, slut punktens slut punkt. Och du vill kunna filtrera dessa händelser så att endast en delmängd av dem är tillgänglig. 
- Du vill ha insyn i mått som rör publicerade händelser.
- Du vill använda [Cloud events 1,0](https://cloudevents.io/) -schema för dina händelser.

### <a name="for-subscribers"></a>För prenumeranter

- Du vill prenumerera på händelser från [tredje parts utgivare](#available-third-party-event-publishers) och hantera händelser med hjälp av händelse hanterare som finns på Azure eller någon annan stans.
- Du vill dra nytta av den omfattande uppsättningen funktioner för Routning och [mål/händelse hanterare](overview.md#event-handlers) för att bearbeta händelser från källor från tredje part. 
- Du vill implementera löst sammankopplade arkitekturer där prenumeranten/händelse hanteraren inte är medveten om förekomsten av den meddelande Broker som används. 
- Du behöver en elastisk push-leverans med stöd för att skicka nya försök och minst en gång.
- Du vill använda [Cloud events 1,0](https://cloudevents.io/) -schema för dina händelser. 


## <a name="available-third-party-event-publishers"></a>Tillgängliga händelse utgivare från tredje part
En tredje parts händelse Utgivare måste gå igenom en [onboarding-process](partner-onboarding-overview.md) innan en prenumerant kan börja använda sina händelser. 

Om du är prenumerant och vill att en tjänst från tredje part ska exponera sina händelser via Event Grid 

### <a name="auth0"></a>Auth0
**Auth0** är den första partner utgivare som är tillgänglig. Du kan skapa ett [Auth0-partner ämne](auth0-overview.md) för att ansluta dina Auth0-och Azure-konton. Med den här integreringen kan du reagera på, logga och övervaka Auth0-händelser i real tid. Information om hur du provar finns i [integrera Azure Event Grid med Auto0](auth0-how-to.md)

Om du vill att en tjänst från tredje part ska exponera sina händelser via Event Grid kan du skicka in idén på [användarens röst Portal](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Resurser som hanteras av händelse utgivare
Händelse utgivare skapar och hanterar följande resurser:

### <a name="partner-registration"></a>Partner registrering
En registrering innehåller allmän information som rör en utgivare. Den definierar en typ av partner ämne som visas i Azure Portal som ett alternativ när användare försöker skapa ett partner ämne. En utgivare kan exponera mer än en eller flera partner ämnes typer för att passa behoven hos sina prenumeranter. Det innebär att en utgivare kan skapa separata registreringar (ämnes typer för partner) för händelser från olika tjänster. För personal tjänsten personal kan du till exempel definiera ett partner ämne för händelser som till exempel anställd, personal som marknadsförs och anställd till vänster i företaget. 

Tänk på följande punkter:

- Endast Azure-godkända partner registreringar visas. 
- Registreringar är globala. Det innebär att de inte är kopplade till en viss Azure-region.
- En registrering är en valfri resurs. Men vi rekommenderar att du (som utgivare) skapar en registrering. Det gör att användarna kan identifiera dina ämnen på sidan **skapa partner ämne** i [Azure Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic). Sedan kan användaren välja händelse typer (till exempel anställd, anställd till vänster och så vidare) när de skapar händelse prenumerationer.

### <a name="namespace"></a>Namnområde
Precis som med [anpassade ämnen](custom-topics.md) och [domäner](event-domains.md)är ett partner namn område en regional slut punkt för att publicera händelser. Det är via namn områden som utgivare skapar och hanterar händelse kanaler. En namnrymd fungerar också som behållar resurs för händelse kanaler.

### <a name="event-channels"></a>Händelse kanaler
En händelse kanal är en speglad resurs till ett partner ämne. När en utgivare skapar en händelse kanal i utgivarens Azure-prenumeration skapar den också ett partner ämne under en prenumerants Azure-prenumeration. De åtgärder som utförs mot en händelse kanal (förutom GET) kommer att tillämpas på motsvarande prenumerations partner ämne, även borttagning. Men endast partner ämnen är den typ av resurser som prenumerationer och händelse leverans kan konfigureras på.

## <a name="resources-managed-by-subscribers"></a>Resurser som hanteras av prenumeranter 
Prenumeranter kan använda partner ämnen som definieras av en utgivare och det är den enda typen av resurs som de ser och hanterar. När ett partner ämne har skapats kan en prenumerant skapa händelse prenumerationer för att definiera filter regler till [destinationer/händelse hanterare](overview.md#event-handlers). Till prenumeranter ger ett partner ämne och tillhör ande händelse prenumerationer samma omfattande funktioner som [anpassade ämnen](custom-topics.md) och dess relaterade prenumeration (er) med en viktig skillnad: partner ämnen stöder bara [moln händelser 1,0-schemat](cloudevents-schema.md), vilket ger en större uppsättning funktioner än andra scheman som stöds.

## <a name="pricing"></a>Prissättning
Partner ämnen debiteras genom antalet åtgärder som utförs när du använder Event Grid. Mer information om alla typer av åtgärder som används som grund för fakturering och detaljerad pris information finns i [Event Grid prissättning](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="limits"></a>Gränser
Detaljerad information om begränsningarna för partner ämnen finns i [Event Grid tjänst begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) .


## <a name="next-steps"></a>Nästa steg

- [Auth0-partner ämne](auth0-overview.md)
- [Så här använder du Auth0-partner ämnet](auth0-how-to.md)
- [Bli en Event Grid-partner](partner-onboarding-overview.md)