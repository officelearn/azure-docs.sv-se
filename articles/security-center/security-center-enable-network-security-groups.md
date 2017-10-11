---
title: "Aktivera Nätverkssäkerhetsgrupper i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet beskrivs hur du implementerar rekommenderar Azure Security Center ** aktivera Network Security grupper **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 1e034d59d8847f237fa0d4c772344d45cd618576
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Aktivera Nätverkssäkerhetsgrupper i Azure Security Center
Azure Security Center rekommenderar att du aktiverar en nätverkssäkerhetsgrupp (NSG) om en inte redan är aktiverad. NSG: er innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till VM-instanser i ett virtuellt nätverk. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet. Dessutom kan trafik till en enskild VM begränsas ytterligare genom att koppla en NSG direkt till den virtuella datorn. Läs mer finns [vad är en Nätverkssäkerhetsgrupp (NSG)?](../virtual-network/virtual-networks-nsg.md)

Om du inte har NSG: er aktiverat två rekommendationer för dig visas i Security Center: aktivera Nätverkssäkerhetsgrupper i undernät och aktivera Nätverkssäkerhetsgrupper på virtuella datorer. Du kan välja vilka nivå, undernät eller virtuella datorn, för att tillämpa NSG: er.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **aktivera Nätverkssäkerhetsgrupper** på undernät eller virtuella datorer.
   ![Aktivera nätverkssäkerhetsgrupper][1]
2. Gör det öppnas bladet **konfigurera saknade Nätverkssäkerhetsgrupper** för undernät eller virtuella datorer, beroende på rekommendationen som du har valt. Välj ett undernät eller en virtuell dator för att konfigurera en NSG på.

   ![Konfigurera NSG för undernätet][2]

   ![Konfigurera NSG för VM][3]
3. På den **Välj nätverkssäkerhetsgrupp** bladet Välj en befintlig NSG eller **Skapa nytt** att skapa en NSG.

   ![Välj Nätverkssäkerhetsgrupp][4]

Om du skapar en NSG, följer du stegen i [hantera NSG: er med hjälp av Azure portal](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) att skapa en NSG och säkerhetsregler.

## <a name="see-also"></a>Se även
Den här artikeln visades hur du implementerar Security Center-rekommendationen ”aktivera Nätverkssäkerhetsgrupper” för undernät eller virtuella datorer. Mer information om hur du aktiverar NSG: er finns i följande avsnitt:

* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../virtual-network/virtual-networks-nsg.md)
* [Hantera NSG: er med hjälp av Azure portal](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --Azure security nyheter och information.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
