---
title: Hantera problem med slut punkts skydd med Azure Security Center | Microsoft Docs
description: Lär dig hur du hanterar problem med slut punkts skydd i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 58a220192d6a02c6ad66094e8421405578a82a01
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231316"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Hantera problem med slut punkts skydd med Azure Security Center
Azure Security Center övervakar status för skydd mot skadlig kod och rapporterar detta under bladet problem med slut punkts skydd. Security Center fokuserar på problem, till exempel identifierade hot och otillräckligt skydd, vilket kan göra att dina virtuella datorer (VM) och datorer är utsatta för hot mot skadlig kod. Genom att använda informationen under **problem med slut punkts skydd**kan du identifiera en plan för att åtgärda eventuella problem som identifieras.

Security Center rapporterar följande problem med slut punkts skydd:

- Endpoint Protection installeras inte på virtuella Azure-datorer – en lösning för program mot skadlig kod som stöds är inte installerad på dessa virtuella Azure-datorer.
- Endpoint Protection är inte installerat på icke-Azure-datorer – ett program mot skadlig kod som stöds är inte installerat på dessa datorer som inte är Azure-datorer.
- Endpoint Protection-hälsa:

  - Inaktuell signatur – en lösning för program mot skadlig kod installeras på de virtuella datorerna och datorerna, men lösningen har inte de senaste signaturerna för program mot skadlig kod.
  - Inget real tids skydd – en lösning för program mot skadlig kod installeras på de virtuella datorerna och datorerna, men har inte kon figurer ATS för real tids skydd.   Tjänsten kan vara inaktive rad eller Security Center kanske inte kan hämta status eftersom lösningen inte stöds. Se [partner integration](security-center-services.md#endpoint-supported) för en lista över lösningar som stöds.
  - Ej rapportering – en lösning för program mot skadlig kod installeras men rapporterar inte data.
  - Okänd – en lösning för program mot skadlig kod har installerats men dess status är okänd eller rapporterar ett okänt fel.

    > [!NOTE]
    > Se [integrera](security-center-services.md#endpoint-supported) säkerhetslösningar för en lista över säkerhets lösningar för Endpoint Protection som är integrerade med Security Center.
    >
    >

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
Problem med slut punkts skydd presenteras som en rekommendation i Security Center.  Om din miljö är utsatt för hot mot skadlig kod visas den här rekommendationen under **rekommendationer** och under **beräkning**. Om du vill se **instrument panelen för slut punkts skydds problem**måste du följa beräknings arbets flödet.

I det här exemplet ska vi använda **Compute**.  Vi ska titta på hur du installerar program mot skadlig kod på virtuella Azure-datorer och på datorer som inte är Azure-datorer.

## <a name="install-antimalware-on-azure-vms"></a>Installera program mot skadlig kod på virtuella Azure-datorer

1. Välj **compute &-appar** under Security Center huvud meny eller **Översikt**.

   ![Välj beräkning][1]

2. Under **Compute**väljer du **problem med slut punkts skydd**. Instrument panelen för **slut punkts skydds problem** öppnas.

   ![Välj problem med slut punkts skydd][2]

   Instrument panelens överkant innehåller:

   - Installerade Endpoint Protection-providers – listar de olika providers som identifieras av Security Center.
   - Installerat hälso tillstånd för slut punkts skydd – visar hälso tillståndet för virtuella datorer och datorer där en Endpoint Protection-lösning är installerad. Diagrammet visar antalet virtuella datorer och datorer som är felfria och antalet med otillräckligt skydd.
   - Identifierad skadlig kod – visar antalet virtuella datorer och datorer där Security Center rapporterar identifierad skadlig kod.
   - Angrepp datorer – visar antalet virtuella datorer och datorer där Security Center rapporterar attacker av skadlig kod.

   Längst ned på instrument panelen finns en lista med problem med slut punkts skydd som innehåller följande information:  

   - **Totalt** – antalet virtuella datorer och datorer som påverkas av problemet.
   - Ett fält som aggregerar antalet virtuella datorer och datorer som påverkas av problemet. Färgerna i fältet identifierar prioritet:

      - Röd – hög prioritet och bör åtgärdas omedelbart
      - Orange – medelhög prioritet och bör åtgärdas så snart som möjligt

3. Välj **Endpoint Protection inte installerat på virtuella Azure-datorer**.

   ![Välj Endpoint Protection inte installerat på virtuella Azure-datorer][3]

4. Under **Endpoint Protection som inte är installerat på virtuella Azure-datorer** är en lista över virtuella Azure-datorer som inte har installerat program vara.  Du kan välja att installera program mot skadlig kod på alla virtuella datorer i listan eller välja enskilda virtuella datorer som du vill installera program mot skadlig kod på genom att klicka på den specifika virtuella datorn.
5. Under **Välj Endpoint Protection**väljer du den slut punkts skydds lösning som du vill använda. I det här exemplet väljer du **Microsoft Antimalware**.
6. Ytterligare information om slut punkts skydds lösningen visas. Välj **Skapa**.

## <a name="install-antimalware-on-non-azure-computers"></a>Installera program mot skadlig kod på datorer som inte är Azure-datorer

1. Gå tillbaka till **problem med slut punkts skydd** och välj **Endpoint Protection inte installerat på datorer som inte är Azure-datorer**.

   ![Välj Endpoint Protection inte installerat på datorer som inte är Azure-datorer][4]

2. Under **Endpoint Protection inte installerat på icke-Azure-datorer**väljer du en arbets yta. En Azure Monitor loggar Sök frågan som filtrerats till arbets ytan öppnas och visar en lista över datorer som saknar skadlig kod. Välj en dator i listan om du vill ha mer information.

   ![Sök i Azure Monitor loggar][5]

Ett annat Sök Resultat öppnas med information som filtrerats enbart för den datorn.

  ![Sök i Azure Monitor loggar][6]

> [!NOTE]
> Vi rekommenderar att Endpoint Protection tillhandahålls för alla virtuella datorer och datorer för att hjälpa till att identifiera och ta bort virus, spionprogram och annan skadlig program vara.
>
>

## <a name="next-steps"></a>Nästa steg
I den här artikeln visar vi hur du implementerar Security Center rekommendation "Installera Endpoint Protection". Mer information om hur du aktiverar Microsoft Antimalware i Azure finns i följande dokument:

* [Microsoft Antimalware för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md) --lär dig hur du distribuerar Microsoft Antimalware.

Mer information om Security Center finns i följande dokument:

* [Ange säkerhets principer i Azure Security Center](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
