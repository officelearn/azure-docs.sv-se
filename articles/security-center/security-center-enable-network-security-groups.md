---
title: Aktivera nätverks säkerhets grupper i Azure Security Center | Microsoft Docs
description: I det här dokumentet beskrivs hur du implementerar Azure Security Center rekommendation **Aktivera nätverks säkerhets grupper**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "60911470"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Aktivera nätverks säkerhets grupper i Azure Security Center
Azure Security Center rekommenderar att du aktiverar en nätverks säkerhets grupp (NSG) om den inte redan är aktive rad. NSG: er innehåller en lista med regler för Access Control lista (ACL) som tillåter eller nekar nätverks trafik till dina VM-instanser i en Virtual Network. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet. Dessutom kan trafik till en enskild virtuell dator begränsas ytterligare genom att associera en NSG direkt till den virtuella datorn. Mer information finns i [Vad är en nätverks säkerhets grupp (NSG)?](../virtual-network/security-overview.md)

Om du inte har aktiverat NSG: er, visar Security Center två rekommendationer till dig: Aktivera nätverks säkerhets grupper på undernät och aktivera nätverks säkerhets grupper på virtuella datorer. Du kan välja vilken nivå, undernät eller virtuell dator som ska användas för NSG: er.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. På bladet **rekommendationer** väljer du **Aktivera nätverks säkerhets grupper** på undernät eller virtuella datorer.
   ![Aktivera nätverkssäkerhetsgrupper][1]
2. Detta öppnar bladet **Konfigurera nätverks säkerhets grupper som saknas** för undernät eller virtuella datorer, beroende på vilken rekommendation du har valt. Välj ett undernät eller en virtuell dator för att konfigurera en NSG på.

   ![Konfigurera NSG för undernät][2]

   ![Konfigurera NSG för virtuell dator][3]
3. På bladet **Välj Nätverks säkerhets grupp** väljer du en befintlig NSG eller väljer **Skapa ny** för att skapa en NSG.

   ![Välj Nätverks säkerhets grupp][4]

Om du skapar en NSG följer du stegen i [hantera en nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md) för att skapa en NSG och ange säkerhets regler.

## <a name="see-also"></a>Se också
I den här artikeln visar vi hur du implementerar Security Center rekommendation "Aktivera nätverks säkerhets grupper" för undernät eller virtuella datorer. Mer information om hur du aktiverar NSG: er finns i följande avsnitt:

* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../virtual-network/security-overview.md)
* [Hantera en nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hämta de senaste nyheterna och informationen om Azure-säkerhet.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
