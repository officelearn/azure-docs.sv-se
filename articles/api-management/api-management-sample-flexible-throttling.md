---
title: Advanced request throttling with Azure API Management (Avancerad begränsning av begäranden med Azure API Management)
description: Lär dig hur du skapar och tillämpar flexibla principer för kvot- och hastighetsbegränsning med Azure API Management.
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
ms.openlocfilehash: 467d9cee74567fc0d19031773415675ae7c51818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71066760"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Advanced request throttling with Azure API Management (Avancerad begränsning av begäranden med Azure API Management)
Att kunna begränsa inkommande begäranden är en viktig roll för Azure API Management. Antingen genom att kontrollera antalet begäranden eller de totala begäranden/data som överförs, tillåter API Management API-leverantörer att skydda sina API:er från missbruk och skapa värde för olika API-produktnivåer.

## <a name="product-based-throttling"></a>Produktbaserad begränsning
Hittills har hastighetsbegränsningsfunktionerna begränsats till att begränsas till en viss produktprenumeration, definierad i Azure-portalen. Detta är användbart för API-providern att tillämpa gränser för utvecklare som har registrerat sig för att använda deras API, men det hjälper inte, till exempel, att begränsa enskilda slutanvändare av API. Det är möjligt att för en användare av utvecklarens program att förbruka hela kvoten och sedan hindra andra kunder till utvecklaren från att kunna använda programmet. Dessutom kan flera kunder som kan generera en stor mängd begäranden begränsa åtkomsten till tillfälliga användare.

## <a name="custom-key-based-throttling"></a>Anpassad nyckelbaserad begränsning

> [!NOTE]
> Principerna `rate-limit-by-key` `quota-by-key` och är inte tillgängliga när de finns på förbrukningsnivån för Azure API Management. 

De nya [policyerna för hastighetsbegränsning för nyckel](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) och [kvot för nyckel](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) ger en mer flexibel lösning på trafikkontrollen. Med de här nya principerna kan du definiera uttryck för att identifiera de nycklar som används för att spåra trafikanvändning. Hur detta fungerar är lättast illustreras med ett exempel. 

## <a name="ip-address-throttling"></a>Begränsning av IP-adress
Följande principer begränsar en enskild klient-IP-adress till endast 10 samtal varje minut, med totalt 1 000 000 samtal och 10 000 kilobyte bandbredd per månad. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Om alla klienter på Internet använde en unik IP-adress kan detta vara ett effektivt sätt att begränsa användningen av användare. Det är dock troligt att flera användare delar en enda offentlig IP-adress på grund av att de ansluter till Internet via en NAT-enhet. Trots detta, för API: er som tillåter `IpAddress` oautentiserade åtkomst kan det bästa alternativet.

## <a name="user-identity-throttling"></a>Begränsning av användaridentitet
Om en slutanvändare autentiseras kan en begränsningsnyckel genereras baserat på information som unikt identifierar användaren.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Det här exemplet visar hur du extraherar auktoriseringshuvudet, konverterar det till `JWT` objekt och använder ämnet för token för att identifiera användaren och använda det som hastighetsbegränsande nyckel. Om användaridentiteten lagras `JWT` som en av de andra anspråken kan det värdet användas i dess ställe.

## <a name="combined-policies"></a>Kombinerade policyer
Även om de nya begränsningsprinciperna ger mer kontroll än de befintliga begränsningsprinciperna finns det fortfarande ett värde som kombinerar båda funktionerna. Begränsning av produktprenumerationsnyckel ([Begränsa samtalsfrekvens per prenumeration](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) och Ange [användningskvot per prenumeration)](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)är ett bra sätt att aktivera intäktsgenerering av ett API genom att debitera baserat på användningsnivåer. Den finare korniga kontrollen av att kunna begränsa av användaren kompletterar varandra och förhindrar att en användares beteende försämrar upplevelsen av en annan. 

## <a name="client-driven-throttling"></a>Klientdriven begränsning
När begränsningsnyckeln definieras med hjälp av ett [principuttryck](/azure/api-management/api-management-policy-expressions)är det API-providern som väljer hur begränsningen ska begränsas. En utvecklare kanske vill styra hur de betygsätter begränsa sina egna kunder. Detta kan aktiveras av API-providern genom att införa en anpassad rubrik så att utvecklarens klientprogram kan kommunicera nyckeln till API: et.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Detta gör det möjligt för utvecklarens klientprogram att välja hur de vill skapa den hastighetsbegränsande nyckeln. Klientutvecklarna kan skapa sina egna kursnivåer genom att tilldela uppsättningar nycklar till användare och rotera nyckelanvändningen.

## <a name="summary"></a>Sammanfattning
Azure API Management ger hastighet och offertbegränsning för att både skydda och lägga till värde till din API-tjänst. De nya begränsningsprinciperna med anpassade omfångsregler gör att du kan få finare kornig kontroll över dessa principer så att dina kunder kan skapa ännu bättre program. Exemplen i den här artikeln visar användningen av dessa nya principer genom att skapa hastighetsbegränsande nycklar med klient-IP-adresser, användaridentitet och klientgenererade värden. Det finns dock många andra delar av meddelandet som kan användas, till exempel användaragent, URL-sökvägsfragment, meddelandestorlek.

## <a name="next-steps"></a>Nästa steg
Vänligen ge oss din feedback som en GitHub fråga för detta ämne. Det skulle vara bra att höra om andra potentiella nyckelvärden som har varit ett logiskt val i dina scenarier.

