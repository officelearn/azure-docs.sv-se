---
title: Skydda dina Azure App Service-webbappar och API:er
description: Den här artikeln hjälper dig att komma igång med att skydda dina Azure App Service-webbappar och API:er i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616473"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Skydda dina Azure App Service-webbappar och API:er

Azure App Service är en fullständigt hanterad plattform för att skapa och vara värd för dina webbappar och API:er utan att behöva hantera infrastrukturen. Den ger hanterings-, övervaknings- och driftsinsikter för att uppfylla prestanda-, säkerhets- och efterlevnadskrav i företagsklass. Mer information finns i [Azure App Service](https://azure.microsoft.com/services/app-service/).

Om du vill aktivera avancerat hotskydd för din Azure App Service-plan måste du:

* Prenumerera på Azure Security Centers standardprisnivå
* Aktivera apptjänstplanen enligt nedan. Security Center är inbyggt integrerat med App Service, vilket eliminerar behovet av distribution och introduktion - integrationen är transparent.
* Har en App Service-plan som är associerad med dedikerade datorer. Planer som stöds är: Basic, Standard, Premium, Isolerade eller Linux. Security Center stöder inte planerna För lediga, delade eller Förbrukning. Mer information finns i [App Service Plans](https://azure.microsoft.com/pricing/details/app-service/plans/).

Med App Service-planen aktiverad utvärderar Security Center de resurser som omfattas av apptjänstplanen och genererar säkerhetsrekommendationer baserat på resultaten. Security Center skyddar den VM-instans där apptjänsten körs och hanteringsgränssnittet. Den övervakar också förfrågningar och svar som skickas till och från dina appar som körs i App Service.

Security Center utnyttjar molnets skala och den synlighet som Azure har som molnleverantör för att övervaka vanliga webbappattacker. Security Center kan upptäcka attacker mot dina program och identifiera nya attacker – även när angripare befinner sig i spaningsfasen, skanning för att identifiera sårbarheter i flera Azure-värdbaserade program. Som en Azure-infödd tjänst är Security Center också i en unik position för att erbjuda värdbaserad säkerhetsanalys som täcker de underliggande beräkningsnoderna för denna PaaS, vilket gör security center aktiverat för att identifiera attacker mot webbprogram som redan har utnyttjats. Mer information finns i [Hotskydd för Azure App Service](threat-protection.md#app-services).


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Aktivera övervakning och skydd av App Service

1. Välj Security Center i Azure-portalen.
2. Gå till **inställningar för prissättning &** och välj en prenumeration.
3. Växla planen till **Aktiverad**under **Prisnivå**på raden **App-tjänst** .

    [![Aktivera apptjänster i prenumerationen på standardnivå](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Antalet instanser som anges för **resurskvantiteten representerar** det totala antalet beräkningsinstanser, i alla App Service-planer på den här prenumerationen och som körs vid den tidpunkt då du öppnade prisnivåbladet.
>
> Azure App Service erbjuder en mängd olika planer. App service-planen definierar uppsättningen beräkningsresurser för en webbapp som ska köras. Dessa motsvarar server gårdar i konventionella webbhotell. En eller flera appar kan konfigureras för att köras på samma datorresurser (eller i samma App Service-plan).
>
>För att validera antalet, gå till "App Service planer" i Azure Portal, där du kan se antalet beräkningsinstanser som används av varje plan. 






Om du vill inaktivera övervakning och rekommendationer för apptjänsten upprepar du den här processen och växlar **apptjänstplanen** till **Inaktiverad**.



## <a name="see-also"></a>Se även
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. Mer information om Azure Security Center finns i följande artiklar:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Apptjänster](security-center-virtual-machine-protection.md#app-services): Visa en lista över apptjänstmiljöer med hälsosammanfattningar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
