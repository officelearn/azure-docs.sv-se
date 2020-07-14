---
title: Azure Front Door
description: Den här artikeln lär dig hur du konfigurerar ett säkerhets huvud via regel motorn på Azures front dörr
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: d489186dc1c012fe8c181f17e00bcdb999e230dd
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232142"
---
# <a name="add-security-headers-with-rules-engine"></a>Lägga till säkerhetsrubriker med regelmotor

Implementera säkerhets rubriker för att förhindra webbläsarbaserade sårbarheter som HTTP Strict-Transport-Security (HSTS), X-XSS-skydd, Content-Security-policy eller X-Frame-Options. Säkerhetsbaserade attribut kan också definieras med cookies.

I följande exempel visas hur du lägger till ett innehålls-säkerhets-princip-huvud till alla inkommande förfrågningar som matchar den sökväg som definierats i den väg som regel motor konfigurationen är associerad med. Här kan vi bara tillåta att skript från vår betrodda plats **https://apiphany.portal.azure-api.net** körs i vårt program.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Lägg till ett Content-Security-Policy-huvud i Azure Portal

1. Innan du skapar den här regeln lär du dig hur du [skapar en frontend-dörr](quickstart-create-front-door.md) eller hur du [skapar en regel motor](front-door-tutorial-rules-engine.md) om det är första gången du använder antingen AFD eller funktionen för regel motor.

2. Klicka på **Lägg till** för att lägga till en ny regel. Ange ett namn för regeln och klicka sedan på **Lägg till ett åtgärds**  >  **svars huvud**.

3. Ange att operatorn ska **läggas till för att** lägga till den här rubriken som ett svar på alla inkommande begär anden till den här vägen.

4. Lägg till rubrik namnet: **Content-Security-Policy** och definiera de värden som rubriken ska acceptera. I det här scenariot väljer vi *"script-src ' Self ' https://apiphany.portal.azure-api.net ."*

5. När du har lagt till alla regler som du vill ha i konfigurationen kan du inte glömma att gå till önskad väg och koppla din regel Motors konfiguration till din väg regel. Det här steget krävs för att aktivera regeln för att fungera. 

![Portal exempel](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> I det här scenariot har vi inte lagt till [matchnings villkor](front-door-rules-engine-match-conditions.md) för regeln. Alla inkommande begär Anden som matchar den sökväg som definierats i flödes regeln har den här regeln tillämpats. Om du vill att det bara ska gälla för en delmängd av dessa begär Anden, måste du lägga till dina egna matchnings villkor för den här regeln.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [AFD-regel motorn](front-door-rules-engine.md). 
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
- Lär dig mer om regel motorns [matchnings villkor](front-door-rules-engine-match-conditions.md)
- Läs mer i AFD Rules Engine [CLI-referens](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest). 
- Läs mer i AFD-regel motor [PowerShell-referens](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
