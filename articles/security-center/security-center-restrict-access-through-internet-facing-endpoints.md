---
title: Begränsa åtkomst via Internet-riktade slutpunkter i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationen **begränsa åtkomst via slutpunkt mot Internet**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 8e404651fa315f630ff190c9e70ccdd3eec7f117
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230010"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Begränsa åtkomst via Internet-riktade slutpunkter i Azure Security Center
Azure Security Center rekommenderar att du begränsar åtkomst via Internet-riktade slutpunkter om någon av dina Nätverkssäkerhetsgrupper (NSG) har en eller flera inkommande regler som tillåter åtkomst från ”alla” källans IP-adress. Öppna åtkomst till ”alla” kan angripare får tillgång till dina resurser. Security Center rekommenderar att du redigerar dessa regler för inkommande trafik för att begränsa åtkomsten till källans IP-adresser som faktiskt behöver åtkomst.

Den här rekommendationen genereras för alla icke-web-portar som innehåller ”något” som källa.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution. Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationsbladet**väljer **begränsa åtkomst via slutpunkt mot Internet**.

   ![Begränsa åtkomst via slutpunkt mot Internet][1]
2. Då öppnas bladet **begränsa åtkomst via slutpunkt mot Internet**. Det här bladet visar en lista över virtuella datorer (VM) med regler för inkommande trafik som skapar potentiella säkerhetsproblem. Välj en virtuell dator.

   ![Välj en virtuell dator][2]
3. Den **NSG** bladet visar Nätverkssäkerhetsgrupp information, relaterade regler för inkommande trafik och den associerade virtuella datorn. Välj **redigera regler för inkommande trafik** att fortsätta med att redigera en inkommande regel.

   ![Bladet för Nätverkssäkerhetsgrupp][3]
4. På den **ingående säkerhetsregler** bladet Välj den inkommande regeln att redigera. I det här exemplet väljer vi **AllowWeb**.

   ![Ingående säkerhetsregler][4]

   Observera att du kan också välja **standardregler** att se uppsättning standardregler som finns i alla NSG: er. Standardreglerna kan inte tas bort, men eftersom de är tilldelade en lägre prioritet, kan de överskridas av de regler som du skapar. Läs mer om [standardregler](../virtual-network/security-overview.md#default-security-rules).

   ![Standardregler][5]
5. På den **AllowWeb** bladet redigera egenskaperna för den inkommande regeln så att den **källa** är ett IP-adress eller ett block av IP-adresser. Läs mer om egenskaperna för den inkommande regeln i [NSG-regler](../virtual-network/security-overview.md#security-rules).

   ![Redigera regel för inkommande trafik][6]

## <a name="see-also"></a>Se också
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”begränsa åtkomst via Internet-riktade slutpunkten”. Mer information om hur du aktiverar NSG: er och regler finns följande:

* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../virtual-network/security-overview.md)
* [Hantera en grupp](../virtual-network/manage-network-security-group.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Här får du lära dig hur du kan skydda resurserna i Azure med hjälp av rekommendationer.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
