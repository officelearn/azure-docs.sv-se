---
title: Avancerade begäran begränsning med Azure API Management
description: Lär dig hur du skapar och använder flexibla kvoten och begränsa principer med Azure API Management hastighet.
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
ms.openlocfilehash: c7fcbd57021134631e9f10dcbb2d40e4c130af02
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29800031"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Avancerade begäran begränsning med Azure API Management
Att kunna begränsa inkommande begäranden är en viktig roll i Azure API Management. Antingen genom att kontrollera antalet förfrågningar eller totalt antal begäranden/överförda data kan API Management-API-leverantörer att skydda sina API: er från missbruk och skapa värde för olika nivåer för API-produkten.

## <a name="product-based-throttling"></a>Produkt-baserade begränsning
Hittills, frekvensen har bandbreddsbegränsning funktioner begränsad till är begränsad till en viss produkt-prenumeration som definierats i Azure-portalen. Detta är användbart för API-providern kan tillämpa begränsningar på utvecklare som har registrerat dig att använda sitt API, men det hjälper inte, till exempel i begränsning enskilda användare i API. Det är möjligt att för enkel användaren av utvecklarens program att använda hela kvoten och sedan förhindra andra kunder som utvecklare kan använda programmet. Flera kunder som kan generera en stor mängd begäranden kan också begränsa åtkomsten till tillfällig användare.

## <a name="custom-key-based-throttling"></a>Anpassade nyckelbaserad begränsning
Den nya [hastighet gränsen av nyckeln](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) och [kvoten av nyckeln](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) principerna förser dig med en mer flexibel lösning för att kontrollera trafik. Dessa nya principer kan du definiera uttryck för att identifiera de nycklar som används för att spåra användningen av trafik. Hur detta fungerar illustreras easiest med ett exempel. 

## <a name="ip-address-throttling"></a>Begränsning av IP-adress
Följande principer begränsa en enskild klient-IP-adress till endast 10 anrop i minuten, med totalt 1 000 000 anrop och 10 000 kB bandbredd per månad. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Om en unik IP-adress används för alla klienter på Internet, kan det vara ett effektivt sätt för att begränsa användningen av användaren. Det är dock troligt att flera användare delar en offentlig IP-adress på grund av dem åtkomst till Internet via en NAT-enhet. Trots detta för API: er som tillåter obehörig åtkomst av `IpAddress` kan vara det bästa alternativet.

## <a name="user-identity-throttling"></a>Begränsning av identitet
Om en användare autentiseras kan sedan en bandbreddsbegränsning nyckel genereras baserat på information som unikt identifierar den användaren.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Det här exemplet visar hur du extrahera Authorization-huvud, konvertera den till `JWT` objekt och använda föremål för token för att identifiera användaren och använda det som den hastighet som begränsar nyckel. Om användarens identitet lagras i den `JWT` som en av de andra anspråk, sedan detta värde kan användas i dess ställe.

## <a name="combined-policies"></a>Kombinerade principer
Även om de nya bandbreddsbegränsning principerna ger mer kontroll än de befintliga bandbreddsbegränsning principerna, finns det fortfarande värde kombinera båda funktioner. Begränsning av prenumeration produktnyckel ([gränsen anropet frekvensen av prenumerationen](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) och [kvot för uppsättningen av prenumerationen](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) är ett bra sätt att aktivera monetizing för en API genom att dra baserat på användning nivåer. Avancerat hjälpmedel metataggkontroll kontroll av att kunna begränsa av användare kompletterar och förhindrar att en användarbeteende försämring upplevelse av en annan. 

## <a name="client-driven-throttling"></a>Klienten drivs begränsning
När nyckeln bandbreddsbegränsning definieras med hjälp av en [principuttryck](https://msdn.microsoft.com/library/azure/dn910913.aspx), så är det den API-provider som väljer hur begränsningen är begränsad. Men en utvecklare kanske vill styra hur de hastighetsbegränsning sina egna kunder. Detta kan aktiveras av API-providern genom att introducera en anpassad rubrik så att utvecklarens klientprogram att kommunicera nyckeln till API: et.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Detta gör att utvecklarens klientprogram att välja hur de vill skapa den hastighet som begränsar nyckel. Klient-utvecklare kan skapa sina egna hastighet nivåer genom att tilldela användare uppsättningar av nycklar och rotera nyckelanvändningen.

## <a name="summary"></a>Sammanfattning
Azure API Management ger hastighet och citattecken begränsning för både skydd och Lägg till värde i API-tjänsten. Nya bandbreddsbegränsning principer med anpassade reglerna Tillåt avancerat hjälpmedel metataggkontroll kontroll över dessa principer för att aktivera dina kunder att bygga program ännu bättre. Exemplen i den här artikeln visar användningen av dessa nya principer med tillverkning som begränsar nycklar med klienternas IP-adresser, användar-ID och klienten genererade värden. Det finns emellertid många andra delar av meddelandet som kan användas som användaragent, URL-sökväg fragment, meddelandestorlek.

## <a name="next-steps"></a>Nästa steg
Ge oss din feedback i Disqus-tråden för det här avsnittet. Det är bra att läsa om andra potentiella nyckelvärden som har ett logiskt val i dina scenarier.

