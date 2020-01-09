---
title: Priser för Azure Security Center-nivåer
description: Azure Security Center erbjuds på två nivåer – kostnads fri och standard. Den här sidan visar hur du uppgraderar från kostnads fritt till standard.
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
ms.openlocfilehash: 9979a672e8a149fb384d0142659a19b8227647fa
ms.sourcegitcommit: 541e6139c535d38b9b4d4c5e3bfa7eef02446fdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75667478"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Uppgradera till standard nivån för förbättrad säkerhet
Azure Security Center har en enhetlig säkerhetshantering och avancerat skydd mot hot för arbetsbelastningar som körs i Azure, lokalt och i andra moln. Den ger insyn och kontroll över hybrid moln arbets belastningar, aktiva försvar som minskar exponeringen för hot och intelligent identifiering som hjälper dig att hålla jämna steg med snabbt växande cyberhot-attacker.

## <a name="pricing-tiers"></a>Prisnivåer
Security Center finns på två nivåer:

- Den **kostnads fria** nivån är aktive rad på alla dina Azure-prenumerationer när du besöker Azure Security Center instrument panelen i Azure Portal för första gången, eller om den har Aktiver ATS via programmering via API. Den kostnads fria nivån tillhandahåller säkerhets principer, kontinuerlig säkerhets utvärdering och åtgärds bara säkerhets rekommendationer som hjälper dig att skydda dina Azure-resurser.
- **Standard** nivån utökar funktionerna i den kostnads fria nivån till arbets belastningar som körs i privata och andra offentliga moln, vilket ger enhetlig säkerhets hantering och skydd mot hot i dina hybrid moln arbets belastningar. På standard-nivån läggs även avancerade hot identifierings funktioner till, som använder inbyggd beteende analys och maskin inlärning för att identifiera attacker och användning av noll dagar, åtkomst och program kontroller för att minska exponeringen för nätverks attacker och skadlig kod. kraftfull. Dessutom lägger standard nivån till sårbarhets genomsökning för dina virtuella datorer. Du kan prova standard nivån kostnads fritt. Security Center standard stöder Azure-resurser, inklusive virtuella datorer, skalnings uppsättningar för virtuella datorer, App Service, SQL-servrar och lagrings konton. Om du har Azure Security Center standard kan du välja att få slut på support baserat på resurs typ. 

De flesta av de kostnads fria säkerhets utvärderingarna för virtuella datorer för virtuella datorer, liksom många av säkerhets aviseringar på standard nivå, kräver installation av MMA-funktionen (Microsoft Monitoring Agent). Du kan aktivera automatisk etablering på Security Center för att automatiskt distribuera agenten för dina virtuella Azure-datorer.

## <a name="try-standard-free-for-30-days"></a>Prova standard kostnads fritt i 30 dagar
Standard-nivån är kostnadsfri de första 30 dagarna. Om du väljer att fortsätta använda tjänsten i slutet av 30 dagar börjar vi automatiskt att debitera för användning.

Du kan uppgradera en hel Azure-prenumeration till standard nivån, som ärvs av alla resurser i prenumerationen.

Så här hämtar du standard nivån:

1. Välj **pris & inställningar** på **Security Center** huvud menyn.
2. Välj den prenumeration som du vill uppgradera till standard.
3. Välj **Prisnivå**.
4. Välj **standard** som ska uppgraderas.
5. Klicka på **Spara**.

[Priser för ![Security Center](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> Om du vill aktivera alla funktioner i Security Center, måste du aktivera Standardprisnivån för prenumerationen som innehåller de tillämpliga virtuella datorerna. Om du konfigurerar priser för en arbets yta aktive ras inte just-in-Time VM-åtkomst, anpassningsbara program kontroller och nätverks identifieringar för Azure-resurser.
>

## <a name="why-upgrade-to-standard"></a>Varför uppgradera till standard?
Security Center erbjuder förbättrat skydd mot säkerhet och hot för dina hybrid moln arbets belastningar, inklusive:

- **Hybrid säkerhet** – få en enhetlig vy över säkerheten i alla dina lokala och molnbaserade arbets belastningar. Tillämpa säkerhets principer och utvärdera kontinuerligt säkerheten för dina hybrid moln arbets belastningar för att säkerställa efterlevnaden av säkerhets standarder. Samla in, Sök och analysera säkerhets data från flera källor, inklusive brand väggar och andra partner lösningar.
- **Avancerad hot identifiering** – Använd avancerad analys och Microsoft Intelligent Security Graph för att få en gräns för de växande cyberhot-angrepp. Utnyttja inbyggda beteendeanalyser och maskinlärning för att identifiera attacker och nolldagarshot. Övervaka nätverk, datorer och molntjänster för att se inkommande attacker och aktiviteter efter intrång. Effektiviseras undersökningen med interaktiva verktyg och kontextbaserad hotinformation.
- **Sårbarhets sökning för virtuella datorer** – du kan enkelt distribuera en skanner till alla virtuella datorer som tillhandahåller branschens mest avancerade lösning för sårbarhets hantering. Visa, Undersök och åtgärda resultaten direkt i Security Center. 
- **Åtkomst-och program kontroller** – blockera skadlig kod och andra oönskade program genom att använda Machine Learning-vit listning rekommendationer som är anpassade till dina specifika arbets belastningar. Minska nätverks attack ytan med just-in-Time-kontrollerad åtkomst till hanterings portar på virtuella Azure-datorer. Detta minskar drastiskt exponeringen för brute force och andra nätverks attacker.
- **Säkerhets funktioner för behållare** – dra nytta av sårbarhets hantering och hot identifiering i real tid i dina behållares miljöer. När du aktiverar resursen behållar register kan det ta upp till 12hrs tills alla funktioner har Aktiver ATS.


## <a name="next-steps"></a>Nästa steg
I den här artikeln introducerades priser för Security Center. Om du vill veta mer om standard nivån för förbättrad säkerhet och Avancerat skydd, se:

- [Avancerad hotidentifiering](security-center-threat-report.md)
- [Just-in-Time VM Access Control](security-center-just-in-time.md)
- [Översikt över container säkerhet](container-security.md)
- [Pris information i valfri valuta och enligt din region](https://azure.microsoft.com/pricing/details/security-center/)