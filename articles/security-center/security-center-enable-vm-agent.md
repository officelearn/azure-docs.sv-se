---
title: Aktivera VM-agenten i Azure Security Center | Microsoft Docs
description: "Det här dokumentet beskrivs hur du implementerar rekommenderar Azure Security Center ** aktivera VM Agent **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 337a7adfd93c76882a749685702bea6d1524c96a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-vm-agent-in-azure-security-center"></a>Aktivera VM-agenten i Azure Security Center
VM-agenten måste installeras på virtuella datorer (VM) för att [Aktivera datainsamling](security-center-enable-data-collection.md).  Azure Security Center kan du se vilka virtuella datorer som kräver den Virtuella Datoragenten och rekommenderar att du aktiverar VM-agenten på dessa virtuella datorer.

VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Artikeln [VM Agent and Extensions – Part 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) (VM-agenter och tillägg – del 2) innehåller information om hur VM-agenten ska installeras.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution. Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer bladet**väljer **aktivera VM-agenten**.
   ![Aktivera VM-Agent][1]
2. Gör det öppnas bladet **VM-agenten saknas eller inte svarar**. Det här bladet visar de virtuella datorer som kräver VM-agenten. Följ instruktionerna på bladet för att installera den Virtuella datoragenten.
   ![VM-agenten saknas][2]

## <a name="see-also"></a>Se även
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Här får du lära dig hur du kan skydda resurserna i Azure med hjälp av rekommendationer.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
