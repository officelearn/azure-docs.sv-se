---
title: Programmet Gateway-integration med Azure Security Center | Microsoft Docs
description: "Den här sidan innehåller information om hur Application Gateway är integrerat i Azure Security Center."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: e0ae5d797a2a502cceeb1be20af4953f16f1320a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Översikt över integrering mellan Programgateway och Azure Security Center

Lär dig hur Programgateway och Security Center skydda resurser i ditt webbprogram. Brandvägg för programmet gateway webbaserade program (Brandvägg) kan integreras med [Security Center](../security-center/security-center-intro.md) för att ge en smidig vy för att förhindra, identifiera och åtgärda hot till oskyddade webbprogram i din miljö.

## <a name="overview"></a>Översikt

Programmet Gateway Brandvägg är en rekommendation i Security Center för att skydda webbprogram från kryphål och säkerhetsproblem. Aktiverad webbresurser som inte skyddas av Brandvägg visas i security center som Hög allvarlighetsgrad rekommendationer. Rekommendationer för web application brandväggar visas på den **översikt** sidan under **program**.

![integrering med security center][1]

Klicka på några rekommendationer om Brandvägg för webbaserade program öppnas ett nytt blad som visar information om rekommendationen.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Lägg till en brandvägg för webbaserade program i en befintlig resurs

Gå till **fler tjänster** > **säkerhet + identitet** > **Security Center** och på den **Security Center - översikt**  bladet, klickar du på **program**. På den **Security Center - program** bladet tabellen innehåller en lista över program som Security Center identifieras i din prenumeration.

![webbprogram][3]

Genom att klicka på ett webbprogram med ett allvarligt problem kan du hämta den **programmet säkerhetshälsa** bladet. I bilden nedan, webbprogram som inte skyddas av en brandvägg för webbaserade program. 

![webbresurser som inte skyddas][2]

Klicka på **lägga till en brandvägg för webbaserade program** under **rekommendationer** att öppna den **lägga till en brandvägg för webbaserade program** bladet.

Om du inte har en befintlig Gateway för programmet, eller om du vill skapa en ny, klickar du på **Skapa nytt** och på den **skapa en ny Brandvägg för webbaserade program** bladet och klickar på **Microsoft - program Gateway**. Detta tar dig igenom stegen för att skapa en Programgateway. Webbprogrammet har nu lagts till som en skyddad resurs, Security Center nu spårar att den här resursen skyddas av en brandvägg för webbaserade program. Detta lägger inte till den som en medlem för backend-poolen.

Om du har en befintlig Programgateway, kan du välja den under **med befintliga lösning**

![Brandvägg för webbaserade program lägger du till bladet][4]

Lägger till ett webbprogram till en Programgateway via Security Center inte lägger till resursen som medlem backend-adresspool, måste du göra det på gateway-programresurs direkt.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Lägg till en resurs i en befintlig Brandvägg för webbaserade program

Gå till **fler tjänster** > **säkerhet + identitet** > **Security Center** och på den **Security Center - översikt**  bladet, klickar du på **partnerlösningar**. Befintliga gateways för Security Center-medvetna programmet visas i den **partnerlösningar** bladet.

![partnerlösningar][7]

Klicka på **Link app** att öppna den **länken program** bladet här du ges alternativ för befintliga program. Välj program att skydda och klicka på **OK**. Detta lägger inte till webbprogrammet till serverdelspoolen för programgatewayen. Anger resurserna som en skyddad resurs så kan spåra av Security Center. Om du vill lägga till resursen som medlem i serverdelen poolen detta måste göras på Programgateway från det aktuella bladet kan du klicka på **lösning konsolen** för att programmet gatewayresursen där du kan lägga till webbprogrammet till den serverdelspool.

![partner solutions program][6]

## <a name="finalize-configuration"></a>Slutför konfiguration

Security Center spårar program som har lagts till i en Programgateway som en skyddad resurs.  Den övervakar tillståndet för den här resursen och garanterar att den skyddas av en Programgateway. Nästa steg är att lägga till privata IP-, offentlig IP-adress eller nätverkskort på den virtuella datorn till serverdelspoolen för programgatewayen. Tills detta görs en ytterligare rekommendation av **Slutför programskydd** visas förrän resursen har lagts till.

![Brandvägg för webbaserade program lägger du till bladet][5]

## <a name="security-alerts"></a>Säkerhetsaviseringar

Navigera till i Security Center **identifiering** > **säkerhetsaviseringar**.  Här hittar du Brandvägg aviseringar för din programgatewayer. Aviseringar är fördelade på Brandvägg regeln.

![säkerhetsaviseringar][8]

Klicka på en regel ger en lista över aviseringar för den specifika regeln Brandvägg. Varje avisering visar ytterligare information om den söka efter. Informationen innehåller en länk till programgatewayen.
 
![aviseringsinformation][9]

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du aktiverar Brandvägg för webbaserade program på en befintlig Programgateway [skapa eller uppdatera en Azure Programgateway med Brandvägg för webbaserade program](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png