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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 393e769f147629d2d40344052ba502b22a18cd2a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314546"
---
# <a name="pricing-of-azure-security-center"></a>Prissättning för Azure Security Center
Azure Security Center har en enhetlig säkerhetshantering och avancerat skydd mot hot för arbetsbelastningar som körs i Azure, lokalt och i andra moln. Den ger insyn och kontroll över hybrid moln arbets belastningar, aktiva försvar som minskar exponeringen för hot och intelligent identifiering som hjälper dig att hålla jämna steg med snabbt växande cyberhot-attacker.


## <a name="free-option-vs-azure-defender-enabled"></a>Gratis alternativ kontra Azure Defender aktiverat

Security Center erbjuds i två lägen:

- **Azure Defender off** (kostnads fri) – Security Center utan Azure Defender är aktiverat för alla dina Azure-prenumerationer när du besöker Azure Security Center instrument panelen i Azure Portal för första gången, eller om den har Aktiver ATS via programmering via API. Med det här kostnads fria läget kan du skydda dina Azure-resurser genom att använda säkerhets principer, kontinuerlig säkerhets utvärdering och åtgärds bara säkerhets rekommendationer.

- **Azure Defender on** -aktivera Azure Defender utökar funktionerna i det kostnads fria läget till arbets belastningar som körs i privata och andra offentliga moln, vilket ger enhetlig säkerhets hantering och skydd mot hot i dina hybrid moln arbets belastningar. Några av de viktigaste funktionerna i Azure Defender:

    - **Hybrid säkerhet** – få en enhetlig vy över säkerheten i alla dina lokala och molnbaserade arbets belastningar. Tillämpa säkerhets principer och utvärdera kontinuerligt säkerheten för dina hybrid moln arbets belastningar för att säkerställa efterlevnaden av säkerhets standarder. Samla in, Sök och analysera säkerhets data från flera källor, inklusive brand väggar och andra partner lösningar.
    - **Hot skydds aviseringar** – avancerad beteende analys och Microsoft Intelligent Security Graph ger en gräns för de växande cyberhot-angrepp. Använd inbyggd beteende analys och maskin inlärning för att identifiera attacker och den långsiktiga ingreppen. Övervaka nätverk, datorer och moln tjänster för inkommande attacker och aktiviteter efter intrång. Effektivisera undersökningen med interaktiva verktyg och sammanhangsbaserad Hot information.
    - **Sårbarhets sökning för virtuella datorer och behållar register** – du kan enkelt distribuera en skanner till alla virtuella datorer som tillhandahåller branschens mest avancerade lösning för sårbarhets hantering. Visa, Undersök och åtgärda resultaten direkt i Security Center. 
    - **Åtkomst-och program kontroller** – blockera skadlig kod och andra oönskade program genom att använda Machine Learning-baserade rekommendationer som är anpassade till dina specifika arbets belastningar för att skapa listor över tillåtna och nekade. Minska nätverks attack ytan med just-in-Time-kontrollerad åtkomst till hanterings portar på virtuella Azure-datorer. Detta minskar drastiskt exponeringen för brute force och andra nätverks attacker.
    - **Behållar säkerhetsfunktioner** – dra nytta av sårbarhets hantering och skydd mot Real tids skydd i dina behållares miljöer. När du aktiverar **Azure Defender för behållar register**kan det ta upp till 12hrs tills alla funktioner har Aktiver ATS. Avgifterna baseras på antalet unika behållar avbildningar som push-överförts till det anslutna registret. När en bild har genomsökts en gång, kommer du inte att debiteras för den igen om den inte ändras och skickas en gång till. 

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
> Om du vill aktivera alla Security Center funktioner, inklusive hot skydds funktioner, måste du aktivera Azure Defender på prenumerationen som innehåller de aktuella arbets belastningarna. Att aktivera den på arbets ytans nivå ger inte just-in-Time-åtkomst till virtuella datorer, anpassningsbara program kontroller och nätverks identifieringar för Azure-resurser. 
>
> Du kan aktivera **Azure Defender för lagrings konton** på antingen prenumerations nivå eller resurs nivå.
> Du kan aktivera **Azure Defender för SQL** på antingen prenumerations nivån eller resurs nivån.
> Du kan aktivera skydd mot hot för **Azure Database for MariaDB/MySQL/postgresql** endast på resurs nivå.


## <a name="next-steps"></a>Nästa steg
I den här artikeln introducerades priser för Security Center. Information om relaterade material finns i:

- [Optimera dina Azure-arbetsbelastnings kostnader](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Pris information i valfri valuta och enligt din region](https://azure.microsoft.com/pricing/details/security-center/)
- Du kanske vill hantera dina kostnader och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter. Med [lösnings mål](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du tillämpa en omfattning för lösningen och rikta in dig på en delmängd av datorerna i arbets ytan. Om du använder lösnings mål, Security Center visar arbets ytan som en lösning.