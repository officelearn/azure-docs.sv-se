---
title: Tillämpa systemuppdateringar i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationer **tillämpa systemuppdateringar** och **starta om efter systemuppdateringar**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a0d0cbacfa03080d519e5d9e3592ad01ed58b933
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316591"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Tillämpa systemuppdateringar i Azure Security Center
Azure Security Center övervakar dagliga Windows och Linux-datorer (VM) och datorer efter saknade uppdateringar av operativsystemet. Security Center hämtar en lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services (WSUS), beroende på vilken tjänst som har konfigurerats på en Windows-dator. Security Center söker också efter de senaste uppdateringarna i Linux-system. Om en virtuell dator eller en dator saknar en systemuppdatering, rekommenderar Security Center att du tillämpar systemuppdateringar.

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
Tillämpa system uppdateringar visas som en rekommendation i Security Center. Om en virtuell dator eller en dator saknar en systemuppdatering, den här rekommendationen visas under **rekommendationer** och under **Compute**.  Om du väljer rekommendationen öppnas den **tillämpa systemuppdateringar** instrumentpanelen.

I det här exemplet ska vi använda **Compute**.

1. Välj **Compute** huvudmenyn i Security Center.

   ![Välj Compute][1]

2. Under **Compute**väljer **systemuppdateringar**. Den **tillämpa systemuppdateringar** instrumentpanelen öppnas.

   ![Tillämpa uppdateringar instrumentpanel för systemet][2]

   Överst på instrumentpanelen ger:

    - Det totala antalet Windows och Linux-datorer och datorer som saknar systemuppdateringar.
    - Det totala antalet kritiska uppdateringar som saknas i dina virtuella datorer och datorer.
    - Det totala antalet säkerhetsuppdateringar som saknas i dina virtuella datorer och datorer.

  Längst ned på instrumentpanelen visar en lista över alla saknade uppdateringar för dina virtuella datorer och datorer och allvarlighetsgraden för saknad uppdatering.  Listan innehåller:

    - NAME: Namnet på uppdateringen som saknas.
    - NEJ. VM: ar och datorer: Totalt antal virtuella datorer och datorer som saknar denna uppdatering.
    - TILLSTÅND: Det aktuella tillståndet för rekommendationen:

      - Öppna: Rekommendationen har inte utförts än.
      - Pågår: Rekommendationen håller på att utföras och ingen åtgärd krävs av dig.
      - Löst: Rekommendationen har redan slutförts. (När problemet har lösts, inaktiveras posten).

    - ALLVARLIGHETSGRAD: Visas hur viktig rekommendationen:

      - Hög: En säkerhetsrisk finns en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
      - Medel: Icke-kritiska eller ytterligare åtgärder krävs för att slutföra en process eller åtgärda en säkerhetsrisk.
      - Låg: En säkerhetsrisk bör åtgärdas, men kräver inte omedelbar åtgärd. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)

3. Välj en saknad uppdatering i listan om du vill visa information.

   ![Saknade säkerhetsuppdateringen][3]

4. Välj den **Search** ikonen i menyfliksområdet längst upp.  En sökfråga för Log Analytics öppnas filtrerade till de datorer som saknar uppdateringen.

   ![Log Analytics-sökningen][4]

5. Välj en dator i listan för mer information. En annan sökresultat öppnas med information som filtrerad endast för den datorn.

    ![Log Analytics-sökningen][5]

## <a name="reboot-after-system-updates"></a>Starta om datorn efter uppdateringarna
1. Gå tillbaka till den **rekommendationer** bladet. En ny post genererades när du har tillämpat systemuppdateringar, kallas **starta om efter systemuppdateringar**. Den här posten kan du vet att du behöver starta om den virtuella datorn för att slutföra processen med att tillämpa uppdateringar.

   ![Starta om datorn efter uppdateringarna][6]
2. Välj **starta om efter systemuppdateringar**. Då öppnas **väntar en omstart för att slutföra systemuppdateringar** bladet som visar en lista över virtuella datorer som du måste starta om för att slutföra tillämpa systemet uppdaterar processen.

   ![Väntar på omstart][7]

Starta om den virtuella datorn från Azure för att slutföra processen.

## <a name="next-steps"></a>Nästa steg
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-azure-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om Azure-säkerhet och regelefterlevnad.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
