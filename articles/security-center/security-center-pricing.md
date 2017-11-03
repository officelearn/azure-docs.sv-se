---
title: "Uppgradera till standardnivån om Security Center för ökad säkerhet | Microsoft Docs"
description: "Den här artikeln innehåller information om priser för Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: terrylan
ms.openlocfilehash: 0a8fb526602692db6737842c24649b686bea5dad
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Uppgradera till standardnivån om Security Center för ökad säkerhet
Azure Security Center ger enhetlig hantering och avancerade threat protection för arbetsbelastningar som körs i Azure, lokalt och i andra moln. Den ger synlighet och kontroll över hybrid cloud arbetsbelastningar, aktiva försvar som minskar din utsättas för hot och intelligent identifiering som hjälper dig att hålla jämna steg med utvecklas snabbt cyber attacker.

## <a name="pricing-tiers"></a>Prisnivåer
Security Center erbjuds i två nivåer:

- Den **lediga** nivå aktiveras automatiskt på alla Azure-prenumerationer och ger säkerhetsprincip och kontinuerlig security assessment tillämplig säkerhetsrekommendationer som hjälper dig att skydda dina Azure-resurser.
- Den **Standard** nivå utökar funktionerna för den kostnadsfria nivån för arbetsbelastningar som körs i privata och andra offentliga moln, tillhandahåller enhetlig hantering och hot säkerhetsskydd över hybrid cloud arbetsbelastningar. Standardnivån lägger också till advanced threat identifieringsfunktionerna, och som använder inbyggda beteendeanalys och maskininlärning att identifiera attacker och noll-dagars kryphål, åtkomst och programmet kontroller för att minska exponeringen för nätverksattacker och skadlig kod, och Mer. Standardnivån är gratis för de första 60 dagarna.

Mer information finns i Security Center [sida med priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="try-standard-free-for-60-days"></a>Försök Standard gratis under 60 dagar
Standardnivån erbjuds kostnadsfritt de första 60 dagarna. I slutet av 60 dagar startar om du väljer att fortsätta använda tjänsten, vi automatiskt avgifter för användning.

Du kan uppgradera en hela Azure-prenumeration till standardnivån som ärvs av alla resurser i prenumerationen, eller så kan du definiera en unik princip för att uppgradera en viss resursgrupp endast.

Hämta standardnivån:

1. Välj **säkerhetsprincip** på den **Security Center** huvudmenyn.
2. Välj den prenumeration som du vill uppgradera till Standard.
3. På den **säkerhetsprincip** bladet väljer **prisnivå**.
4. Välj **Standard** att uppgradera.
5. Klicka på **Spara**.

![Säkerhetsincident][1]

> [!NOTE]
> Om du vill aktivera alla funktioner i Security Center, måste du koppla Standardprisnivån till prenumeration eller resursgrupp som innehåller de tillämpliga virtuella datorerna. Konfigurera priser för en arbetsyta aktiverar inte just-in-time VM-åtkomst och anpassningsbar programkontroller nätverket identifieringar för Azure-resurser.
>
>

## <a name="why-upgrade-to-standard"></a>Varför uppgradera till Standard?
Security Center ger förbättrad säkerhet och skydd för dina hybrid cloud arbetsbelastningar, inklusive:

- **Hybrid säkerhet** – få en samlad bild av säkerhet för alla dina lokala och molnet arbetsbelastningar. Tillämpa säkerhetsprinciper och kontinuerligt utvärdera säkerheten för dina hybrid cloud arbetsbelastningar för efterlevnad säkerhetskrav. Samla in, sök efter och analysera säkerhetsdata från flera olika källor, till exempel brandväggar och andra partnerlösningar.
- **Avancerade hotidentifiering** -använda avancerade analyser och Microsoft Intelligent säkerhet Graph för att hämta en kant över utvecklas cyber-attacker.  Utnyttja inbyggda beteendeanalyser och maskinlärning för att identifiera attacker och nolldagarshot. Övervaka nätverk, datorer och molntjänster för att se inkommande attacker och aktiviteter efter intrång. Effektiviseras undersökningen med interaktiva verktyg och kontextbaserad hotinformation.
- **Åtkomst-och** -Block skadlig kod och andra oönskade program genom att använda vitlistning rekommendationer anpassade till din specifika arbetsbelastningar och drivs av machine learning. Minska nätverket risken för angrepp med just-in-time, kontrollerad åtkomst till hanteringsportar på Azure Virtual Machines, vilket drastiskt minskar risken för brute force och andra nätverksattacker.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har introducerats du priser för Security Center. Mer information om standardnivån förbättrad säkerhet och skydd avancerade finns:

- [Avancerad hotidentifiering](security-center-threat-report.md)
- [Precis i tid VM åtkomstkontroll](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
