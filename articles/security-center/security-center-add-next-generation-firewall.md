---
title: Lägg till nästa generations brand vägg i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du implementerar Azure Security Center rekommendationerna **Lägg till nästa generations brand vägg** och **dirigera trafik via NGFW**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "60707082"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Lägg till nästa generations brand vägg i Azure Security Center
Azure Security Center kan rekommendera att du lägger till en nästa generations brand vägg (NGFW) från en Microsoft-partner för att öka säkerhets skyddet. Det här dokumentet vägleder dig genom ett exempel på hur du gör detta.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. På bladet **rekommendationer** väljer du **Lägg till nästa generations brand vägg**.
   ![Lägga till en nästa generations brandvägg][1]
2. Välj en slut punkt i bladet **Lägg till en nästa generations brand vägg** .
   ![Välj en slut punkt][2]
3. Ett andra-blad för att **lägga till nästa generations brand vägg** öppnas. Du kan välja att använda en befintlig lösning om den är tillgänglig eller så kan du skapa en ny. I det här exemplet finns det inga befintliga lösningar som är tillgängliga så vi skapar en NGFW.
   ![Skapa nästa generations brand vägg][3]
4. Om du vill skapa en NGFW väljer du en lösning i listan över integrerade partner. I det här exemplet väljer vi **Check Point**.
   ![Välj nästa generations brand Väggs lösning][4]
5. Bladet **Check punkt** öppnas och ger dig information om partner lösningen. Välj **skapa** på bladet information.
   ![Informations blad för brand vägg][5]
6. Bladet **Skapa virtuell dator** öppnas. Här kan du ange information som krävs för att skapa en virtuell dator (VM) som kör NGFW. Följ anvisningarna och ange NGFW-informationen som krävs. Välj OK för att tillämpa.
   ![Skapa en virtuell dator för att köra NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Vidarebefordra trafik enbart via NGFW
Gå tillbaka till bladet **rekommendationer** . En ny post genererades efter att du har lagt till en NGFW via Security Center, som **endast kallas väg trafik via NGFW**. Den här rekommendationen skapas endast om du har installerat din NGFW via Security Center. Om du har slut punkter mot Internet rekommenderar Security Center att du konfigurerar regler för nätverks säkerhets grupper som tvingar inkommande trafik till den virtuella datorn via din NGFW.

1. På **bladet rekommendationer**väljer du **dirigera trafik endast via NGFW**.
   ![Dirigera trafiken endast via NGFW][7]
2. Detta öppnar bladet **dirigera trafik endast via NGFW**, som visar en lista över virtuella datorer som du kan dirigera trafik till. Välj en virtuell dator i listan.
   ![Välj en virtuell dator][8]
3. Ett blad för den valda virtuella datorn öppnas och visar relaterade regler för inkommande trafik. En beskrivning ger mer information om möjliga nästa steg. Välj **Redigera regler för inkommande** trafik om du vill fortsätta redigera en regel för inkommande trafik. Förväntat är att **källan** inte är inställd på **någon** för de Internet-riktade slut punkter som är länkade till NGFW. Mer information om egenskaperna för den inkommande regeln finns i [säkerhets regler](../virtual-network/security-overview.md#security-rules).
   ![Konfigurera regler för att begränsa][9]
   åtkomst![redigera inkommande regel][10]

## <a name="see-also"></a>Se också
Det här dokumentet visar hur du implementerar Security Center rekommendationen "Lägg till nästa generations brand vägg". Om du vill veta mer om NGFWs och Check Point partner-lösningen, se följande:

* [Nästa generations brand vägg](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point-vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhets principer i Azure Security Center](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
