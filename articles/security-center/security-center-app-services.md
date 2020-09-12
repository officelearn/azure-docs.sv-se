---
title: Skydda dina Azure App Service-webbappar och API:er
description: 'Den här artikeln hjälper dig att komma igång med att skydda dina Azure App Service-webbappar och API: er i Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: ec3fb8609612f3920e330da7922fdd1eb8883305
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459784"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Skydda dina Azure App Service-webbappar och API:er

Azure App Service är en helt hanterad plattform för att bygga och vara värd för dina webbappar och API: er utan att behöva hantera infrastrukturen. Den ger hantering, övervakning och Operational Insights för att uppfylla prestanda-, säkerhets-och efterlevnads krav i företags klass. Mer information finns i [Azure App Service](https://azure.microsoft.com/services/app-service/).

Om du vill aktivera avancerat skydd för din Azure App Service-plan måste du:

* Prenumerera på Azure Security Center standard pris nivån
* Aktivera App Service plan som visas nedan. Security Center är internt integrerat med App Service, vilket eliminerar behovet av distribution och onboarding – integreringen är transparent.
* Ha en App Service plan som är associerad med dedikerade datorer. De planer som stöds är: Basic, standard, Premium, isolerat eller Linux. Security Center stöder inte de kostnads fria, delade eller konsumtions planerna. Mer information finns i [App Service-planer](https://azure.microsoft.com/pricing/details/app-service/plans/).

När App Service plan aktive rad bedömer Security Center resurserna som omfattas av din App Service plan och genererar säkerhets rekommendationer baserat på dess resultat. Security Center skyddar den VM-instans där App Service körs och hanterings gränssnittet. Den övervakar också förfrågningar och svar som skickas till och från dina appar som körs i App Service.

Security Center utnyttjar molnets skala och den synlighet som Azure har som moln leverantör, för att övervaka för vanliga attacker från webb program. Security Center kan identifiera attacker på dina program och identifiera nya attacker – även om angripare befinner sig i rekognosering-fasen för att identifiera sårbarheter i flera Azure-värdbaserade program. Som en Azure-Native-tjänst är Security Center också i en unik position för att erbjuda värdbaserade säkerhets analyser som täcker underliggande datornoder för den här PaaS, vilket gör att Security Center kan upptäcka attacker mot webb program som redan utnyttjats. Mer information finns i [skydd mot Azure App Service](threat-protection.md#app-services).


## <a name="enable-monitoring-and-protection-of-app-service"></a>Aktivera övervakning och skydd av App Service

1. I Azure Portal väljer du Security Center.
2. Gå till **pris & inställningar** och välj en prenumeration.
3. Under **pris nivå**, i den **App Service** -rad, växlar du planen till **aktive rad**.

    [![Aktivera app Services i din prenumeration på standard nivå](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Antalet instanser som anges för din **resurs kvantitet** representerar det totala antalet beräknings instanser, i alla App Service planer i den här prenumerationen, som körs när du öppnade bladet pris nivå.
>
> Azure App Service erbjuder en rad olika planer. App Service plan definierar mängden beräknings resurser för en webbapp som ska köras. Dessa motsvarar Server grupper i konventionell webb värd. En eller flera appar kan konfigureras för att köras på samma dator resurser (eller i samma App Service plan).
>
>Om du vill validera antalet, head to App Service Planes i Azure-portalen, där du kan se antalet beräknings instanser som används av varje plan. 






Om du vill inaktivera övervakning och rekommendationer för din App Service upprepar du den här processen och växlar **app Services** planen till **inaktive rad**.



## <a name="see-also"></a>Se även
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. Mer information om Azure Security Center finns i följande artiklar:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [App Services](security-center-virtual-machine-protection.md#app-services): Visa en lista över App Service-miljöer med hälso översikter.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Azures säkerhetsblogg](https://docs.microsoft.com/archive/blogs/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
