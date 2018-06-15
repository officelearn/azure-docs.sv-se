---
title: Begränsa åtkomst via Internet-riktade slutpunkter i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskrivs hur du implementerar rekommenderar Azure Security Center **begränsa åtkomst via Internetuppkopplad slutpunkt**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: 92906d31f4db21f37094f192dadd080e28cc6e8e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363061"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Begränsa åtkomst via Internet-riktade slutpunkter i Azure Security Center
Azure Security Center rekommenderar att du begränsar åtkomst via Internet-riktade slutpunkter om någon av dina Nätverkssäkerhetsgrupper (NSG: er) har en eller flera regler för inkommande trafik som tillåter åtkomst från ”alla” källans IP-adress. Öppna åtkomst till ”” kan du aktivera angripare att få åtkomst till dina resurser. Security Center rekommenderar att du redigera dessa regler för inkommande trafik för att begränsa åtkomsten till källans IP-adresser som verkligen behöver åtkomst.

Denna rekommendation genereras för alla icke-web-portar som innehåller ”något” som källa.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution. Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer bladet**väljer **begränsa åtkomst via Internetuppkopplad slutpunkt**.

   ![Begränsa åtkomst via slutpunkt mot Internet][1]
2. Gör det öppnas bladet **begränsa åtkomst via Internetuppkopplad slutpunkt**. Det här bladet visar de virtuella datorerna (VM) med regler för inkommande trafik som skapar en potentiell säkerhetsrisk. Välj en virtuell dator.

   ![Välj en virtuell dator][2]
3. Den **NSG** bladet visar Nätverkssäkerhetsgruppen information, relaterade regler för inkommande trafik och den associera virtuella datorn. Välj **redigera regler för inkommande trafik** att fortsätta med att redigera en inkommande regel.

   ![Nätverkssäkerhetsgruppen bladet][3]
4. På den **inkommande säkerhetsregler** bladet Välj en inkommande regel för att redigera. I det här exemplet ska vi väljer **AllowWeb**.

   ![Inkommande säkerhetsregler][4]

   Observera att du kan också välja **standard regler** att se en uppsättning standardregler som finns i alla NSG: er. Standardreglerna kan inte tas bort, men eftersom de har tilldelats en lägre prioritet, kan de överskridas av de regler som du skapar. Lär dig mer om [standard regler](../virtual-network/security-overview.md#default-security-rules).

   ![Standardregler][5]
5. På den **AllowWeb** bladet redigera egenskaper för regel för inkommande trafik så att den **källa** är ett IP-adress eller ett block av IP-adresser. Mer information om egenskaperna för den inkommande regeln finns [NSG-regler](../virtual-network/security-overview.md#security-rules).

   ![Redigera regel för inkommande trafik][6]

## <a name="see-also"></a>Se också
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”begränsa åtkomst via internetuppkopplad slutpunkt”. Mer information om hur du aktiverar regler och NSG: er finns i följande avsnitt:

* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../virtual-network/security-overview.md)
* [Hantera en nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Här får du lära dig hur du kan skydda resurserna i Azure med hjälp av rekommendationer.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
