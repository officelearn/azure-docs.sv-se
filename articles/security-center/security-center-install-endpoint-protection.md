---
title: Hantera endpoint protection-problem med Azure Security Center | Microsoft Docs
description: "Lär dig mer om att hantera problem med endpoint protection i Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: abbcb0a8e0206d78ca94520dfa81ab92506c47af
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Hantera endpoint protection-problem med Azure Security Center
Azure Security Center övervakar statusen för skydd mot skadlig kod och rapporterar detta under bladet Endpoint protection-problem. Security Center visar problem, till exempel identifierade hot och otillräckligt skydd, vilket kan göra dina virtuella datorer (VM) och datorer sårbar för attacker mot skadlig kod. Med hjälp av informationen under **Endpoint protection-problem**, du kan identifiera en plan för att åtgärda eventuella problem som identifieras.

Security Center rapporterar endpoint protection följande:

- Endpoint protection inte installeras på Azure Virtual Machines – en lösning stöds mot är inte installerat på dessa virtuella Azure-datorer.
- Endpoint protection inte installeras på Azure-datorer – en stöds program mot skadlig kod är inte installerad på dessa Azure-datorer.
- Endpoint protection hälsotillstånd:

   - Inaktuell signatur – en lösning för program mot skadlig kod installeras på dessa virtuella datorer och datorer, men lösningen inte har de senaste signaturerna för program mot skadlig kod.
   - Inget realtidsskydd – en lösning för program mot skadlig kod installeras på dessa virtuella datorer och datorer, men den har inte konfigurerats för realtidsskydd.   Tjänsten kan ha inaktiverats eller Security Center kan inte hämta status eftersom lösningen inte stöds. Se [partner integration](security-center-partner-integration.md) för en lista med lösningar som stöds.
   - Inte rapporterar – en lösning för program mot skadlig kod är installerat men inte rapporterar data.
   - Okänd – en lösning för program mot skadlig kod har installerats men dess status är okänd eller reporting ett okänt fel.

   > [!NOTE]
   > Se [integrera säkerhetslösningar](security-center-partner-integration.md#integrated-azure-security-solutions) en lista över säkerhetslösningar för endpoint protection integreras med Security Center.
   >
   >

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
Endpoint protection-problem presenteras som en rekommendation i Security Center.  Om din miljö är sårbar för attacker mot skadlig kod, visas den här rekommendationen **rekommendationer** och under **Compute**. Se den **Endpoint protection skickar instrumentpanelen**, måste du följa beräknings arbetsflöde.

I det här exemplet ska vi använda **Compute**.  Vi ska titta på hur du installerar program mot skadlig kod på virtuella Azure-datorer och Azure-datorer.

## <a name="install-antimalware-on-azure-vms"></a>Installera program mot skadlig kod på virtuella Azure-datorer

1. Välj **Compute** under Security Center huvudmenyn eller **översikt**.

   ![Välj beräkning][1]

2. Under **Compute**väljer **Endpoint protection-problem**. Den **Endpoint protection-problem** instrumentpanelen öppnas.

   ![Välj Endpoint protection-problem][2]

   Överst i instrumentpanelen innehåller:

   - Installera endpoint protection providers – visar de olika providrar som identifieras av Security Center.
   - Hälsotillståndet för installerade endpoint protection - visar hälsotillståndet för virtuella datorer och datorer som har en slutpunktsskydd installerad. Diagrammet visar antalet virtuella datorer och datorer som är felfria och tal med otillräckligt skydd.
   - Skadlig kod har identifierats – visar hur många virtuella datorer och datorer där Security Center rapporterar identifierat en skadlig kod.
   - Angripna datorer – visar hur många virtuella datorer och datorer där Security Center rapporterar angrepp av skadlig kod.

   Längst ned i instrumentpanelen är det en lista över endpoint protection-problem som innehåller följande information:  

   - **Totalt antal** -antalet virtuella datorer och datorer som påverkas av problemet.
   - Ett liggande sammanställa antalet virtuella datorer och datorer som påverkas av problemet. Färger i fältet identifiera prioritet:

      - Röd - hög prioritet och bör åtgärdas omedelbart
      - Orange - Medelhög prioritet och bör åtgärdas så snart som möjligt

3. Välj **Endpoint protection inte installeras på Azure Virtual Machines**.

   ![Välj Endpoint protection inte installeras på Azure Virtual Machines][3]

4. Under **Endpoint protection inte installeras på Azure Virtual Machines** är en lista över virtuella Azure-datorer som inte har installerat program mot skadlig kod.  Du kan välja att installera program mot skadlig kod på alla virtuella datorer i listan eller välja enskilda virtuella datorer för att installera program mot skadlig kod på genom att klicka på en specifik VM.
5. Under **Välj Endpoint protection**, Välj slutpunktsskydd som du vill använda. I det här exemplet väljer **Microsoft Antimalware**.
6. Mer information om endpoint protection lösningen visas. Välj **Skapa**.

## <a name="install-antimalware-on-non-azure-computers"></a>Installera program mot skadlig kod på Azure-datorer

1. Gå tillbaka till **Endpoint protection-problem** och välj **Endpoint protection inte installeras på Azure-datorer**.

   ![Välj Endpoint protection inte installeras på Azure-datorer][4]

2. Under **Endpoint protection inte installeras på Azure-datorer**, Välj en arbetsyta. En logganalys sökfråga filtrerats till arbetsytan öppnas och visar en lista över datorer som saknar program mot skadlig kod. Välj en dator i listan för mer information.

   ![Logganalys Sök][5]

En annan sökresultatet öppnas med information som filtrerad endast för den datorn.

  ![Logganalys Sök][6]

> [!NOTE]
> Vi rekommenderar att endpoint protection etableras för alla virtuella datorer och datorer för att identifiera och ta bort virus, spionprogram och annan skadlig programvara.
>
>

## <a name="next-steps"></a>Nästa steg
Den här artikeln visades hur du implementerar Security Center-rekommendationen ”installera Endpoint Protection”. Mer information om hur du aktiverar Microsoft Antimalware i Azure finns i följande dokument:

* [Microsoft Antimalware för molntjänster och virtuella datorer](../security/azure-security-antimalware.md) – Lär dig hur du distribuerar Microsoft Antimalware.

Mer information om Security Center finns i följande dokument:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om säkerhet och Azure kompatibilitet.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
