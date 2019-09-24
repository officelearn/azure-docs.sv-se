---
title: Skydda App Services i Azure Security Center | Microsoft Docs
description: Den här artikeln hjälper dig att komma igång med att skydda dina App Services i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 68f7c47f0a0f56085d632f1c1741318f440b41ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202475"
---
# <a name="protect-app-service-with-azure-security-center"></a>Skydda App Service med Azure Security Center
Den här artikeln hjälper dig att använda Azure Security Center för att övervaka och skydda dina program som körs ovanpå App Service.

Med App Service kan du bygga och vara värd för webb program i valfritt programmeringsspråk utan att behöva hantera infrastrukturen. App Service erbjuder automatisk skalning och hög tillgänglighet, stöder både Windows och Linux, samt automatiserade distributioner från GitHub, Azure DevOps eller git-lagringsplatsen. 

Sårbarheter i webb program utnyttjas ofta av angripare, eftersom de har ett gemensamt och dynamiskt gränssnitt för nästan alla organisationer på Internet. Förfrågningar till program som körs ovanpå App Service går igenom flera gatewayer som distribueras i Azure-datacenter runtom i världen och som ansvarar för att dirigera varje begäran till motsvarande program. 

Azure Security Center kan köra utvärderingar och rekommendationer för dina program som körs i App Service i sand boxen i din virtuella dator eller på begäran-instanser. Genom att dra nytta av den synlighet som Azure har som moln leverantör analyserar Security Center App Service interna loggar för att övervaka vanliga attacker för webb program som ofta körs över flera mål.

Security Center utnyttjar molnets skala för att identifiera attacker på dina App Service program och fokuserar på nya attacker, medan angripare är på rekognosering-fasen och söker efter sårbarheter på flera webbplatser som finns på Azure. Security Center använder analyser och maskin inlärnings modeller för att hantera alla gränssnitt som gör det möjligt för kunder att interagera med sina program, oavsett om de är över HTTP eller via en hanterings metod. Som första parts service i Azure är Security Center också i en unik position för att erbjuda värdbaserade säkerhets analyser som täcker underliggande datornoder för den här PaaS, vilket gör att Security Center kan upptäcka attacker mot webb program som redan utnyttjat.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en App Service-plan associerad med dedikerade datorer för att övervaka och skydda din App Service. Dessa planer är: Basic, Standard, Premium, Isolerad eller Linux. Azure Security Center stöder inte planerna Kostnadsfri, Delad eller Förbrukning. Mer information finns i [App Service-planer](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Security Center skydd

Azure Security Center skyddar den VM-instans där App Service körs och hanterings gränssnittet. Den övervakar också förfrågningar och svar som skickas till och från dina appar som körs i App Service.

Security Center är internt integrerat med App Service, vilket eliminerar behovet av distribution och onboarding – integrationen är helt transparent.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Aktivera övervakning och skydd av App Service

1. I Azure väljer du Security Center.
2. Gå till **pris & inställningar** och välj en prenumeration.
3. Under **pris nivå**, i den **App Service** -rad, växlar du planen till **aktive rad**.

![Växla App Service](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Antalet instanser som anges för din resurs kvantitet representerar antalet relevanta instanser av app service som är aktiva när du öppnade bladet pris nivå. Eftersom det här antalet kan ändras baserat på de skalnings alternativ som du har valt så ändras antalet instanser som du debiteras för.

Om du vill inaktivera övervakning och rekommendationer för din App Service upprepar du den här processen och växlar **app Services** planen till **inaktive rad**.



## <a name="see-also"></a>Se också
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ställer in säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets inställningar i Azure Security Center.
* [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [App Services](security-center-virtual-machine-protection.md#app-services):  Visa en lista över App Service-miljöer med hälso översikter.
* [Övervaka partner lösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Azure Security Center vanliga frågor och svar](security-center-faq.md): Här finns vanliga frågor om att använda tjänsten.
* [Azure-säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
