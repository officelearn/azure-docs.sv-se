---
title: Lägg till en nästa generations brandvägg i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationer **lägga till en nästa generations brandvägg** och **väg traffice via NGFW endast**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 0f1949373e99df1cccd6c49ff92c80d595519377
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341159"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Lägg till en nästa generations brandvägg i Azure Security Center
Azure Security Center rekommendera att du lägger till en nästa generations brandvägg (NGFW) från en Microsoft-partner att öka din säkerhetsskydd. Det här dokumentet vägleder dig genom ett exempel på hur du gör detta.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **lägga till en nästa generations brandvägg**.
   ![Lägga till en nästa generations brandvägg][1]
2. I den **lägga till en nästa generations brandvägg** bladet väljer du en slutpunkt.
   ![Välj en slutpunkt][2]
3. En andra **lägga till en nästa generations brandvägg** blad öppnas. Du kan välja att använda en befintlig lösning om det är tillgängligt eller du kan skapa en ny. I det här exemplet finns det inga befintliga lösningar så skapar vi en NGFW.
   ![Skapa nästa generations brandvägg][3]
4. Välj en lösning för att skapa en NGFW, i listan med integrerade partnerleverantörer. I det här exemplet väljer vi **Check Point**.
   ![Välj nästa generations brandväggslösningen][4]
5. Den **Check Point** öppnas bladet ger dig information om Partnerlösningen. Välj **skapa** på bladet information.
   ![Brandväggen information bladet][5]
6. Den **Skapa virtuell dator** blad öppnas. Här kan du ange information som krävs för att skapa en virtuell dator (VM) som kör NGFW. Följ stegen och ange NGFW informationen som krävs. Välj OK om du vill använda.
   ![Skapa virtuell dator för att köra NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Dirigera trafiken endast via NGFW
Gå tillbaka till den **rekommendationer** bladet. En ny post har skapats efter att du har lagt till en NGFW via Security Center, som kallas **dirigera trafiken endast via NGFW**. Den här rekommendationen skapas endast om du har installerat din NGFW via Security Center. Om du har Internet-riktade slutpunkter kan rekommenderar Security Center att du konfigurerar regler för Nätverkssäkerhetsgruppen som tvingar inkommande trafik till den virtuella datorn via din NGFW.

1. I den **rekommendationsbladet**väljer **dirigera trafiken endast via NGFW**.
   ![Dirigera trafiken endast via NGFW][7]
2. Då öppnas bladet **dirigera trafiken endast via NGFW**, som visar en lista över virtuella datorer som du kan dirigera trafik till. Välj en virtuell dator i listan.
   ![Välj en virtuell dator][8]
3. Ett blad för den valda virtuella datorn öppnas och visar relaterade regler för inkommande trafik. En beskrivning får du mer information om möjliga nästa steg. Välj **redigera regler för inkommande trafik** att fortsätta med att redigera en inkommande regel. Väntas som **källa** inte är inställt på **alla** för Internet-riktade slutpunkter som är kopplad till NGFW. Läs mer om egenskaperna för den inkommande regeln i [säkerhetsregler](../virtual-network/security-overview.md#security-rules).
   ![Konfigurera regler för att begränsa åtkomsten][9]
   ![Redigera regel för inkommande trafik][10]

## <a name="see-also"></a>Se också
Det här dokumentet visade hur du implementerar Security Center-rekommendationen ”Lägg till en nästa generations brandvägg”. Mer information om NGFWs och Partnerlösningen Check Point finns följande:

* [Nästa generations brandvägg](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om Azure-säkerhet och regelefterlevnad.

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
