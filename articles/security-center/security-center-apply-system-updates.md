---
title: Använda system uppdateringar i Azure Security Center | Microsoft Docs
description: I det här dokumentet beskrivs hur du implementerar Azure Security Center rekommendationer **använder system uppdateringar** och **omstart efter system uppdateringar**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: v-mohabe
ms.openlocfilehash: 7f9c5b702c0da8de4476508114be911a9d74f78a
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531460"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Tillämpa system uppdateringar i Azure Security Center
Azure Security Center övervakar dagliga virtuella Windows-och Linux-datorer (VM) och datorer för saknade uppdateringar av operativ systemet. Security Center hämtar en lista över tillgängliga säkerhets uppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services (WSUS), beroende på vilken tjänst som har kon figurer ATS på en Windows-dator. Security Center också att söka efter de senaste uppdateringarna i Linux-system. Om din virtuella dator eller dator saknar en system uppdatering rekommenderar Security Center att du installerar System uppdateringar.

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
Tillämpa system uppdateringar visas som en rekommendation i Security Center. Om din virtuella dator eller dator saknar en system uppdatering visas den här rekommendationen under **rekommendationer** och under **beräkning**.  Om du väljer rekommendation öppnas instrument panelen **tillämpa system uppdateringar** .

I det här exemplet ska vi använda **Compute**.

1. Välj **beräkning** under Security Center huvud menyn.

   ![Välj beräkning][1]

2. Under **beräkning**väljer du **System uppdateringar som saknas**. Instrument panelen **tillämpa system uppdateringar** öppnas.

   ![Använd instrument panelen system uppdateringar][2]

   Instrument panelens överkant innehåller:

    - Det totala antalet virtuella Windows-och Linux-datorer och datorer som saknar system uppdateringar.
    - Det totala antalet kritiska uppdateringar som saknas på dina virtuella datorer och datorer.
    - Det totala antalet säkerhets uppdateringar som saknas på dina virtuella datorer och datorer.

   Längst ned på instrument panelen visas alla uppdateringar som saknas på de virtuella datorerna och datorerna och den saknade uppdateringens allvarlighets grad.  Listan innehåller:

    - NAMN: Namnet på den saknade uppdateringen.
    - NEJ. Virtuella datorer & datorer: Totalt antal virtuella datorer och datorer som saknar uppdateringen.
    - LÅST Rekommendationens aktuella tillstånd:

      - Inställningar Rekommendationen har ännu inte åtgärd ATS.
      - Pågår: Rekommendationen används för närvarande på dessa resurser och ingen åtgärd krävs av dig.
      - Göras Rekommendationen har redan slutförts. (När problemet har lösts, inaktiveras posten).

    - ALLVARLIGHETS GRAD Beskriver allvarlighets graden för den specifika rekommendationen:

      - Högt Det finns ett säkerhets problem med en meningsfull resurs (program, virtuell dator eller nätverks säkerhets grupp) och kräver åtgärd.
      - Säker Icke-kritiska eller ytterligare steg krävs för att slutföra en process eller eliminera en sårbarhet.
      - Börjar Ett säkerhets problem bör åtgärdas men kräver ingen omedelbar åtgärd. (Rekommendationer med låg allvarlighetsgrad visas normalt inte, men du kan filtrera fram dem om du vill se dem.)

3. Välj en uppdatering som saknas i listan för att visa information.

   ![Säkerhets uppdatering saknas][3]

4. Välj **Sök** ikonen i det översta menyfliksområdet.  En Azure Monitor loggar Sök frågan öppnar filtrerade till de datorer där uppdateringen saknas.

   ![Sök i Azure Monitor loggar][4]

5. Välj en dator i listan om du vill ha mer information. Ett annat Sök Resultat öppnas med information som filtrerats enbart för den datorn.

    ![Sök i Azure Monitor loggar][5]

## <a name="next-steps"></a>Nästa steg
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
