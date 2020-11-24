---
title: Prissättning för Azure Security Center
description: Azure Security Center erbjuds i två lägen med och utan Azure Defender.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: bc17b63ef8f83e4086262d52334a518ec85f4fc6
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95792611"
---
# <a name="pricing-of-azure-security-center"></a>Prissättning för Azure Security Center
Azure Security Center har en enhetlig säkerhetshantering och avancerat skydd mot hot för arbetsbelastningar som körs i Azure, lokalt och i andra moln. Den ger insyn och kontroll över hybrid moln arbets belastningar, aktiva försvar som minskar exponeringen för hot och intelligent identifiering som hjälper dig att hålla jämna steg med snabbt växande cyberhot-risker.


## <a name="free-option-vs-azure-defender-enabled"></a>Gratis alternativ kontra Azure Defender aktiverat

Security Center erbjuds i två lägen:

- **Azure Defender off** (kostnads fri) – Security Center utan Azure Defender är aktiverat för alla dina Azure-prenumerationer när du besöker Azure Security Center instrument panelen i Azure Portal för första gången, eller om den har Aktiver ATS via programmering via API. Med det här kostnads fria läget kan du skydda dina Azure-resurser genom att använda säkerhets principer, kontinuerlig säkerhets utvärdering och åtgärds bara säkerhets rekommendationer.

- **Azure Defender on** -aktivera Azure Defender utökar funktionerna i det kostnads fria läget till arbets belastningar som körs i privata och andra offentliga moln, vilket ger enhetlig säkerhets hantering och skydd mot hot i dina hybrid moln arbets belastningar. Några av de viktigaste funktionerna i Azure Defender:

    - **Microsoft Defender för slut punkt** – Azure Defender for-servrar innehåller [Microsoft Defender för slut punkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender) för omfattande slut punkts identifiering och-svar (EDR). Lär dig mer om fördelarna med att använda Microsoft Defender för slut punkt tillsammans med Azure Defender som [används Security Center s integrerade EDR-lösning](security-center-wdatp.md).
    - **Sårbarhets sökning för virtuella datorer och behållar register** – du kan enkelt distribuera en skanner till alla virtuella datorer som tillhandahåller branschens mest avancerade lösning för sårbarhets hantering. Visa, Undersök och åtgärda resultaten direkt i Security Center. 
    - **Hybrid säkerhet** – få en enhetlig vy över säkerheten i alla dina lokala och molnbaserade arbets belastningar. Tillämpa säkerhets principer och utvärdera kontinuerligt säkerheten för dina hybrid moln arbets belastningar för att säkerställa efterlevnaden av säkerhets standarder. Samla in, Sök och analysera säkerhets data från flera källor, inklusive brand väggar och andra partner lösningar.
    - **Hot skydds aviseringar** – avancerad beteende analys och Microsoft Intelligent Security Graph ger en gräns för de växande cyberhot-angrepp. Inbyggd beteende analys och maskin inlärning kan identifiera attacker och utnyttjandet på noll dagar. Övervaka nätverk, datorer och moln tjänster för inkommande attacker och aktiviteter efter intrång. Effektivisera undersökningen med interaktiva verktyg och sammanhangsbaserad Hot information.
    - **Åtkomst-och program kontroller** (AAC) – blockera skadlig kod och andra oönskade program genom att använda rekommendationer för Machine Learning som är anpassade till dina specifika arbets belastningar för att skapa listor över tillåtna och nekade. Minska nätverks attack ytan med just-in-Time-kontrollerad åtkomst till hanterings portar på virtuella Azure-datorer. AAC minskar drastiskt exponeringen med brute force och andra nätverks attacker.
    - **Behållar säkerhetsfunktioner** – dra nytta av sårbarhets hantering och skydd mot Real tids skydd i dina behållares miljöer. När du aktiverar **Azure Defender för behållar register** kan det ta upp till 12 timmar tills alla funktioner har Aktiver ATS. Avgifterna baseras på antalet unika behållar avbildningar som push-överförts till det anslutna registret. När en bild har genomsökts en gång, kommer du inte att debiteras för den igen om den inte ändras och skickas en gång till. 

## <a name="try-azure-defender-free-for-30-days"></a>Prova Azure Defender kostnadsfritt i 30 dagar
Azure Defender är kostnads fritt under de första 30 dagarna. Om du väljer att fortsätta använda tjänsten i slutet av 30 dagar börjar vi automatiskt att debitera för användning.

## <a name="enable-azure-defender"></a>Aktivera Azure Defender
Du kan skydda en hel Azure-prenumeration med Azure Defender och skyddet kommer att ärvas av alla resurser i prenumerationen.

Så här aktiverar du Azure Defender:

1. Från Security Centerens huvud meny väljer du **pris & inställningar**.
1. Välj den prenumeration som du vill uppgradera.
1. Välj **Azure Defender på** för att uppgradera.
1. Välj **Spara**.

Nedan visas pris sidan för en exempel prenumeration. Du märker att varje plan i Azure Defender priss ätts separat och kan ställas in på eller av separat.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Security Center sidan med priser i portalen":::

> [!NOTE]
> Om du vill aktivera alla Security Center funktioner, inklusive hot skydds funktioner, måste du aktivera Azure Defender på prenumerationen som innehåller de aktuella arbets belastningarna. Att aktivera den på arbets ytans nivå ger inte just-in-Time-åtkomst till virtuella datorer, anpassningsbara program kontroller och nätverks identifieringar för Azure-resurser. Dessutom är de enda Azure Defender-planer som är tillgängliga på arbets ytans nivå Azure Defender för servrar och Azure Defender för SQL-servrar på datorer.
>
> Du kan aktivera **Azure Defender för lagrings konton** på antingen prenumerations nivå eller resurs nivå.
> Du kan aktivera **Azure Defender för SQL** på antingen prenumerations nivån eller resurs nivån.
> Du kan aktivera skydd mot hot för **Azure Database for MariaDB/MySQL/postgresql** endast på resurs nivå.


## <a name="faq---pricing-and-billing"></a>Vanliga frågor och svar – priser och fakturering 

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-azure-security-center"></a>Hur kan jag spåra vem i organisationen aktiverade Azure Defender-ändringar i Azure Security Center
Azure-prenumerationer kan ha flera administratörer som har behörighet att ändra pris inställningarna. Om du vill ta reda på vilken användare som har gjort en ändring använder du Azure-aktivitets loggen.

Om användarens info inte visas i kolumnen **händelse som initieras av** , undersöker du händelsen för att se om det finns relevant information.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Händelse loggen i Azure visar en händelse för pris ändring":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Vilka är de planer som erbjuds av Security Center? 
Security Center har två erbjudanden: 

- Kostnadsfritt Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Hur aktiverar jag Azure Defender för min prenumeration? 
Du kan använda något av följande sätt för att aktivera Azure Defender för din prenumeration: 

|Metod  |Instruktioner  |
|---------|---------|
|Azure Security Center sidor i Azure Portal|[Aktivera Azure Defender](#enable-azure-defender)|
|REST-API|[Prissättnings-API](https://docs.microsoft.com/rest/api/securitycenter/pricings)|
|Azure CLI|[AZ Security-prissättning](https://docs.microsoft.com/cli/azure/security/pricing)|
|PowerShell|[Set-AzSecurityPricing](https://docs.microsoft.com/powershell/module/az.security/set-azsecuritypricing)|
|Azure Policy|[Paket priser](https://github.com/Azure/Azure-Security-Center/tree/master/Pricing%20%26%20Settings/Azure%20Policy%20definitions/Bundle%20Pricings)|
|||

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Kan jag aktivera Azure Defender för en delmängd av servrarna i min prenumeration?
Nej. När du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) i en prenumeration kommer alla servrar i prenumerationen att skyddas av Azure Defender. 

Ett alternativ är att aktivera Azure Defender för servrar på Log Analytics arbets ytans nivå. Om du gör detta kommer endast servrar som rapporterar till arbets ytan att skyddas och faktureras. Flera funktioner är dock inte tillgängliga. Dessa inkluderar just-in-Time VM-åtkomst, nätverks identifiering, regelefterlevnad, anpassad nätverks härdning, anpassningsbar program kontroll och mycket annat. 


### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Min prenumeration har Azure Defender för servrar aktiverat, betalar jag för servrar som inte är igång? 
Nej. När du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) i en prenumeration faktureras du per timme för endast servrar som körs. Du debiteras inte för någon server som är inaktive rad, under den tid det tar. 

> [!TIP]
> Detta gäller även för de andra resurs typer som skyddas av Security Center. 

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Kommer jag att debiteras för datorer utan att Log Analytics-agenten är installerad?
Ja. När du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) i en prenumeration får datorerna i den prenumerationen ett antal skydd även om du inte har installerat Log Analytics agenten.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Kommer jag att debiteras två gånger om en Log Analytics agent rapporterar till flera arbets ytor? 
Ja. Om du har konfigurerat Log Analytics-agenten för att skicka data till två eller flera olika Log Analytics arbets ytor (Multi-värdar) debiteras du för alla arbets ytor som har en säkerhets-eller Antiskadlig kod-lösning installerad. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Om en Log Analytics agent rapporterar till flera arbets ytor, är 500 MB kostnads fri data inmatning tillgänglig på alla?
Ja. Om du har konfigurerat Log Analytics-agenten för att skicka data till två eller flera olika Log Analytics arbets ytor (Multi-värdar) får du 500 MB kostnads fri data inmatning. Den beräknas per nod, per rapporterad arbets yta, per dag och tillgänglig för alla arbets ytor som har en säkerhets-eller Antiskadlig kod-lösning installerad. Du debiteras för alla data som matas in över 500 MB.


## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras Security Center pris alternativ. För relaterat material, se:

- [Optimera dina Azure-arbetsbelastnings kostnader](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Pris information i valfri valuta och enligt din region](https://azure.microsoft.com/pricing/details/security-center/)
- Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter. Med [lösnings mål](../azure-monitor/insights/solution-targeting.md) kan du tillämpa en omfattning för lösningen och rikta in dig på en delmängd av datorerna i arbets ytan. Om du använder lösnings mål, Security Center visar arbets ytan som en lösning.