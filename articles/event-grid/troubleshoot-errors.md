---
title: Azure Event Grid – fel söknings guide
description: Den här artikeln innehåller en lista över felkoder, fel meddelanden, beskrivningar och rekommenderade åtgärder.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1dd464339e7654f8886224ff07cf368b4724ff82
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041391"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Felsöka Azure Event Grid fel
Den här fel söknings guiden innehåller en lista över Azure Event Grid felkoder, fel meddelanden, beskrivningar och rekommenderade åtgärder som du bör vidta när du får dessa fel. 

## <a name="error-code-400"></a>Felkod: 400
| Felkod | Felmeddelande | Description | Rekommendation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | Ämnes namnet måste vara mellan 3 och 50 tecken långt. | Namnet på den anpassade ämnes namnet ska vara mellan 3 och 50 tecken långt. Endast alfanumeriska bokstäver, siffror och tecknen '-' är tillåtna i ämnes namnet. Namnet får inte heller börja med följande reserverade ord: <ul><li>Utforskaren</li><li>EventGrid</li><li>Säker</li></ul> | Välj ett annat ämnes namn som följer namn kraven för ämnet. |
| HttpStatusCode. BadRequest<br/>400 | Domän namnet måste vara mellan 3 och 50 tecken långt. | Domän namnets längd måste vara mellan 3 och 50 tecken långt. Endast alfanumeriska bokstäver, siffror och tecknen '-' är tillåtna i domän namnet. Namnet får inte heller börja med följande reserverade ord:<ul><li>Utforskaren</li><li>EventGrid</li><li>Säker</li> | Välj ett annat domän namn som följer domän namns kraven. |
| HttpStatusCode. BadRequest<br/>400 | Ogiltig förfallo tid. | Förfallo tiden för händelse prenumerationen avgör när händelse prenumerationen ska tas ur bruk. Värdet måste vara ett giltigt DateTime-värde i framtiden.| Se till att förfallo tiden för händelse prenumerationen är i ett giltigt DateTime-format och att den har angetts vara i framtiden. |

## <a name="error-code-409"></a>Felkod: 409
| Felkod | Felmeddelande | Description | Rekommenderad åtgärd |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. konflikt <br/>409 | Det finns redan ett ämne med det angivna namnet. Välj ett annat ämnes namn.   | Namnet på den anpassade artikeln bör vara unikt i en enda Azure-region för att säkerställa en korrekt publicerings åtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för ämnet. |
| HttpStatusCode. konflikt <br/> 409 | Den angivna domänen finns redan. Välj ett annat domän namn. | Domän namnet bör vara unikt i en enda Azure-region för att säkerställa en korrekt publicerings åtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för domänen. |
| HttpStatusCode. konflikt<br/>409 | Kvot gränsen har uppnåtts. Mer information om dessa gränser finns [Azure Event Grid gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Varje Azure-prenumeration har en gräns för antalet Azure Event Grid-resurser som den kan använda. En eller flera av kvoten har överskridits och inga fler resurser kunde skapas. |    Kontrol lera den aktuella resurs användningen och ta bort alla som inte behövs. Om du fortfarande behöver öka kvoten skickar du ett e-postmeddelande till [aeg@microsoft.com](mailto:aeg@microsoft.com) med det exakta antalet resurser som behövs. |

## <a name="error-code-403"></a>Felkod: 403

| Felkod | Felmeddelande | Description | Rekommenderad åtgärd |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. förbjuden <br/>403 | Publicering till {Topic/Domain} av klienten {IpAddress} nekas på grund av IP-filter. | Avsnittet eller domänen har konfigurerade regler för IP-brandvägg och åtkomst begränsas endast till konfigurerade IP-adresser. | Lägg till IP-adressen i IP-brandväggens regler, se [Konfigurera IP-brandvägg](configure-firewall.md) |
| HttpStatusCode. förbjuden <br/> 403 | Publicering till {Topic/Domain} av klienten avvisas eftersom begäran kom från privat slut punkt och ingen matchande privat slut punkts anslutning hittades för resursen. | Ämnet eller domänen har konfigurerade privata slut punkter och publicerings förfrågan kom från en privat slut punkt som inte har kon figurer ATS/godkänts. | Konfigurera en privat slut punkt för ämnet/domänen. [Konfigurera privata slutpunkter](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>Felsöka verifiering av händelse prenumeration

Om du ser ett fel meddelande, till exempel `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , innebär det att det är ett fel i validerings hand skakningen när händelse prenumerationen skapas. Kontrol lera följande aspekter för att lösa det här felet:

- Gör ett HTTP-inlägg till webhook-URL: en med ett [exempel på SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) för begäran med Postman eller sväng eller liknande verktyg.
- Om webhooken implementerar mekanismen för synkron verifierings hand skakning kontrollerar du att ValidationCode returneras som en del av svaret.
- Om webhooken implementerar mekanismen för asynkron verifierings hand skakning kontrollerar du att HTTP-inlägget returnerar 200 OK.
- Om webhooken returnerar 403 (förbjuden) i svaret kontrollerar du att webhooken ligger bakom en Azure Application Gateway eller brand vägg för webbaserade program. Om så är fallet måste du inaktivera dessa brand Väggs regler och göra ett HTTP-inlägg igen:

  920300 (begäran saknar ett accept-huvud, vi kan åtgärda detta)

  942430 (begränsad SQL Character-avvikelse identifiering (argument): # av specialtecken har överskridits (12))

  920230 (flera URL-kodning identifierade)

  942130 (SQL-injektering: SQL-Tautology har identifierats.)

  931130 (möjligt RFI-angrepp (Remote File inkludering) = Off-Domain referens/länk)


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/). 
