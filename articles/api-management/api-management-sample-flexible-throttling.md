---
title: Avancerad begränsning av förfrågningar med Azure API Management
description: Lär dig hur du skapar och tillämpar flexibla kvot-och hastighets begränsnings principer med Azure API Management.
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
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066760"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Avancerad begränsning av förfrågningar med Azure API Management
Att kunna begränsa inkommande begär Anden är en viktig roll i Azure API Management. Antingen genom att kontrol lera antalet begär Anden eller de totala begär Anden/data som överförs, tillåter API Management att API-providers skyddar sina API: er från missbruk och skapar värden för olika API-produktsortiment.

## <a name="product-based-throttling"></a>Produkt-baserad begränsning
Hittills har frekvens begränsnings funktionerna begränsats till en viss produkt prenumeration, som definieras i Azure Portal. Detta är användbart för API-providern för att tillämpa gränser på de utvecklare som har registrerat sig för att använda deras API, men den hjälper inte till exempel att begränsa enskilda slutanvändare av API: et. Det är möjligt att användare av utvecklarens program kan använda hela kvoten och sedan hindra andra utvecklare från att kunna använda programmet. Dessutom kan flera kunder som kan generera en stor mängd begär Anden begränsa åtkomsten till tillfälliga användare.

## <a name="custom-key-based-throttling"></a>Anpassad nyckelbaserad begränsning

> [!NOTE]
> \- `rate-limit-by-key` Och`quota-by-key` -principerna är inte tillgängliga när du arbetar på användnings nivån i Azure API Management. 

De nya principerna för [hastighets begränsning](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) och [kvot-till-nyckel](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) ger en mer flexibel lösning för trafik kontroll. Med de här nya principerna kan du definiera uttryck för att identifiera de nycklar som används för att spåra användning av trafiken. Sättet det här fungerar är enkelt att illustrera med ett exempel. 

## <a name="ip-address-throttling"></a>IP-adress begränsning
Följande principer begränsar en enskild klients IP-adress till endast 10 anrop varje minut, med totalt 1 000 000 anrop och 10 000 KB bandbredd per månad. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Om alla klienter på Internet använder en unik IP-adress kan det vara ett effektivt sätt att begränsa användningen av användare. Det är dock troligt att flera användare delar en enda offentlig IP-adress på grund av att de ansluter till Internet via en NAT-enhet. Trots detta `IpAddress` kan det bästa alternativet för API: er som tillåter oautentiserad åtkomst.

## <a name="user-identity-throttling"></a>Begränsning av användar identitet
Om en slutanvändare autentiseras kan en begränsnings nyckel genereras baserat på information som unikt identifierar användaren.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Det här exemplet visar hur du extraherar Authorization-huvudet, konverterar `JWT` det till-objekt och använder ämnet för token för att identifiera användaren och använda den som begränsnings nyckel. Om användar identiteten lagras i `JWT` som en av de andra anspråken, kan det värdet användas i sitt ställe.

## <a name="combined-policies"></a>Kombinerade principer
Även om de nya begränsnings principerna ger mer kontroll än befintliga begränsnings principer finns det fortfarande värde som kombinerar båda funktionerna. Begränsning av produkt prenumerations nyckel ([begränsa anrops frekvensen per](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) prenumeration och [Ange användnings kvoten för prenumerationen](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) är ett bra sätt att aktivera monetarisering av ett API genom att debiteras baserat på användnings nivåer. Den bättre korniga kontrollen av att kunna begränsas av användaren är kompletterande och förhindrar en användares beteende från att försämra upplevelsen av en annan. 

## <a name="client-driven-throttling"></a>Klient driven begränsning
När begränsnings nyckeln definieras med ett [princip uttryck](/azure/api-management/api-management-policy-expressions)är det den API-provider som väljer hur begränsningen är begränsad. En utvecklare kan dock vilja styra hur de begränsar sina egna kunder. Detta kan aktive ras av API-providern genom att introducera en anpassad rubrik så att utvecklarens klient program kan kommunicera nyckeln till API: et.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Detta gör det möjligt för utvecklarens klient program att välja hur du vill skapa en hastighets begränsnings nyckel. Klient utvecklarna kan skapa sina egna pris nivåer genom att allokera uppsättningar nycklar till användare och rotera nyckel användningen.

## <a name="summary"></a>Sammanfattning
Azure API Management ger pris-och offert begränsning till både skydda och lägga till värde i din API-tjänst. Med de nya begränsnings principerna med anpassade omfångs regler kan du få bättre kontroll över principerna så att kunderna kan skapa ännu bättre program. I exemplen i den här artikeln demonstreras användningen av dessa nya principer med hjälp av tillverknings frekvens begränsar nycklar med klientens IP-adresser, användar identitet och klienter som genererar värden. Det finns dock många andra delar av meddelandet som kan användas, till exempel användar agent, fragment för URL-sökvägar, meddelande storlek.

## <a name="next-steps"></a>Nästa steg
Ge oss din feedback som ett GitHub-problem för det här ämnet. Det skulle vara bra att höra om andra potentiella nyckel värden som har varit ett logiskt val i dina scenarier.

