---
title: Brand vägg för Azure-webbprogram och Azure Policy
description: Azure Web Application Firewall (WAF) kombinerat med Azure Policy kan hjälpa organisations standarder och utvärdera kompatibilitet för WAF-resurser
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 12ad18edbb434bdfaec2ae817ea079a843661ef6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111360"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Brand vägg för Azure-webbprogram och Azure Policy

Azure Web Application-brandväggen (WAF) kombinerat med Azure Policy kan hjälpa organisations standarder och utvärdera kompatibiliteten för WAF-resurser. Azure policy är ett styrnings verktyg som ger en sammanställd vy för att utvärdera miljöns övergripande tillstånd, med möjlighet att öka detalj nivån för att öka detalj nivån per resurs, per princip. Med Azure policy kan du också få till gång till dina resurser genom att använda Mass reparationer för befintliga resurser och automatisk reparation av nya resurser.

## <a name="azure-policies-for-web-application-firewall"></a>Azure-principer för brand vägg för webbaserade program

Det finns flera inbyggda Azure-principer för att hantera WAF-resurser. En uppdelning av principerna och deras funktioner är följande:

1. **Brand vägg för webbaserade program (WAF) måste vara aktive rad för Azures frontend-tjänst**: Azure-frontend-tjänster utvärderas på om det finns en WAF för att skapa resurser. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en Azure-frontend-tjänst inte har en WAF och låter användarna se vad Azure-tjänsten för front dörr inte följer. Neka förhindrar att ingen Azure-frontend-tjänst skapas om en WAF inte är ansluten. Inaktive rad inaktiverar den här principen.

2. **Brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway**: programgatewayer utvärderas på om det finns en WAF för att skapa en resurs. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en Application Gateway inte har en WAF och låter användarna se vad Application Gateway inte uppfyller. Neka förhindrar att Application Gateway skapas om en WAF inte är ansluten. Inaktive rad inaktiverar den här principen.

3. **Brand vägg för webbaserade program (WAF) ska använda det angivna läget för Azures frontend-tjänst**: bestämmer användningen av "identifiering" eller "förebyggande" läge så att de aktive ras i alla brand Väggs principer för webb program för Azure-tjänsten för front dörr. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en WAF inte passar det angivna läget. Neka förhindrar att ingen WAF skapas om den inte är i rätt läge. Inaktive rad inaktiverar den här principen.

4. **Brand vägg för webbaserade program (WAF) ska använda det angivna läget för Application Gateway**: bestämmer användningen av "identifiering" eller "förebyggande" läge som aktiv på alla brand Väggs principer för webb program för Application Gateway. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en WAF inte passar det angivna läget. Neka förhindrar att ingen WAF skapas om den inte är i rätt läge. Inaktive rad inaktiverar den här principen.


## <a name="launch-an-azure-policy"></a>Starta en Azure Policy


1.  På sidan Azure-start skriver du princip i Sök fältet och klickar på ikonen Azure Policy

2.  I Azure policy service, under **redigering**, väljer du **tilldelningar**.

![Brand vägg för Azure-webbprogram](../media/waf-azure-policy/policy-home.png)

3.  På sidan tilldelningar väljer du ikonen **tilldela princip** överst.

![Brand vägg för Azure-webbprogram](../media/waf-azure-policy/assign-policy.png)

4.  Uppdatera följande fält på fliken för att tilldela princip sidor:
    1.  **Omfång**: Välj vilka Azure-prenumerationer och resurs grupper som ska påverkas av Azure policy.
    2.  **Undantag**: Välj eventuella resurser från omfånget som ska undantas från principen 
    3.  **Princip definition**: Välj Azure policy som ska användas för omfånget med undantag. Skriv "brand vägg för webbaserade program" i Sök fältet för att välja relevant brand vägg för webbaserade program Azure Policy.

![Brand vägg för Azure-webbprogram](../media/waf-azure-policy/policy-listing.png)


5.  Välj fliken **parametrar** och uppdatera princip parametrarna. För att ytterligare klargöra vad parametern gör, Hovra över informations ikonen bredvid parameter namnet för ytterligare klargörande.

6.  Välj **Granska + skapa** för att slutföra din Azure-princip. Azure policy tar cirka 15 minuter tills den aktive ras för nya resurser.
