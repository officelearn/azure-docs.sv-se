---
title: Integrering av programgateway med Azure Security Center | Microsoft-dokument
description: Den här sidan innehåller information om hur Application Gateway är integrerat i Azure Security Center.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277190"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Översikt över integrering mellan Application Gateway och Azure Security Center

Lär dig hur Application Gateway och Security Center skyddar dina webbprogramresurser. Brandvägg för webbprogram för programgateway (WAF) integreras med [Security Center](../security-center/security-center-intro.md) för att ge en sömlös vy för att förebygga, upptäcka och svara på hot mot oskyddade webbprogram i din miljö.

## <a name="overview"></a>Översikt

Application Gateway WAF är en rekommendation i Security Center för att skydda webbprogram från bedrifter och sårbarheter. Webbaktiverade resurser som inte skyddas av WAF visar i säkerhetscentret som rekommendationer för hög allvarlighetsgrad. Rekommendationer för brandväggar för webbprogram visas på **översiktssidan** under **Program**.

![integration med säkerhetscenter][1]

Om du klickar på några rekommendationer om brandvägg för webbprogram öppnas en ny sida som visar information om rekommendationen.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Lägga till en brandvägg för webbprogram i en befintlig resurs

Navigera till **Alla tjänster** > **Security + Identity** > **Security Center** och på Security Center **- Översikt**klickar du på **Program**. I **Security Center - Program**innehåller tabellen en lista över program som Security Center har upptäckt i din prenumeration.

![webbapplikationer][3]

Genom att klicka på ett webbprogram med ett kritiskt problem får du hälsosidan **för programsäkerhet.** I bilden nedan visas webbprogrammet som inte skyddas av en brandvägg för webbprogram. 

![webbresurser som inte är skyddade][2]

Klicka på Lägg till en brandvägg för **webbprogram** under **Rekommendationer** om du vill öppna sidan **Lägg till en brandvägg för webbprogram.**

Om du inte har en befintlig Programgateway, eller om du vill skapa en ny, klickar du på **Skapa ny** och på Skapa en ny brandvägg **för webbprogram**och klickar på **Microsoft - Application Gateway**. Detta tar dig igenom stegen för att skapa en programgateway. Nu läggs webbprogrammet till som en skyddad resurs, security center spårar nu att den här resursen skyddas av en brandvägg för webbprogram. Detta lägger inte till det som en backend pool medlem.

Om du har en befintlig programgateway kan du välja den under **Använd befintlig lösning**

![Sida för att lägga till en brandvägg för webbprogram][4]

Att lägga till ett webbprogram i en programgateway via Security Center lägger inte till resursen som en backend poolmedlem. Detta måste göras direkt på programgatewayresursen.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Lägga till en resurs i en befintlig brandvägg för webbprogram

Navigera till **Alla tjänster** > **Security + Identity** > **Security Center** och på Security Center - **Översikt**klickar du på **Partnerlösningar**. Befintliga säkerhetscenter-medvetna programgateways visas på sidan **Partnerlösningar.**

![partnerlösningar][7]

Klicka på **Länk app** för att öppna **Länk program**, här får du möjlighet att välja befintliga program. Välj de program som ska skyddas och klicka på **OK**. Detta lägger inte till webbprogrammet i backend-poolen för programgatewayen. Detta anger resurserna som en skyddad resurs så att Security Center kan spåra den. Om du vill lägga till resursen som en serverdelspoolmedlem måste detta göras på programgatewayen, från den aktuella sidan kan du klicka på **Lösningskonsol** som ska tas till programgatewayresursen där du kan lägga till webbprogrammet i serverdelspoolen.

![partnerlösningar applikationer][6]

## <a name="finalize-configuration"></a>Slutför konfigurationen

Security Center spårar program som läggs till i en programgateway som en skyddad resurs.  Den övervakar hälsotillståndet för den här resursen och säkerställer att den skyddas av en programgateway. Nästa steg är att lägga till den privata IP-adressen, den offentliga IP-datorn eller nätverkskortet för den virtuella datorn i serverdapoolen för programgatewayen. Tills detta görs visas ytterligare en rekommendation om **slutfört programskydd** tills resursen läggs till.

![Sida för att lägga till en brandvägg för webbprogram][5]

## <a name="security-alerts"></a>Säkerhetsvarningar

Inom Security Center navigera till **DETECTION** > **Security Alerts**.  Här hittar du WAF-aviseringar för dina programgateways. Aviseringar delas upp efter WAF-regel.

![säkerhetsvarningar][8]

Om du väljer en regel visas en lista över aviseringar för den specifika WAF-regeln. Varje avisering visar ytterligare information om fyndet. Informationen innehåller en länk till programgatewayen.
 
![information om varning][9]

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du aktiverar brandvägg för webbprogram i en befintlig programgateway besöker du [Skapa eller uppdaterar en Azure Application Gateway med brandvägg för webbprogram](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png