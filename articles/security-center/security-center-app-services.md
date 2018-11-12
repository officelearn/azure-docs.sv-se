---
title: Skydda App Services i Azure Security Center | Microsoft Docs
description: Den här artikeln hjälper dig att komma igång med att skydda dina App Services i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 062d3ce75372cd09e617fb984208542a31cb8e4a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019251"
---
# <a name="protect-app-service-with-azure-security-center"></a>Skydda App Service med Azure Security Center
Den här artikeln hjälper dig att använda Azure Security Center att övervaka och skydda dina program som körs på App Service.

App Service kan du skapa och agera värd för webbprogram i vilket språk du önskar utan att behöva hantera infrastrukturen. App Service tillhandahåller automatisk skalning och hög tillgänglighet, stöd för både Windows och Linux, samt automatiska distributioner från GitHub, Visual Studio Team Services eller valfri Git-lagringsplats. 

Säkerhetsproblem i webbprogram är ofta utnyttjas av angripare, eftersom de har en gemensam och dynamiska gränssnitt för nästan alla organisationer på Internet. Begäranden till program som körs på App Service går igenom flera gatewayar som distribuerats i Azure-Datacenter runtom i världen som ansvarar för routning varje begäran till dess motsvarande program. 

Azure Security Center kan köra utvärderingar och rekommendationer på dina program som körs i App Service i sandbox-miljöer i virtuell dator eller på begäran-instanser. Security Center analyserar dina interna App Service-loggar att övervaka vanliga web app-attacker som ofta körs över flera mål med hjälp av synlighet med Azure som molntjänstleverantör.

Security Center utnyttjar skalan för molnet för att identifiera attacker på dina App Service-appar och fokusera på nya attacker, samtidigt som angripare är på rekognoseringsfasen genomsökning för att identifiera säkerhetsproblem över flera webbplatser som körs på Azure. Security Center använder analyser och machine learning-modeller för att täcka alla gränssnitt så att kunder kan interagera med deras program, oavsett om via HTTP eller via en management-metoder. Dessutom som en Förstaparts-tjänst i Azure är Security Center också unik möjlighet att erbjuda värdbaserade säkerhetsanalyser omfattar de underliggande compute-noderna för det här PaaS, när Security Center för att identifiera attacker mot webbprogram som har redan utnyttjat.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en App Service-plan som är associerad med dedikerade datorer för att övervaka och skydda dina App Service. Dessa är: Basic, Standard, Premium, isolerad eller Linux. Azure Security Center har inte stöd för prenumerationerna kostnadsfri, delad eller förbrukning. Mer information finns i [App Service-planer](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Security Center-skydd

Azure Security Center skyddar VM-instans där din App Service körs och hanteringsgränssnittet. Den övervakar även begäranden och svar som skickas till och från program som körs i App Service.

Security Center är inbyggt med App Service, vilket eliminerar behovet av distribution och registrering - integrationen är helt transparent.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Aktivera övervakning och skydd av App Service

1. Välj Security Center i Azure.
2. Gå till **säkerhetsprincip** och välj en prenumeration.
3. I slutet av raden med prenumerationen klickar du på **redigera inställningar för**.
4. Under **prisnivå**i den **apptjänst** rad, växla för att **aktiverad**.

![App service-växla](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Antalet instanser som anges för din resurskvantitet representerar antalet relevanta instanser av apptjänst som är aktiva för tillfället när du har öppnat prisnivåbladet. Eftersom det här talet ändras baserat på skalningsalternativ som du har valt, kommer antalet instanser debiteras du för att ändras i enlighet med detta.

Om du vill inaktivera övervakning och rekommendationer för App Service, upprepa den här processen och växla dina **Apptjänst** tänker **inaktiverad**.



## <a name="see-also"></a>Se också
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
