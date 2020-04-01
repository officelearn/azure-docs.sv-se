---
title: Prissättning av Azure Security Center-nivåer
description: Azure Security Center erbjuds i två nivåer - Gratis och Standard. Den här sidan visar hur du uppgraderar från gratis till standard.
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
ms.date: 12/13/2019
ms.author: memildin
ms.openlocfilehash: fd84058c8421d144678c91fac3e5671511d0fd4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435506"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Uppgradera till standardnivå för ökad säkerhet
Azure Security Center har en enhetlig säkerhetshantering och avancerat skydd mot hot för arbetsbelastningar som körs i Azure, lokalt och i andra moln. Det ger synlighet och kontroll över hybridmolnarbetsbelastningar, aktivt försvar som minskar din exponering för hot och intelligent identifiering som hjälper dig att hålla jämna steg med snabbt föränderliga cyberattacker.

## <a name="pricing-tiers"></a>Prisnivåer
Security Center finns på två nivåer:

- Den **kostnadsfria** nivån är aktiverad på alla dina Azure-prenumerationer när du besöker Azure Security Center-instrumentpanelen i Azure-portalen för första gången, eller om den är aktiverad programmässigt via API. Den kostnadsfria nivån ger säkerhetsprincip, kontinuerlig säkerhetsbedömning och användbara säkerhetsrekommendationer som hjälper dig att skydda dina Azure-resurser.
- **Standardnivån** utökar funktionerna på den kostnadsfria nivån till arbetsbelastningar som körs i privata och andra offentliga moln, vilket ger enhetlig säkerhetshantering och hotskydd över dina hybridmolnarbetsbelastningar. Standardnivån lägger också till hotskyddsfunktioner, som använder inbyggda beteendeanalyser och maskininlärning för att identifiera attacker och zero-day-kryphål, åtkomst- och programkontroller för att minska exponeringen för nätverksattacker och skadlig kod med mera. Dessutom lägger standardnivå till sårbarhetssökning för dina virtuella datorer. Du kan prova standardnivån gratis. Security Center-standarden stöder Azure-resurser, inklusive virtuella datorer, skalningsuppsättningar för virtuella datorer, App Service, SQL-servrar och lagringskonton. Om du har Standard för Azure Security Center kan du välja bort support baserat på resurstyp. 

De flesta av de kostnadsfria nivåsäkerhetsbedömningarna för virtuella datorer, liksom många av säkerhetsaviseringarna på standardnivå, kräver installation av log analytics-agentfunktionen. Du kan aktivera automatisk etablering på Security Center för att automatiskt distribuera agenten för dina virtuella Azure-datorer.

## <a name="try-standard-tier-free-for-30-days"></a>Prova standardnivån gratis i 30 dagar
Standardnivån är gratis under de första 30 dagarna. I slutet av 30 dagar, om du väljer att fortsätta använda tjänsten, kommer vi automatiskt att börja ta betalt för användning.

Du kan uppgradera en hel Azure-prenumeration till standardnivån, som ärvs av alla resurser i prenumerationen.

Så här hämtar du standardnivån:

1. Välj **Pris & inställningar** på huvudmenyn för **Säkerhetscenter.**
2. Välj den prenumeration som du vill uppgradera till standard.
3. Välj **prisnivå**.
4. Välj **Standard** att uppgradera.
5. Klicka på **Spara**.

[![Prissättning av säkerhetscenter](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Om du vill aktivera alla Security Center-funktioner måste du använda standardprisnivån på prenumerationen som innehåller tillämpliga virtuella datorer. Konfigurera priser för en arbetsyta gör det inte möjligt för enkel åtkomst till virtuella datorer, anpassningsbara programkontroller och nätverksidentifieringar för Azure-resurser.
>

## <a name="why-upgrade-to-standard"></a>Varför uppgradera till standard?
Security Center erbjuder förbättrat säkerhets- och hotskydd för dina hybridmolnarbetsbelastningar, inklusive:

- **Hybridsäkerhet** – Få en enhetlig säkerhetsvy över alla dina lokala och molnarbetsbelastningar. Tillämpa säkerhetsprinciper och utvärdera kontinuerligt säkerheten för dina hybridmolnarbetsbelastningar för att säkerställa efterlevnad av säkerhetsstandarder. Samla in, söka efter och analysera säkerhetsdata från flera källor, inklusive brandväggar och andra partnerlösningar.
- **Säkerhetsvarningar** – Använd avancerad analys och Microsoft Intelligent Security Graph för att få ett försprång jämfört med nya cyberattacker. Utnyttja inbyggda beteendeanalyser och maskininlärning för att identifiera attacker och zero-day-kryphål. Övervaka nätverk, datorer och molntjänster för inkommande attacker och aktivitet efter överträdelsen. Effektivisera undersökningen med interaktiva verktyg och kontextuell hotinformation.
- **Sårbarhetsskanning för virtuella datorer** – Distribuera enkelt en skanner till alla dina virtuella datorer som tillhandahåller branschens mest avancerade lösning för sårbarhetshantering. Visa, undersöka och åtgärda resultaten direkt i Security Center. 
- **Åtkomst- och programkontroller** – Blockera skadlig kod och andra oönskade program genom att tillämpa maskininlärningsdrivna vitlistningsrekommendationer som är anpassade till dina specifika arbetsbelastningar. Minska nätverksattackytan med just-in-time, kontrollerad åtkomst till hanteringsportar på virtuella Azure-datorer. Detta minskar drastiskt exponeringen för brute force och andra nätverksattacker.
- **Säkerhetsfunktioner för behållare** – Dra nytta av sårbarhetshantering och hotskydd i realtid i dina containermiljöer. När du aktiverar behållarregisterresursen kan det ta upp till 12 timmar tills alla funktioner är aktiverade.


## <a name="next-steps"></a>Nästa steg
I den här artikeln introducerades du till prissättning för Security Center. Mer information om standardnivåns förbättrade säkerhet och avancerade hotskydd finns i:

- [Skydd mot hot i Azure Security Center](threat-protection.md)
- [Åtkomstkontroll för just-in-time-vm](security-center-just-in-time.md)
- [Översikt över containersäkerhet](container-security.md)
- [Prisuppgifter i din valuta val, och enligt din region](https://azure.microsoft.com/pricing/details/security-center/)