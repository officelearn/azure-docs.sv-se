---
title: Autentisera händelse leverans till händelse hanterare (Azure Event Grid)
description: I den här artikeln beskrivs olika sätt att autentisera leverans till händelse hanterare i Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d48930ac9cfdd1ecd3e7d6c64067d5389323f8bc
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119947"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Autentisera händelse leverans till händelse hanterare (Azure Event Grid)
Den här artikeln innehåller information om hur du autentiserar händelse leverans till händelse hanterare. Den visar också hur du skyddar webhook-slutpunkter som används för att ta emot händelser från Event Grid med Azure Active Directory (Azure AD) eller en delad hemlighet.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Använd systemtilldelade identiteter för händelse leverans
Du kan aktivera en systemtilldelad hanterad identitet för ett ämne eller en domän och använda identiteten för att vidarebefordra händelser till destinationer som stöds, till exempel Service Bus köer och ämnen, Event Hub och lagrings konton.

Gör så här: 

1. Skapa ett ämne eller en domän med en tilldelad identitet eller uppdatera ett befintligt ämne eller en befintlig domän för att aktivera identitet. 
1. Lägg till identiteten i en lämplig roll (till exempel Service Bus data avsändare) på målet (till exempel en Service Bus kö).
1. När du skapar händelse prenumerationer kan du aktivera användningen av identiteten för att leverera händelser till målet. 

Detaljerade steg-för-steg-instruktioner finns i [händelse leverans med en hanterad identitet](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autentisera slut punkter för händelse leverans till webhook
I följande avsnitt beskrivs hur du autentiserar händelse leverans till webhook-slutpunkter. Du måste använda en mekanism för validerings hand skakning oavsett vilken metod du använder. Mer information finns i avsnittet om att [leverera webhook-händelser](webhook-event-delivery.md) . 


### <a name="using-azure-active-directory-azure-ad"></a>Använda Azure Active Directory (Azure AD)
Du kan skydda webhook-slutpunkten som används för att ta emot händelser från Event Grid med hjälp av Azure AD. Du måste skapa ett Azure AD-program, skapa en roll-och tjänst-huvudobjekt i ditt program som auktoriserar Event Grid och konfigurera händelse prenumerationen så att den använder Azure AD-programmet. Lär dig hur du [konfigurerar Azure Active Directory med event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Använda klient hemlighet som frågeparameter
Du kan också skydda webhook-slutpunkten genom att lägga till frågeparametrar i URL: en för webhook-målet som anges som en del av att skapa en händelse prenumeration. Ange att en av frågeparametrar ska vara en klient hemlighet, till exempel en [åtkomsttoken](https://en.wikipedia.org/wiki/Access_token) eller en delad hemlighet. Event Grid-tjänsten innehåller alla frågeparametrar i varje händelse leverans förfrågan till webhooken. Webhook-tjänsten kan hämta och verifiera hemligheten. Om klient hemligheten uppdateras måste händelse prenumerationen också uppdateras. Undvik leverans problem under den här hemliga rotationen genom att göra så att webhooken godkänner både gamla och nya hemligheter under en begränsad tid innan du uppdaterar händelse prenumerationen med den nya hemligheten. 

Eftersom frågeparametrar kan innehålla klient hemligheter hanteras de med extra noggrannhet. De lagras som krypterade och är inte tillgängliga för tjänst operatörer. De loggas inte som en del av tjänst loggarna/spårningarna. När du hämtar egenskaperna för händelse prenumerationen returneras inte mål frågans parametrar som standard. Till exempel: [--include-fullständig-Endpoint-URL-](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parameter ska användas i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Mer information om att leverera händelser till Webhooks finns i avsnittet om [leverans av webhook-händelser](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid stöder endast **https** webhook-slutpunkter. 


## <a name="next-steps"></a>Nästa steg
Se [autentisera publicerings klienter](security-authenticate-publishing-clients.md) för att lära dig mer om att autentisera klienter som publicerar händelser till ämnen eller domäner. 
