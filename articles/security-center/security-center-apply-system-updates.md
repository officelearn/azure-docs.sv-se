---
title: Installera systemuppdateringar i Azure Security Center | Microsoft-dokument
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationerna **Tillämpa systemuppdateringar** och **Starta om efter systemuppdateringar**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604550"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Installera systemuppdateringar i Azure Security Center
Azure Security Center övervakar dagligen virtuella Windows- och Linux-datorer (VM) och datorer med avseende på saknade uppdateringar av operativsystemet. Security Center hämtar en lista med tillgängliga säkerhetsuppdateringar och andra viktiga uppdateringar från Windows Update eller Windows Server Update Services (WSUS), beroende på vilken tjänst som är konfigurerad på en Windows-dator. Security Center söker även efter de senaste uppdateringarna i Linux-system. Om din virtuella dator eller dator saknar en systemuppdatering rekommenderar Security Center att du tillämpar systemuppdateringar.

## <a name="implement-the-recommendation"></a>Genomföra rekommendationen
Tillämpa systemuppdateringar presenteras som en rekommendation i Security Center. Om den virtuella datorn eller datorn saknar en systemuppdatering visas den här rekommendationen under **Rekommendationer** och under **Beräkna**.  Om du väljer rekommendationen öppnas instrumentpanelen **Använd systemuppdateringar.**

I det här exemplet använder vi **Compute**.

1. Välj **Beräkna** under huvudmenyn för Säkerhetscenter.

   ![Välj beräkning][1]

2. Under **Beräkna**väljer du **Saknade systemuppdateringar**. Instrumentpanelen **Använd systemuppdateringar** öppnas.

   ![Använda instrumentpanelen för systemuppdateringar][2]

   Den övre sidan av instrumentpanelen innehåller:

    - Det totala antalet virtuella datorer och datorer i Windows och Linux som saknas systemuppdateringar.
    - Det totala antalet viktiga uppdateringar som saknas i dina virtuella datorer och datorer.
    - Det totala antalet säkerhetsuppdateringar som saknas i dina virtuella datorer och datorer.

   Längst ned på instrumentpanelen visas alla uppdateringar som saknas på dina virtuella datorer och datorer och hur allvarlig den saknade uppdateringen är.  Listan innehåller:

    - NAMN: Namnet på den uppdatering som saknas.
    - Nej. ANTAL virtuella datorer & datorer: Totalt antal virtuella datorer och datorer som saknar den här uppdateringen.
    - STATE (STATUS): Här visas det aktuella tillståndet för rekommendationen:

      - Öppen: Rekommendationen har inte tagits upp ännu.
      - Pågående: Rekommendationen håller på att utföras och ingen åtgärd behövs från din sida.
      - Löst: Rekommendation har redan slutförts. (När problemet har lösts, inaktiveras posten).

    - SEVERITY (ALLVARLIGHETSGRAD): Här visas hur viktig rekommendationen är.

      - Hög: Det finns en säkerhetsrisk i en viktig resurs (program, virtuell dator eller nätverkssäkerhetsgrupp) som måste åtgärdas.
      - Medelhög: Det behövs mindre viktiga eller ytterligare åtgärder för att slutföra en process eller åtgärda en säkerhetsrisk.
      - Low (Låg): Det finns en säkerhetsrisk som bör åtgärdas, men det måste inte göras omedelbart. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)

3. Välj en uppdatering som saknas i listan om du vill visa information.

   ![Säkerhetsuppdatering saknas][3]

4. Välj **sökikonen** i det övre menyfliksområdet.  En sökfråga för Azure Monitor loggar upp filtreras till de datorer som saknar uppdateringen.

   ![Sökning i Azure Monitor-loggar][4]

5. Välj en dator i listan för mer information. Ett annat sökresultat öppnas med information filtrerad endast för den datorn.

    ![Sökning i Azure Monitor-loggar][5]

## <a name="next-steps"></a>Nästa steg
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* Övervakning av [säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Läs om hur du hanterar och svarar på säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Azure Security-blogg](https://blogs.msdn.com/b/azuresecurity/) – Hitta blogginlägg om Azure-säkerhet och efterlevnad.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
