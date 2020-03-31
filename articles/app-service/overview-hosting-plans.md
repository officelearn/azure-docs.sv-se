---
title: App Service-planer
description: Läs om hur App Service-abonnemang fungerar i Azure App Service, hur de faktureras kunden och hur du skalar dem efter dina behov.
keywords: apptjänst, azure app-tjänst, skala, skalbar, skalbarhet, apptjänstplan, apptjänstkostnad
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 11/09/2017
ms.custom: seodec18
ms.openlocfilehash: f1012f8c00de4b19bbf6206408ec1a806e09e54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482351"
---
# <a name="azure-app-service-plan-overview"></a>Översikt över Azure App Service-plan

I App Service körs en app i en _App Service-plan_. En App Service-plan definierar en uppsättning beräkningsresurser som en webbapp ska köra. Dessa beräkningsresurser är analoga med [_servergruppen_](https://wikipedia.org/wiki/Server_farm) i konventionellt webbhotell. En eller flera appar kan konfigureras för att köras på samma datorresurser (eller i samma App Service-plan).

När du skapar en App Service-plan i en viss region (till exempel Västeuropa) skapas en uppsättning beräkningsresurser för den planen i den regionen. Oavsett vilka appar du lägger in i den här App Service-planen körs på dessa beräkningsresurser enligt definitionen i apptjänstplanen. Varje App Service-plan definierar:

- Region (USA, västra, USA, östra osv.)
- Antal VM-instanser
- Storleken på VM-instanser (liten, medel, stor)
- Prisnivå (kostnadsfri, delad, grundläggande, standard, premium, PremiumV2, isolerad)

_Prisnivån för_ en App Service-plan avgör vilka App Service-funktioner du får och hur mycket du betalar för planen. Det finns ett antal kategorier för prisnivåer:

- **Delad beräkning:** **Kostnadsfri** och **delad**, de två basnivåerna, kör en app på samma Virtuella Azure-dator som andra App Service-appar, inklusive appar för andra kunder. Dessa nivåer allokerar CPU-kvoter till varje app som körs på de delade resurserna, och de resurserna kan inte skalas om.
- **Dedikerad beräkning:** Nivåerna **Basic,** **Standard,** **Premium**och **PremiumV2** kör appar på dedikerade virtuella Azure-datorer. Det är bara appar i samma App Service-plan som delar samma beräkningsresurser. Ju högre nivå, desto fler VM-instanser blir tillgängliga som du kan skala ut.
- **Isolerad:** Den här nivån kör dedikerade virtuella Azure-datorer på dedikerade virtuella Azure-nätverk. Det ger nätverksisolering ovanpå beräkningsisolering till dina appar. Den ger maximalt med utskalningsfunktioner.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Varje nivå innehåller också en specifik delmängd av App Service-funktioner. Dessa funktioner omfattar anpassade domäner och SSL-certifikat, automatisk skalning, distributionsplatser, säkerhetskopior, Traffic Manager-integrering med mera. Ju högre nivå, desto fler funktioner är tillgängliga. Information om vilka funktioner som stöds på varje prisnivå finns i [Information om App Service-planen](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Den nya **PremiumV2-prisnivån** ger [virtuella datorer i Dv2-serien](../virtual-machines/dv2-dsv2-series.md) med snabbare processorer, SSD-lagring och dubbelt förhållande mellan minne och kärna jämfört med **standardnivå.** **PremiumV2** stöder också högre skala via ökat antal instanser samtidigt som alla avancerade funktioner som finns i standardplanen. Alla funktioner som är tillgängliga på den befintliga **Premium-nivån** ingår i **PremiumV2.**
>
> På samma sätt som andra dedikerade nivåer är tre VM-storlekar tillgängliga för den här nivån:
>
> - Liten (en CPU-kärna, 3,5 GiB minne) 
> - Medium (två CPU-kärnor, 7 GiB minne) 
> - Stor (fyra CPU-kärnor, 14 GiB minne)  
>
> Prisinformation för **PremiumV2** finns i [Priser för App Service](https://azure.microsoft.com/pricing/details/app-service/).
>
> Mer om du vill komma igång med den nya **PremiumV2-prisnivån** finns i [Konfigurera PremiumV2-nivå för App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Hur körs och skalas min app?

På nivåerna **Fri** och **Delad** tar en app emot CPU-minuter på en delad VM-instans och kan inte skala ut. På andra nivåer körs och skalas en app enligt följande.

När du skapar en app i App Service läggs den till i en apptjänstplan. När appen körs körs den på alla VM-instanser som konfigurerats i App Service-planen. Om flera appar finns i samma App Service-plan delar de alla samma VM-instanser. Om du har flera distributionsplatser för en app körs även alla distributionsplatser på samma VM-instanser. Om du aktiverar diagnostikloggar, utför säkerhetskopior eller kör WebJobs använder de också CPU-cykler och minne på dessa VM-instanser.

På så sätt är App Service-planen skalenheten för App Service-apparna. Om planen är konfigurerad för att köra fem VM-instanser körs alla appar i planen på alla fem instanserna. Om planen är konfigurerad för automatisk skalning skalas alla appar i planen ut tillsammans baserat på inställningarna för automatisk skalning.

Information om hur du skalar ut en app finns i [Skala instansantal manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Hur mycket kostar min App Service-plan?

I det här avsnittet beskrivs hur App Service-appar faktureras. Detaljerad, regionspecifik prisinformation finns i [Prissättningen för App-tjänster](https://azure.microsoft.com/pricing/details/app-service/).

Med undantag för **den kostnadsfria** nivån medför en apptjänstplan en timavgift på de beräkningsresurser som används.

- På den **delade** nivån får varje app en kvot med CPU-minuter, så _varje app_ debiteras varje timme för CPU-kvoten.
- I de dedikerade beräkningsnivåerna (**Basic**, **Standard**, **Premium**, **PremiumV2**) definierar App Service-planen antalet VM-instanser som apparna skalas till, så _varje VM-instans_ i App Service-planen har en timavgift. Dessa VM-instanser debiteras på samma sätt oavsett hur många appar som körs på dem. Mer om du vill undvika oväntade debiteringar finns i [Rensa en apptjänstplan](app-service-plan-manage.md#delete).
- På den **isolerade** nivån definierar App Service-miljön antalet isolerade arbetare som kör dina appar och _varje anställd_ debiteras varje timme. Dessutom finns det en timbasavgift för själva apptjänstmiljön.

Du debiteras inte för att du använder apptjänstfunktionerna som är tillgängliga för dig (konfigurera anpassade domäner, SSL-certifikat, distributionsplatser, säkerhetskopior osv.). Undantagen är:

- App Service Domäner - du betalar när du köper en i Azure och när du förnyar den varje år.
- App Service-certifikat - du betalar när du köper ett i Azure och när du förnyar det varje år.
- IP-baserade SSL-anslutningar - Det finns en timavgift för varje IP-baserad SSL-anslutning, men vissa **standardnivåer** eller högre ger dig en IP-baserad SSL-anslutning gratis. SNI-baserade SSL-anslutningar är gratis.

> [!NOTE]
> Om du integrerar App Service med en annan Azure-tjänst kan du behöva överväga avgifter från dessa andra tjänster. Om du till exempel använder Azure Traffic Manager för att skala din app geografiskt debiterar Azure Traffic Manager dig också baserat på din användning. Information om hur du beräknar kostnaden för dina tjänster mellan tjänster i Azure finns i [Priskalkylator](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Vad händer om min app behöver fler funktioner?

App Service-planen kan skalas upp eller ned när som helst. Det är så enkelt som att ändra prisnivån i planen. Du kan välja en lägre prisnivå först och skala upp senare när du behöver fler App Service-funktioner.

Du kan till exempel börja testa din webbapp i en **kostnadsfri** apptjänstplan och inte betala någonting. När du vill lägga till ditt [anpassade DNS-namn](app-service-web-tutorial-custom-domain.md) i webbappen skalar du bara upp planen till **delad** nivå. Senare, när du vill [skapa en SSL-bindning,](configure-ssl-bindings.md)skala din plan upp till **Grundläggande** nivå. När du vill ha [mellanlagringsmiljöer skalas](deploy-staging-slots.md)du upp till **standardnivå.** När du behöver fler kärnor, minne eller lagring skalar du upp till en större vm-storlek på samma nivå.

Detsamma fungerar i det omvända. När du känner att du inte längre behöver funktionerna eller funktionerna på en högre nivå kan du skala ned till en lägre nivå, vilket sparar pengar.

Information om hur du skalar upp App Service-planen finns [i Skala upp en app i Azure](manage-scale-up.md).

Om din app finns i samma App Service-plan med andra appar kanske du vill förbättra appens prestanda genom att isolera beräkningsresurserna. Du kan göra det genom att flytta appen till en separat App Service-plan. Mer information finns i [Flytta en app till en annan App Service-plan](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Ska jag lägga en app i en ny plan eller en befintlig plan?

Eftersom du betalar för de datorresurser som apptjänstplanen allokerar (se [Hur mycket kostar min App Service-plan?](#cost)) kan du eventuellt spara pengar genom att placera flera appar i en App Service-plan. Du kan fortsätta att lägga till appar i en befintlig plan så länge planen har tillräckligt med resurser för att hantera belastningen. Tänk dock på att appar i samma App Service-plan alla delar samma beräkningsresurser. För att avgöra om den nya appen har de resurser som krävs måste du känna till kapaciteten för den befintliga App Service-planen och den förväntade belastningen för den nya appen. Om en App Service-plan överbelastas kan det eventuellt medföra driftstopp för dina nya och befintliga appar.

Isolera din app till en ny App Service-plan när:

- Appen är resurskrävande.
- Du vill skala appen oberoende av de andra apparna i den befintliga planen.
- Appen behöver resurser i en annan geografisk region.

På så sätt kan du allokera en ny uppsättning resurser för din app och få större kontroll över dina appar.

## <a name="manage-an-app-service-plan"></a>Hantera en apptjänstplan

> [!div class="nextstepaction"]
> [Hantera en apptjänstplan](app-service-plan-manage.md)
