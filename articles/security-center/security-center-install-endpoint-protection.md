---
title: Hantera problem med endpoint protection med Azure Security Center | Microsoft Docs
description: Lär dig hur du hanterar problem med endpoint protection i Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: a3ac23f3874b85da9c0641264ca6f9c55a7b0515
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300011"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Hantera problem med endpoint protection med Azure Security Center
Azure Security Center övervakar statusen för skydd mot skadlig kod och rapporterar detta under Endpoint protection problem-bladet. Security Center visar problem, till exempel identifierade hot och otillräckligt skydd, vilket kan göra att dina virtuella datorer (VM) och datorer sårbar för program mot skadlig kod hot. Med hjälp av informationen under **problem med Endpoint protection**, du kan identifiera en plan för att åtgärda eventuella problem som identifieras.

Security Center-rapporter på följande problem med endpoint protection:

- Slutpunktsskydd har inte installerats på Azure Virtual Machines – en stöds program mot skadlig kod är inte installerad på dessa virtuella datorer i Azure.
- Slutpunktsskydd har inte installerats på Azure-datorer – en stöds program mot skadlig kod installeras inte på dessa icke-Azure-datorer.
- Endpoint protection health:

   - Inaktuell signatur – en lösning för program mot skadlig kod installeras på dessa virtuella datorer och datorer, men lösningen har inte de senaste signaturerna för program mot skadlig kod.
   - Inget realtidsskydd – en lösning för program mot skadlig kod installeras på dessa virtuella datorer och datorer, men den inte är konfigurerad för skydd i realtid.   Tjänsten kan ha inaktiverats eller Security Center kan inte hämta statusen eftersom lösningen inte stöds. Se [partnerintegration](security-center-partner-integration.md) för en lista med lösningar som stöds.
   - Rapporterar inte – en lösning för program mot skadlig kod har installerats men inte rapporterar data.
   - Okänd – en lösning för program mot skadlig kod har installerats men dess status är okänd eller rapporterar ett okänt fel.

   > [!NOTE]
   > Se [integrera säkerhetslösningar](security-center-partner-integration.md#integrated-azure-security-solutions) en lista över säkerhetslösningar för endpoint protection integreras med Security Center.
   >
   >

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
Problem med endpoint protection presenteras som en rekommendation i Security Center.  Om din miljö är sårbar för program mot skadlig kod hot, den här rekommendationen visas under **rekommendationer** och under **Compute**. Se den **Endpoint protection utfärdar instrumentpanelen**, du måste följa beräkning arbetsflödet.

I det här exemplet ska vi använda **Compute**.  Vi ska titta på hur du installerar program mot skadlig kod på Azure Virtual Machines och på Azure-datorer.

## <a name="install-antimalware-on-azure-vms"></a>Installera program mot skadlig kod på virtuella Azure-datorer

1. Välj **Compute** huvudmenyn i Security Center eller **översikt**.

   ![Välj Compute][1]

2. Under **Compute**väljer **problem med Endpoint protection**. Den **problem med Endpoint protection** instrumentpanelen öppnas.

   ![Välj problem med Endpoint protection][2]

   Överst på instrumentpanelen ger:

   - Installera endpoint protection providers – visar de olika leverantörer som identifieras av Security Center.
   - Hälsostatus för installerat endpoint protection - visar hälsotillståndet för virtuella datorer och datorer som har en endpoint protection-lösning installerad. Diagrammet visar hur många virtuella datorer och datorer som är felfria och med otillräckligt skydd.
   - Skadlig kod har identifierats – visar hur många virtuella datorer och datorer där Security Center rapporterar identifierat en skadlig kod.
   - Attackerade datorer – visar antalet virtuella datorer och datorer där Security Center rapporterar angrepp av skadlig kod.

   Längst ned i instrumentpanelen är det en lista över endpoint protection-problem som innehåller följande information:  

   - **Totalt antal** -antalet virtuella datorer och datorer som påverkas av problemet.
   - Ett liggande sammanställa antalet virtuella datorer och datorer som påverkas av problemet. Färger i fältet identifiera prioritet:

      - Röd - hög prioritet och bör åtgärdas omedelbart
      - Orange - Medelhög prioritet och bör åtgärdas så snart som möjligt

3. Välj **Endpoint protection inte installeras på Azure Virtual Machines**.

   ![Välj Endpoint protection inte installerat på virtuella Azure-datorer][3]

4. Under **Endpoint protection inte installeras på Azure Virtual Machines** är en lista över virtuella Azure-datorer som inte har installerat program mot skadlig kod.  Du kan välja att installera program mot skadlig kod på alla virtuella datorer i listan eller välja att installera program mot skadlig kod på genom att klicka på en specifik virtuell dator på enskilda virtuella datorer.
5. Under **Välj Endpoint protection**, Välj endpoint protection-lösning som du vill använda. I det här exemplet väljer **Microsoft Antimalware**.
6. Mer information om endpoint protection-lösning visas. Välj **Skapa**.

## <a name="install-antimalware-on-non-azure-computers"></a>Installera program mot skadlig kod på Azure-datorer

1. Gå tillbaka till **problem med Endpoint protection** och välj **Endpoint protection inte installerat på Azure-datorer**.

   ![Välj Endpoint protection inte installerat på Azure-datorer][4]

2. Under **Endpoint protection inte installerat på Azure-datorer**, Välj en arbetsyta. En sökfråga för Log Analytics som filtrerats för arbetsytan öppnas och visar en lista över datorer som saknar program mot skadlig kod. Välj en dator i listan för mer information.

   ![Log Analytics-sökningen][5]

En annan sökresultat öppnas med information som filtrerad endast för den datorn.

  ![Log Analytics-sökningen][6]

> [!NOTE]
> Vi rekommenderar att slutpunktsskydd ska etableras för alla virtuella datorer och datorer för att identifiera och ta bort virus, spionprogram och annan skadlig programvara.
>
>

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”installera Endpoint Protection”. Mer information om hur du aktiverar Microsoft Antimalware i Azure finns i följande dokument:

* [Microsoft Antimalware for Cloud Services och Virtual Machines](../security/azure-security-antimalware.md) – Lär dig hur du distribuerar Microsoft Antimalware.

Mer information om Security Center finns i följande dokument:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om Azure-säkerhet och regelefterlevnad.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
