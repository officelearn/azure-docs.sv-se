---
title: Application Gateway-integration med Azure Security Center | Microsoft Docs
description: Den här sidan innehåller information om hur Application Gateway är integrerad i Azure Security Center.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: victorh
ms.openlocfilehash: 10f115b64f0bd3f7e557da2bedbf3327d0ef483d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122311"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Översikt över integreringen mellan Application Gateway och Azure Security Center

Lär dig hur Application Gateway och Security Center till att skydda resurser i ditt webbprogram. Application gateway waf (WAF) som kan integreras med [Security Center](../security-center/security-center-intro.md) för att ge en sömlös vy för att förhindra, upptäcka och svara på hot mot oskyddade webbappar i din miljö.

## <a name="overview"></a>Översikt

Application Gateway WAF är en rekommendation i Security Center för att skydda webbprogram från kryphål och säkerhetsproblem. -Aktiverade webbresurser som inte skyddas av WAF visas i security center som rekommendationer med hög allvarlighetsgrad. Rekommendationer för brandväggar för webbprogram visas på den **översikt** sidan under **program**.

![integrering med security center][1]

Klicka på några rekommendationer om Brandvägg för webbaserade program öppnas en ny sida som visar information om rekommendationen.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Lägg till en brandvägg för webbaserade program i en befintlig resurs

Gå till **alla tjänster** > **säkerhet + identitet** > **Security Center** på **Security Center – översikt**, klickar du på **program**. På **Security Center - program**, tabellen innehåller en lista över program som Security Center som identifierats i din prenumeration.

![webbprogram][3]

Genom att klicka på ett webbprogram med ett kritiskt problem, du får den **programmets säkerhetshälsa** sidan. I bilden nedan, webbprogram som inte skyddas av en brandvägg för webbaserade program. 

![webbresurser som inte skyddas][2]

Klicka på **lägga till en brandvägg för webbaserade program** under **rekommendationer** att öppna den **lägga till en brandvägg för webbaserade program** sidan.

Om du inte har en befintlig Application Gateway eller vill skapa en ny, klickar du på **Skapa ny** på **skapa en ny Brandvägg för webbaserade program**, och klicka på **Microsoft – Application Gateway** . Detta tar dig igenom stegen för att skapa en Programgateway. Webbprogrammet har nu lagts till som en skyddad resurs Security Center nu spårar att den här resursen skyddas av en brandvägg för webbaserade program. Detta lägger inte till den som en medlem för backend-poolen.

Om du har en befintlig application gateway kan du välja den under **Använd befintlig lösning**

![Brandvägg för webbaserade program Lägg till sida][4]

Att lägga till ett webbprogram till en application gateway, via Security Center lägger inte till resursen som en medlem för backend-poolen. Detta måste göras på programgatewayresursen direkt.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Lägg till en resurs i en befintlig Brandvägg för webbaserade program

Gå till **alla tjänster** > **säkerhet + identitet** > **Security Center** på **Security Center – översikt**, klickar du på **partnerlösningar**. Befintliga Security Center medveten programgatewayer visa i den **partnerlösningar** sidan.

![Partnerlösningar][7]

Klicka på **länka app** att öppna **länka program**, här ges du alternativ att välja befintliga program. Välj de program som ska skydda och klickar på **OK**. Detta lägger inte till webbappen till serverdelspoolen för application gateway. Detta anger resurserna som en skyddad resurs så att Security Center kan spåra den. Om du vill lägga till resursen som medlem backend-pool, måste du göra det på programgatewayen från den aktuella sidan som du kan klicka på **lösningskonsol** som ska vidtas på den programgatewayresurs där du kan lägga till det webbaserade programmet och den backend-pool.

![program från partner lösningar][6]

## <a name="finalize-configuration"></a>Slutför konfiguration

Security Center spårar program som har lagts till i en application gateway som en skyddad resurs.  Den övervakar hälsotillståndet för den här resursen och ser till att den är skyddad med en Programgateway. Nästa steg är att lägga till privata IP-Adressen, offentlig IP-adress eller NIC för den virtuella datorn till i serverdelspoolen för programgatewayen. Tills detta görs en ytterligare rekommendation av **Slutför programskydd** visas tills resursen har lagts till.

![Brandvägg för webbaserade program Lägg till sida][5]

## <a name="security-alerts"></a>Säkerhetsaviseringar

Navigera till i Security Center **identifiering** > **säkerhetsaviseringar**.  Här hittar du WAF aviseringar för dina programgatewayer. Aviseringar är uppdelade efter WAF-regel.

![Säkerhetsaviseringar][8]

Klicka på en regel får en lista över aviseringar för den specifika WAF-regeln. Varje avisering innehåller ytterligare uppgifter om hittar. Informationen tillhandahåller en länk till application gateway.
 
![Aviseringsinformation][9]

## <a name="next-steps"></a>Nästa steg

Du kan få veta hur du aktiverar Brandvägg för webbaserade program på en befintlig Programgateway [skapa eller uppdatera en Azure-Programgateway med brandväggen för webbaserade program](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png