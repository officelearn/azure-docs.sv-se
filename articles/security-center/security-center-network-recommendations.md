---
title: "Skydda ditt nätverk i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet behandlar rekommendationerna i Azure Security Center som hjälper dig skydda dina Azure-nätverk och uppfyller säkerhetsprinciper."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="protecting-your-network-in-azure-security-center"></a>Skydda ditt nätverk i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsrisker, skapar rekommendationer som hjälper dig att konfigurera nödvändiga kontroller.  Rekommendationer gäller för Azure resurstyper: virtuella datorer (VM), nätverk, SQL och program.

Den här artikeln tar rekommendationer som gäller för ditt nätverk.  Rekommendationer Nätverkscenter runt nästa generation brandväggar, Nätverkssäkerhetsgrupper, konfigurera regler för inkommande trafik och mer.  Använd tabellen nedan som referens för att förstå rekommendationerna som tillgängliga nätverk och det var och en gör om du använder den.

## <a name="available-network-recommendations"></a>Tillgängliga nätverksrekommendationer
| Rekommendation | Beskrivning |
| --- | --- |
| [Lägga till en nästa generations brandvägg](security-center-add-next-generation-firewall.md) |Rekommenderar att du lägger till en nästa generations Brandvägg från en Microsoft-partner att öka din säkerhetsskydd. |
| [Dirigera trafiken endast via NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Rekommenderar att du konfigurerar (NSG) regler för nätverkssäkerhetsgrupper som tvingar inkommande trafik till den virtuella datorn via din nästa generations Brandvägg. |
| [Aktivera Nätverkssäkerhetsgrupper för undernät eller virtuella datorer](security-center-enable-network-security-groups.md) |Rekommenderar att du aktiverar NSG: er på undernät eller virtuella datorer. |
| [Begränsa åtkomst via Internetuppkopplad slutpunkt](security-center-restrict-access-through-internet-facing-endpoints.md) |Rekommenderar att du konfigurerar regler för inkommande trafik för NSG: er. |

## <a name="see-also"></a>Se även
Mer information om rekommendationer som gäller för andra typer av Azure finns i:

* [Skydda dina virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda dina program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda din SQL Azure-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
