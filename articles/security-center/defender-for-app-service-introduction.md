---
title: Azure Defender för App Service – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för App Service.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb0e073d5ccf73434d05c801b9a8727c1d19fa47
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122242"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introduktion till Azure Defender för App Service

Azure App Service är en helt hanterad plattform för att bygga och vara värd för dina webbappar och API: er utan att behöva hantera infrastrukturen. Den ger hantering, övervakning och Operational Insights för att uppfylla prestanda-, säkerhets-och efterlevnads krav i företags klass. Mer information finns i [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Azure Defender för App Service** använder molnets skala för att identifiera angrepps mål program som körs över App Service. Angripare avsöker webb program för att hitta och utnyttja svagheter. Innan de dirigeras till vissa miljöer går förfrågningar till program som körs i Azure genom flera gatewayer, där de inspekteras och loggas. Dessa data används sedan för att identifiera sårbarheter och angripare och för att lära dig nya mönster som ska användas senare.

Genom att använda den synlighet som Azure har som moln leverantör analyserar Security Center App Service interna loggar för att identifiera angrepps metodik på flera mål. Metodik omfattar till exempel omfattande genomsökning och distribuerade attacker. Den här typen av angrepp kommer vanligt vis från en liten delmängd av IP-adresser och visar mönster för crawlning till liknande slut punkter på flera värdar. Attackerna söker efter en sårbar sida eller ett plugin-program och kan inte identifieras från en enda värds synvinkel.


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|[Azure Defender för App Service](azure-defender.md) faktureras så som visas på [sidan med priser](security-center-pricing.md)|
|App Services planer som stöds:|![Ja ](./media/icons/yes-icon.png) Basic, standard, Premium, isolerat eller Linux<br>![Inga ](./media/icons/no-icon.png) lediga, delade eller konsumtion<br>[Läs mer om App Service-planer](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Nej](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Vad är Azure Defender för App Service skydda?

När App Service plan aktive rad bedömer Security Center resurserna som omfattas av din App Service plan och genererar säkerhets rekommendationer baserat på dess resultat. Security Center skyddar den VM-instans där App Service körs och hanterings gränssnittet. Den övervakar också förfrågningar och svar som skickas till och från dina appar som körs i App Service.

Om du kör en Windows-baserad App Service plan har Security Center också åtkomst till de underliggande sand lådorna och de underliggande virtuella datorerna. Tillsammans med de loggdata som anges ovan kan infrastrukturen berätta för historien, från en ny attack som cirkulerar på vilda sätt att kompromissa med kund maskiner. Det innebär att även om Security Center distribueras efter att en webbapp har utnyttjats, kan det vara möjligt att identifiera pågående attacker.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Skydda dina Azure App Service-webbappar och API:er
För att skydda din Azure App Service-plan med Azure Defender för App Service:

- Se till att du har ett App Service plan som är associerat med dedikerade datorer. De planer som stöds anges ovan i [tillgänglighet](#availability).

- Aktivera **Azure Defender** på din prenumeration (du kan välja att bara aktivera **azure Defender för App Service** -plan) enligt beskrivningen i [Azure Security Centers prissättning](security-center-pricing.md)

Security Center är internt integrerat med App Service, vilket eliminerar behovet av distribution och onboarding – integreringen är transparent.

>[!NOTE]
> På sidan priser och inställningar visas ett antal instanser för din **resurs antal**. Detta representerar det totala antalet beräknings instanser, i alla App Service planer i den här prenumerationen, som körs vid den tidpunkt då du öppnade sidan pris nivå.
>
> Azure App Service erbjuder en rad olika planer. App Service plan definierar mängden beräknings resurser för en webbapp som ska köras. Dessa motsvarar Server grupper i konventionell webb värd. En eller flera appar kan konfigureras för att köras på samma dator resurser (eller i samma App Service plan).
>
>Om du vill validera antalet, head to App Service Planes i Azure-portalen, där du kan se antalet beräknings instanser som används av varje plan. 



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för App Service. 

Information om relaterade material finns i följande artiklar: 

- Om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Stream-aviseringar till en Siem, Soar eller IT-Tjänstehanterings lösning](export-to-siem.md).
- En lista över Azure App Service aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureappserv).
- Mer information om App Service-planer finns i [App Service planer](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Aktivera Azure Defender](security-center-pricing.md)