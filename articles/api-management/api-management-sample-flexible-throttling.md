---
title: Avancerad begränsning av förfrågningar med Azure API Management
description: Lär dig hur du skapar och använder frekvensen principerna med Azure API Management som begränsar och flexibla kvot.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783796"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Avancerad begränsning av förfrågningar med Azure API Management
Att kunna begränsa inkommande begäranden är en viktig roll i Azure API Management. Antingen genom att styra frekvensen för begäranden eller totalt antal begäranden/överförda data, API Management kan API-leverantörer att skydda sina API: er från missbruk och skapa värde för olika nivåer för API-produkt.

## <a name="product-based-throttling"></a>Produkt-baserade begränsning
Uppdaterade frekvensen har begränsning funktioner begränsad till att tillhöra en viss produktprenumeration som definierats i Azure-portalen. Detta är användbart för API-leverantör att anpassa begränsningar på utvecklare som har registrerat dig för att använda sitt API, men det hjälper inte, till exempel i enskilda användare i API-begränsning. Det är möjligt att för enskilda användare i utvecklarens program att använda hela kvoten och sedan förhindra andra kunder i utvecklarens från att kunna använda programmet. Flera kunder som kan generera ett stort antal begäranden kan också begränsa åtkomsten till tillfälliga användare.

## <a name="custom-key-based-throttling"></a>Anpassad nyckel baserat begränsning
Den nya [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) och [kvot-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) principerna förser dig med en mer flexibel lösning för att kontrollera trafik. Dessa nya principer kan du definiera uttryck för att identifiera de nycklar som används för att spåra användningen av trafik. Hur detta fungerar illustreras easiest med ett exempel. 

## <a name="ip-address-throttling"></a>Begränsning av IP-adress
Följande principer begränsar en enda klient-IP-adress till endast 10 anrop varje minut, med högst 1 000 000 anrop och 10 000 kilobyte för bandbredd per månad. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Om alla klienter på Internet används en unik IP-adress, kan det vara ett effektivt sätt för att begränsa användningen av användaren. Dock är det troligt att flera användare delar en enda offentlig IP-adress på grund av dem åtkomst till Internet via en NAT-enhet. Trots detta för API: er som tillåter oautentiserad åtkomst den `IpAddress` kan vara det bästa alternativet.

## <a name="user-identity-throttling"></a>Begränsning av identitet
Om en användare autentiseras, sedan genereras en begränsning nyckel baserat på information som unikt identifierar den användaren.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Det här exemplet visar hur du extrahera auktoriseringsrubriken, konvertera den till `JWT` objektet och använda ämnesraden av token för att identifiera användaren och använda det som den hastighet som begränsar nyckel. Om användar-ID lagras i den `JWT` som en av de andra anspråk, sedan detta värde kan användas i dess ställe.

## <a name="combined-policies"></a>Kombinerade principer
Även om de nya begränsningar principerna ger mer kontroll än de befintliga principerna för begränsning, finns det fortfarande värdet kombinera båda funktioner. Begränsning av prenumeration produktnyckel ([begränsa anropsfrekvensen per prenumeration](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) och [ange användningskvot per prenumeration](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) är ett bra sätt att tjäna pengar i ett API genom att debitera baserat på användningsnivå. Den störrre kontrollen för att kunna begränsa av användare är kompletterande och förhindrar att en användares beteende försämra upplevelsen av en annan. 

## <a name="client-driven-throttling"></a>Klienten driven begränsning
När nyckeln begränsning har definierats med hjälp av en [principuttryck](/azure/api-management/api-management-policy-expressions), så är det den API-provider som väljer hur begränsningen är begränsad. Men en utvecklare vara bra att styra hur de hastighetsbegränsning sina egna kunder. Detta kan aktiveras av API-providern genom att introducera en anpassad rubrik så att utvecklarens klientprogram att kommunicera nyckeln till API: et.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

På så sätt kan utvecklarens klientprogram för att välja hur de vill skapa den hastighet som begränsar nyckel. Klient-utvecklare kan skapa sina egna rate-nivåer genom att tilldela uppsättningar med nycklar till användare och rotera nyckelanvändningen.

## <a name="summary"></a>Sammanfattning
Azure API Management ger hastighet och citattecken begränsning för att skydda såväl mervärde till din API-tjänst. Nya principer för begränsning med anpassade reglerna kan du störrre kontroll över dessa principer så att dina kunder att skapa ännu bättre program. Exemplen i den här artikeln demonstrerar användningen av dessa nya principer som begränsar nycklar med klientens IP-adresser, användar-ID och klienten genererade värden för tillverkning frekvensbegränsningen. Men finns det många andra delar av det meddelande som kan användas som användaragent, URL-sökväg fragment, meddelandestorlek.

## <a name="next-steps"></a>Nästa steg
Ge oss din feedback i Disqus-tråden för det här avsnittet. Det vore bra och få information om andra potentiella nyckelvärden som har ett logiskt val i dina scenarier.

