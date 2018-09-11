---
title: Skydda ditt nätverk i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationer i Azure Security Center som hjälper dig att skydda din Azure-nätverk och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 12c00d6dfac6c9c2a377a8c142118ff6fd0af751
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302280"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Skydda ditt nätverk i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera kontrollfunktioner som behövs.  Rekommendationer gäller för Azure-resurstyper: virtuella datorer (VM), nätverk, SQL och program.

Den här artikeln belyser rekommendationer som gäller för ditt nätverk.  Nätverkscenter rekommendationer kring nästa generation-brandväggar, Nätverkssäkerhetsgrupper, konfigurera regler för inkommande trafik och mycket mer.  Använd tabellen nedan som referens för att förstå rekommendationerna som tillgängliga nätverk och vad var och en gör om du använder den.

## <a name="available-network-recommendations"></a>Tillgängliga nätverksrekommendationer
| Rekommendation | Beskrivning |
| --- | --- |
| [Lägga till en nästa generations brandvägg](security-center-add-next-generation-firewall.md) |Rekommenderar att du lägger till en nästa Generation Firewall (NGFW) från en Microsoft-partner att öka din säkerhetsskydd. |
| [Dirigera trafiken endast via NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Rekommenderar att du konfigurerar nätverkssäkerhetsgrupper (NSG) Nätverkssäkerhetsregler som tvingar inkommande trafik till den virtuella datorn via din NGFW. |
| [Aktivera Nätverkssäkerhetsgrupper på undernät eller virtuella datorer](security-center-enable-network-security-groups.md) |Rekommenderar att du aktiverar NSG: er på undernät eller virtuella datorer. |
| [Begränsa åtkomst via slutpunkt mot Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Rekommenderar att du konfigurerar regler för inkommande trafik för NSG: er. |

## <a name="see-also"></a>Se också
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Skydda virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
