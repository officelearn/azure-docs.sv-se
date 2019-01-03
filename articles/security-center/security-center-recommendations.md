---
title: Hantera säkerhetsrekommendationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet vägleder dig genom hur rekommendationerna i Azure Security Center hjälpa dig att skydda dina Azure-resurser och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: f8d87137bb405df566a8115bd17dc10af8ffc441
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539409"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Hantera säkerhetsrekommendationer i Azure Security Center
Det här dokumentet vägleder dig genom hur du använder rekommendationer i Azure Security Center för att hjälpa dig att skydda dina Azure-resurser.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>
>

## <a name="what-are-security-recommendations"></a>Vad är säkerhetsrekommendationer?
Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs.

## <a name="implementing-security-recommendations"></a>Implementera säkerhetsrekommendationer
### <a name="set-recommendations"></a>Set-rekommendationer
I [ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md), lär du dig att:

* Konfigurera säkerhetsprinciper.
* Aktivera insamling av data.
* Välj vilka rekommendationer för att se som en del av din säkerhetsprincip.

Aktuella rekommendationer principcentret runt systemuppdateringar, baslinjeregler, program, [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) på undernät och nätverksgränssnitt, SQL database-granskning, SQL database transparent datakryptering, och brandväggar för webbaserade program.  [Ange säkerhetsprinciper](tutorial-security-policy.md) innehåller en beskrivning av varje rekommendation alternativ.

### <a name="monitor-recommendations"></a>Övervakare för rekommendationer
När du har angett en säkerhetsprincip analyseras säkerhetstillståndet för resurserna i Azure i Security Center för upptäckt av eventuella säkerhetsrisker. Den **rekommendationer** panelen **översikt** gör att du vet att det totala antalet rekommendationer identifierades av Security Center.

![Panel för rekommendationer][1]

Om du vill se information om varje rekommendation, Välj den **rekommendationer panelen** under **översikt**. **Rekommendationer** öppnas.

![Filtrera rekommendationer][2]

Du kan filtrera rekommendationer. Om du vill filtrera rekommendationer, Välj **Filter** på den **rekommendationer** bladet. Den **Filter** bladet öppnas och du kan välja allvarlighetsgrad och status du vill se.

Rekommendationerna visas i tabellformat där varje rad motsvarar en viss rekommendation. Kolumner i den här tabellen är:

* **BESKRIVNING AV**: Förklarar rekommendationen och vad som behöver göras för att åtgärda detta.
* **RESURSEN**: Visar en lista över de resurser som den här rekommendationen gäller.
* **TILLSTÅND**: Beskriver det aktuella tillståndet för rekommendationen:
  * **Öppna**: Rekommendationen har inte utförts än.
  * **Pågår**: Rekommendationen håller på att resurserna och ingen åtgärd krävs av dig.
  * **Löst**: Rekommendationen har redan slutförts (i det här fallet raden är nedtonat).
* **ALLVARLIGHETSGRAD**: Visas hur viktig rekommendationen:
  * **Hög**: En sårbarhet i finns en viktig resurs (till exempel ett program, en virtuell dator eller en nätverkssäkerhetsgrupp) som måste åtgärdas.
  * **Medel**: Det finns ett säkerhetsproblem och icke-kritiska eller ytterligare åtgärder krävs att eliminera den eller för att slutföra en process.
  * **Låg**: Det finns en säkerhetsrisk som bör åtgärdas, men inte kräver omedelbar åtgärd. (Låg rekommendationer är inte visas som standard, men du kan filtrera fram om du vill se dem.)

Använd tabellen nedan som referens för att förstå rekommendationerna som är tillgängliga och vad var och en gör om du använder den.

> [!NOTE]
> Du vill förstå den [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
>
>
### <a name="apply-recommendations"></a>Tillämpa rekommendationer
När du har granskat avgör alla rekommendationer vilken du bör använda först. Vi rekommenderar att du använder risknivån eftersom parametern huvudsakliga för att utvärdera vilka rekommendationer ska tillämpas först.



## <a name="next-steps"></a>Nästa steg
I det här dokumentet berättade säkerhetsrekommendationer i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
