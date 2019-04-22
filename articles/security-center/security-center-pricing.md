---
title: Uppgradera till standardnivån i Security Center för ökad säkerhet | Microsoft Docs
description: Den här artikeln innehåller information om priser för Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: monhaber
ms.openlocfilehash: 27acda2496adea39321e498868aebcc2f824df3c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905298"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Uppgradera till standardnivån i Security Center för ökad säkerhet
Azure Security Center har en enhetlig säkerhetshantering och avancerat skydd mot hot för arbetsbelastningar som körs i Azure, lokalt och i andra moln. Du får insyn och kontroll över hybridmolnarbetsbelastningar active försvar som minskar din exponering för hot och intelligent identifiering för att hålla jämna steg med snabbt växande cyberattacker.

## <a name="pricing-tiers"></a>Prisnivåer
Security Center finns två nivåer:

- Den **kostnadsfri** nivå är automatiskt aktiverat på alla Azure-prenumerationer och ger säkerhetsprincip och kontinuerlig säkerhetsbedömning handlingsbara rekommendationer för att hjälpa dig att skydda dina Azure-resurser.
- Den **Standard** nivån utökar funktionerna för den kostnadsfria nivån för arbetsbelastningar som körs i privat och andra offentliga moln, att tillhandahålla enhetlig säkerhetshantering och hotidentifiering skydd över dina hybridmolnarbetsbelastningar. Standardnivån lägger också till advanced threat funktioner, och som använder inbyggda beteendeanalys och maskininlärning att identifiera attacker och nolldagarshot, åtkomst- och programkontroller för att minska exponeringen för nätverksattacker och skadlig kod, och Mer. Du kan prova nivån Standard utan kostnad. Security Center Standard har stöd för Azure-resurser inklusive virtuella datorer, VM scale sets för, App Service, SQL-servrar och Storage-konton. Om du har Azure Security Center Standard kan du välja bort support utifrån resurstyp. 


Mer information finns i Säkerhetscenter [prissättningssidan](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="try-standard-free-for-30-days"></a>Prova Standard kostnadsfritt i 30 dagar
Standardnivån erbjuds kostnadsfritt de första 30 dagarna. I slutet av 30 dagar börjar om du väljer att fortsätta använda tjänsten, vi automatiskt att debitera för användningen.

Du kan uppgradera en hel Azure-prenumeration till standardnivån som ärvs av alla resurser i prenumerationen.

Hämta Standard-nivån:

1. Välj **säkerhetsprincip** på den **Security Center** huvudmenyn.
2. Välj den prenumeration som du vill uppgradera till Standard.
3. På den **säkerhetsprincip** bladet väljer **prisnivå**.
4. Välj **Standard** att uppgradera.
5. Klicka på **Spara**.

(Priserna i avbildningen är till exempel endast.) ![Security Center-prissättning](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> Om du vill aktivera alla funktioner i Security Center, måste du aktivera Standardprisnivån för prenumerationen som innehåller de tillämpliga virtuella datorerna. Konfigurera priser för en arbetsyta kan inte just-in-time-åtkomst till virtuell dator och anpassningsbara programkontroller nätverk identifieringar för Azure-resurser.
>
>

## <a name="why-upgrade-to-standard"></a>Varför ska du uppgradera till Standard?
Security Center erbjuder förbättrad säkerhet och skydd mot hot i dina hybridmolnarbetsbelastningar, inklusive:

- **Hybridsäkerhet** – få en enhetlig vy över säkerheten i alla dina lokala och molnbaserade arbetsbelastningar. Tillämpa säkerhetsprinciper och utvärdera säkerheten för dina hybridmolnarbetsbelastningar för att säkerställa att säkerhetsstandarder kontinuerligt. Samla in, sök efter och analysera säkerhetsdata från flera olika källor, till exempel brandväggar och andra partnerlösningar.
- **Avancerad hotidentifiering** -Använd avancerade analyser och Microsoft Intelligent Security Graph för att få en kant med utvecklas cyberattacker.  Utnyttja inbyggda beteendeanalyser och maskinlärning för att identifiera attacker och nolldagarshot. Övervaka nätverk, datorer och molntjänster för att se inkommande attacker och aktiviteter efter intrång. Effektiviseras undersökningen med interaktiva verktyg och kontextbaserad hotinformation.
- **Åtkomst- och programkontroll** – blockera skadliga och andra oönskade program genom att tillämpa rekommendationer anpassas efter dina specifika arbetsbelastningar och drivs av maskininlärning. Minska nätverkskontakter med just-in-time och kontrollerad åtkomst till hanteringsportar på virtuella Azure-datorer drastiskt minska exponeringen för råstyrkeattacker och andra nätverksattacker.


## <a name="next-steps"></a>Nästa steg
I den här artikeln berättade priser för Security Center. Mer information om förbättrad säkerhet och Avancerat skydd på Standard-nivån finns:

- [Avancerad hotidentifiering](security-center-threat-report.md)
- [Just-in-time-åtkomstkontroll för virtuell dator](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
