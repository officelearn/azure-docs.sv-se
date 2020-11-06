---
title: Application Gateway integration med Azure Security Center | Microsoft Docs
description: Den här sidan innehåller information om hur Application Gateway integreras i Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: 1464c0c0b0d573711ed07332a76bb67e73dc0484
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397781"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Översikt över integrering mellan Application Gateway och Azure Security Center

Lär dig hur Application Gateway och Security Center hjälper dig att skydda dina webb programs resurser. Brand vägg för webbaserade program i Application Gateway (WAF) integreras med [Security Center](../security-center/security-center-introduction.md) för att ge en sömlös vy för att förhindra, identifiera och svara på hot mot oskyddade webb program i din miljö.

## <a name="overview"></a>Översikt

Application Gateway WAF är en rekommendation i Security Center för att skydda webb program mot sårbarheter och sårbarheter. Web-aktiverade resurser som inte skyddas av WAF visas i Security Center som rekommendationer med hög allvarlighets grad. Rekommendationer för brand väggar för webb program visas på sidan **Översikt** under **program**.

![integrering med Security Center][1]

Om du klickar på alla rekommendationer om brand vägg för webbaserade program öppnas en ny sida som visar information om rekommendationen.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Lägg till en brand vägg för webbaserade program i en befintlig resurs

Gå till **alla tjänster**  >  **säkerhet och identitet**  >  **Security Center** och klicka på **program** på **Security Center-översikt**. I **Security Center-program** innehåller tabellen en lista över program som Security Center identifierats i din prenumeration.

![webb program][3]

Genom att klicka på ett webb program med ett kritiskt problem får du sidan **program säkerhets hälsa** . I bilden nedan är webb programmet som inte skyddas av en brand vägg för webbaserade program. 

![webb resurser som inte är skyddade][2]

Klicka på **Lägg till en brand vägg för webbaserade program** under **rekommendationer** för att öppna sidan **Lägg till en brand vägg för webb program** .

Om du inte har ett befintligt Application Gateway eller om du vill skapa en ny, klickar du på **Skapa ny** och på  **skapa en ny brand vägg för webbaserade program** och klickar på **Microsoft-Application Gateway**. Detta tar dig igenom stegen för att skapa en Programgateway. Nu läggs ditt webb program till som en skyddad resurs, Security Center nu spårar att den här resursen skyddas av en brand vägg för webbaserade program. Detta lägger inte till den som medlem i en server del.

Om du har en befintlig Application Gateway kan du välja den under **Använd befintlig lösning**

![Skärm bild av sidan Lägg till en brand vägg för webbaserade program. Under Använd befintlig lösning visas en Application Gateway.][4]

Om du lägger till ett webb program till en Programgateway via Security Center läggs inte resursen till i en medlem i en Server grupp. Detta måste göras i Application Gateway-resursen direkt.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Lägga till en resurs i en befintlig brand vägg för webbaserade program

Gå till **alla tjänster**  >  **säkerhet och identitet**  >  **Security Center** och klicka på **partner lösningar** på **Security Center-översikt**. Befintliga Security Center medveten Application gateways visas på sidan **partner lösningar** .

![partner lösningar][7]

Klicka på **Länka app** för att öppna **länk program** , här får du alternativ för att välja befintliga program. Välj de program som du vill skydda och klicka på **OK**. Detta lägger inte till webb programmet i backend-poolen för Application Gateway. Detta anger resurserna som en skyddad resurs så Security Center kan spåra den. Om du vill lägga till resursen som medlem i en server del måste du göra detta på Application Gateway, från den aktuella sidan kan du klicka på **lösnings konsolen** för att gå till den Application Gateway-resurs där du kan lägga till webb programmet i backend-poolen.

![partner Solutions-program][6]

## <a name="finalize-configuration"></a>Slutför konfiguration

Security Center spårar program som har lagts till i en Application Gateway som en skyddad resurs.  Den övervakar resursens hälso tillstånd och säkerställer att den skyddas av en Programgateway. Nästa steg är att lägga till den privata IP-adressen, den offentliga IP-adressen eller NIC-adressen för den virtuella datorn i backend-poolen för Application Gateway. Tills detta görs ytterligare en rekommendation om att **slutföra program skydd** visas tills resursen har lagts till.

![Skärm bild av sidan Slutför program skydd, med ett program synligt. Text förklarar vilka steg du bör vidta för att skydda programmet.][5]

## <a name="security-alerts"></a>Säkerhets aviseringar

I Security Center navigera till **identifierings**  >  **säkerhets aviseringar**.  Här hittar du WAF-aviseringar för dina programgatewayer. Aviseringar delas upp av WAF-regeln.

![säkerhets aviseringar][8]

Om du väljer en regel visas en lista över aviseringar för den aktuella WAF-regeln. Varje avisering visar ytterligare information om att hitta. Informationen innehåller en länk till Application Gateway.
 
![aviserings information][9]

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du aktiverar brand vägg för webbaserade program på en befintlig Programgateway kan du gå [till skapa eller uppdatera en Azure Application Gateway med brand vägg för webb program](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png