---
title: Lägg till nästa generations brandvägg i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskrivs hur du implementerar Azure Security Center-rekommendationerna **lägger du till nästa generations brandvägg** och **väg traffice via nästa generations Brandvägg endast**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 25601b01489b95de0e86b314b4b934d3cffd38c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330748"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Lägg till nästa generations brandvägg i Azure Security Center
Azure Security Center kan rekommenderar att du lägger till nästa generations brandvägg (nästa generations Brandvägg) från en Microsoft-partner att öka din säkerhetsskydd. Det här dokumentet vägleder dig genom ett exempel på hur du gör detta.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **lägger du till nästa generations brandvägg**.
   ![Lägga till en nästa generations brandvägg][1]
2. I den **lägger du till nästa generations brandvägg** bladet väljer du en slutpunkt.
   ![Välj en slutpunkt][2]
3. En andra **lägger du till nästa generations brandvägg** blad öppnas. Du kan välja att använda en befintlig lösning om det är tillgängligt eller du kan skapa en ny. I det här exemplet finns det inga befintliga lösningar så skapar vi ett nästa generations Brandvägg.
   ![Skapa nästa generations brandvägg][3]
4. Välj en lösning för att skapa en nästa generations Brandvägg, i listan med integrerade partnerleverantörer. I det här exemplet väljer vi **Check Point**.
   ![Välj nästa generations brandvägg lösning][4]
5. Den **Check Point** öppnas ett blad med information om Partnerlösningen. Välj **skapa** i bladet information.
   ![Brandväggen information bladet][5]
6. Den **Skapa virtuell dator** blad öppnas. Här kan du ange informationen som krävs för att få igång en virtuell dator (VM) som körs i nästa generations Brandvägg. Följ anvisningarna och ange nästa generations Brandvägg information som krävs. Välj OK om du vill använda.
   ![Skapa virtuell dator för att köra nästa generations Brandvägg][6]

## <a name="route-traffic-through-ngfw-only"></a>Dirigera trafiken endast via NGFW
Gå tillbaka till den **rekommendationer** bladet. En ny post skapades när du har lagt till en nästa generations Brandvägg via Security Center, som kallas **dirigera trafik via nästa generations Brandvägg endast**. Denna rekommendation skapas bara om du har installerat din nästa generations Brandvägg via Security Center. Om du har mot Internet slutpunkter rekommenderar Security Center att du konfigurerar Network Security Group regler som tvingar inkommande trafik till den virtuella datorn via din nästa generations Brandvägg.

1. I den **rekommendationer bladet**väljer **dirigera trafik via nästa generations Brandvägg endast**.
   ![Dirigera trafiken endast via NGFW][7]
2. Gör det öppnas bladet **dirigera trafik via nästa generations Brandvägg endast**, som visar en lista över virtuella datorer som du kan vidarebefordra trafiken till. Välj en virtuell dator i listan.
   ![Välj en virtuell dator][8]
3. Öppnas ett blad för den valda virtuella datorn, med relaterade regler för inkommande trafik. En beskrivning får du mer information om möjliga nästa steg. Välj **redigera regler för inkommande trafik** att fortsätta med att redigera en inkommande regel. Förväntningen är att **källa** inte har angetts till **alla** för mot Internet-slutpunkter som är kopplad till nästa generations Brandvägg. Mer information om egenskaperna för den inkommande regeln finns [säkerhetsregler](../virtual-network/security-overview.md#security-rules).
   ![Konfigurera regler för att begränsa åtkomst][9]
   ![Redigera inkommande regel][10]

## <a name="see-also"></a>Se också
Det här dokumentet visar dig hur du implementerar Security Center-rekommendationen ”Lägg till nästa generations brandvägg”. Mer information om NGFWs och Partnerlösningen Check Point finns i:

* [Nästa generations brandvägg](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om säkerhet och Azure kompatibilitet.

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
