---
title: Brand vägg för Azure-webbprogram och Azure Policy
description: Azure Web Application Firewall (WAF) kombinerat med Azure Policy kan hjälpa organisations standarder och utvärdera kompatibilitet för WAF-resurser
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: rimansdo
ms.openlocfilehash: 4c1fd53eb6ebf1f1aebdfba99b736e26bd6cff2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306964"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Brand vägg för Azure-webbprogram och Azure Policy

Azure Web Application-brandväggen (WAF) kombinerat med Azure Policy kan hjälpa organisations standarder och utvärdera kompatibiliteten för WAF-resurser. Azure policy är ett styrnings verktyg som ger en sammanställd vy för att utvärdera miljöns övergripande tillstånd, med möjlighet att öka detalj nivån för att öka detalj nivån per resurs, per princip. Med Azure policy kan du också få till gång till dina resurser genom att använda Mass reparationer för befintliga resurser och automatisk reparation av nya resurser.

## <a name="azure-policies-for-web-application-firewall"></a>Azure-principer för brand vägg för webbaserade program

Det finns flera inbyggda Azure-principer för att hantera WAF-resurser. En uppdelning av principerna och deras funktioner är följande:

1. **Brand vägg för webbaserade program ska aktive ras för Azure-tjänsten för frontend-dörren eller Application Gateway**: Azures tjänster för frontend-dörr och programgatewayer utvärderas på om det finns en WAF för att skapa resurser. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en Azure-frontend-tjänst eller Application Gateway inte har en WAF och låter användarna se vad Azure frontend-tjänsten eller Application Gateway inte uppfyller för närvarande. Neka förhindrar att ingen Azure-frontend-tjänst eller Application Gateway skapas om en WAF inte är ansluten. Inaktive rad inaktiverar den här principen.

2. **Brand väggen för webbaserade program ska vara ett uppsättnings läge för Application Gateway och Azure-tjänsten för frontend-tjänsten**: brand väggen för webbaserade program utvärderas på vilket läge den finns, förebyggande eller identifiering. Principen garanterar läges konsekvens i brand väggar för webb program. Principen har tre effekter: Audit, Deny och Disable. Gransknings spår när en WAF inte passar det angivna läget. Neka förhindrar att ingen WAF skapas om den inte är i rätt läge. Inaktive rad inaktiverar den här principen.


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

![Brand vägg för Azure-webbprogram](../media/waf-azure-policy/policy-listings.png)


5.  Välj fliken **parametrar** och uppdatera princip parametrarna. För att ytterligare klargöra vad parametern gör, Hovra över informations ikonen bredvid parameter namnet för ytterligare klargörande.

6.  Välj **Granska + skapa** för att slutföra din Azure-princip. Azure policy tar cirka 15 minuter tills den aktive ras för nya resurser.
