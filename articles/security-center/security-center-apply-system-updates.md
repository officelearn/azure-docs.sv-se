---
title: Tillämpa uppdateringar i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskrivs hur du implementerar Azure Security Center-rekommendationerna ** gäller system uppdateringar ** och ** startas om efter system uppdateringar **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866431"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Tillämpa uppdateringar i Azure Security Center
Azure Security Center övervakar dagliga Windows och Linux virtuella datorer (VM) och datorer efter saknade uppdateringar av operativsystemet. Security Center hämtar en lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services (WSUS), beroende på vilken tjänsten har konfigurerats på en Windows-dator. Säkerhetscenter kontrollerar också efter de senaste uppdateringarna i Linux-system. Om din dator eller VM saknas för en uppdatering av system, kommer Security Center rekommenderar att du installerar uppdateringar.

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
Tillämpa system uppdateringar visas som en rekommendation i Security Center. Om din dator eller VM saknas för en uppdatering av system, visas den här rekommendationen **rekommendationer** och under **Compute**.  Markerar rekommendationen öppnas den **gäller systemuppdateringar** instrumentpanelen.

I det här exemplet ska vi använda **Compute**.

1. Välj **Compute** under huvudmenyn Security Center.

   ![Välj beräkning][1]

2. Under **Compute**väljer **saknas systemuppdateringar**. Den **gäller systemuppdateringar** instrumentpanelen öppnas.

   ![Tillämpa uppdateringar instrumentpanel för systemet][2]

   Överst i instrumentpanelen innehåller:

    - Det totala antalet Windows och Linux virtuella datorer och datorer som saknar uppdateringar.
    - Det totala antalet viktiga uppdateringar som saknas i dina virtuella datorer och datorer.
    - Det totala antalet säkerhetsuppdateringar som saknas i dina virtuella datorer och datorer.

  Längst ned på instrumentpanelen visar en lista över alla saknade uppdateringar för dina virtuella datorer och datorer och hur allvarligt klassas.  Listan innehåller:

    - NAME: Namnet på saknad uppdatering.
    - NEJ. FÖR virtuella datorer och datorer: Totalt antal virtuella datorer och datorer som saknar den här uppdateringen.
    - TILLSTÅND: Det aktuella tillståndet för rekommendationen:

      - Öppna: Rekommendationen har inte utförts än.
      - Pågår: Rekommendationen är för tillfället används resurserna och ingen åtgärd krävs av dig.
      - Matcha: Rekommendationen har redan slutförts. (När problemet har lösts, inaktiveras posten).

    - ALLVARLIGHETSGRAD: Här visas hur viktig rekommendationen:

      - Hög: Ett säkerhetsproblem finns i en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
      - Medel: Icke-kritiska eller ytterligare steg krävs för att slutföra en process eller åtgärda en säkerhetsrisk.
      - Låg: En säkerhetsrisk bör åtgärdas men kräver inte omedelbara uppmärksamhet. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)

3. Välj en saknad uppdatering i listan för att visa information.

   ![Saknad uppdatering][3]

4. Välj den **Sök** ikon på menyfliken översta.  En sökfråga logganalys öppnas filtrerade till datorer som saknar uppdateringen.

   ![Logganalys Sök][4]

5. Välj en dator i listan för mer information. En annan sökresultatet öppnas med information som filtrerad endast för den datorn.

    ![Logganalys Sök][5]

## <a name="reboot-after-system-updates"></a>Starta om datorn efter uppdateringarna
1. Gå tillbaka till den **rekommendationer** bladet. En ny post skapades när du använt systemuppdateringar kallas **startas om efter systemuppdateringar**. Den här posten kan du vet att du måste starta om den virtuella datorn för att slutföra processen att tillämpa uppdateringar.

   ![Starta om datorn efter uppdateringarna][6]
2. Välj **startas om efter systemuppdateringar**. Då öppnas **väntar en omstart för att slutföra systemuppdateringar** bladet visas en lista över virtuella datorer som du måste starta om för att slutföra systemets tillämpa uppdateringar processen.

   ![Väntande omstart][7]

Starta om den virtuella datorn från Azure för att slutföra processen.

## <a name="next-steps"></a>Nästa steg
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om säkerhet och Azure kompatibilitet.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
