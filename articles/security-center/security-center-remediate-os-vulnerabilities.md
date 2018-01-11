---
title: "Åtgärda säkerhetskonfigurationer i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet beskrivs hur du implementerar rekommenderar Azure Security Center **reparera säkerhetskonfigurationer**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 412234b1486fa15cbc399bcf43be8ce90aac252a
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Åtgärda säkerhetskonfigurationer i Azure Security Center
Azure Security Center analyserar dagligen operativsystem (OS) på virtuella datorer (VM) och för en konfiguration som kan göra de virtuella datorerna och datorer som är mer sårbara för angrepp. Security Center rekommenderar att du löser säkerhetsproblem när Operativsystemets konfiguration matchar inte de rekommenderade konfigurationen säkerhetsreglerna och rekommenderar konfigurationsändringar för att åtgärda dessa problem.

Läs mer om vilka inställningar som övervakas av [lista över rekommenderade konfigurationsregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Se [anpassa OS säkerhetskonfigurationer](security-center-customize-os-security-config.md) att lära dig hur du anpassar security configuration bedömningar i Security Center.

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
Åtgärda säkerhet konfigurationer matchningsfel presenteras som en rekommendation i Security Center. Denna rekommendation visas **rekommendationer** och under **Compute**.

I det här exemplet ska vi titta på den **reparera säkerhetskonfigurationer** rekommendation under **Compute**.
1. Välj **Compute** under huvudmenyn Security Center.

   ![Åtgärda säkerhetskonfigurationer][1]

2. Under **Compute**väljer **reparera säkerhetskonfigurationer**. **Säkerhetskonfigurationer** öppnas.

   ![Säkerhetskonfigurationer][2]

  Överst i instrumentpanelen innehåller:

  - Det totala antalet regler efter allvarlighetsgrad som OS-konfigurationen misslyckades över dina Virtuella datorer och datorns.
  - Det totala antalet regler av typen OS-konfigurationen misslyckades över dina Virtuella datorer och datorns.
  - Det gick inte att det totala antalet regler genom Windows OS-konfigurationer och Linux OS-konfigurationer.

  Längst ned på instrumentpanelen visar alla misslyckade regler för dina virtuella datorer och datorer och hur allvarligt klassas. Listan innehåller:

  - **CCEID**: CCE Unik identifierare för regeln. Security Center använder Common Configuration Enumeration (CCE) för att tilldela unika identifierare för konfigurationsregler.
  - **NAMNET**: misslyckade regelns namn
  - **REGELTYP**: registernyckel eller säkerhetsprincip granskningsprincip
  - **ANTAL FÖR virtuella datorer och datorer**: Totalt antal virtuella datorer och datorer som misslyckas kontrollerat gäller för
  - **REGELN ALLVARLIGHETSGRAD**: CCE allvarlighetsgrad Kritisk, viktigt eller varning
  - **STATE (STATUS)**: Här visas det aktuella tillståndet för rekommendationen:

    - **Open (Ej utförd)**: Rekommendationen har inte utförts än.
    - **Pågående**: rekommendationen används för närvarande till dessa resurser och ingen åtgärd krävs av du
    - **Löst**: Rekommendation har redan slutförts. (När problemet har lösts posten är nedtonad)

3. Välj en misslyckad regel i listan för att visa information.

   ![Konfigurationsregler som har misslyckats][3]

  Den här informationen är på det här bladet:

  - NAMN – Namnet på regel
  - CCIED--CCE Unik identifierare för regeln
  - OS-Version – OS-versionen av den virtuella datorn eller en dator
  - --REGELN CCE allvarlighetsgrad allvarlighetsgrad Kritisk, viktigt eller varning
  - FULLSTÄNDIG beskrivning – Beskrivning av regeln
  - SÄKERHETSPROBLEM--Förklaring av säkerhetsproblem eller risk om regeln inte är installerat
  - POTENTIELLA påverkan--Påverkan på verksamheten när regeln har tillämpats
  - MOTÅTGÄRDEN – steg
  - FÖRVÄNTAT värde--Värde förväntades när Security Center analyserar VM OS konfigurationen mot regeln
  - FAKTISKT värde - Värde returnerades efter analys av VM OS konfigurationen mot regeln
  - --REGELN regeln åtgärden används av Security Center under analys av VM OS konfigurationen mot regeln

4. Välj den **Sök** ikon på menyfliken översta. Sök öppnas lista arbetsytor som har virtuella datorer och datorer med matchningsfel för valda säkerhet konfigurationer. Det här bladet för val av arbetsytan visas bara om den markerade regeln gäller för flera virtuella datorer som är anslutna till olika arbetsytor.

  ![Listan arbetsytor][4]

5. Välj en arbetsyta. En sökfråga logganalys öppnas filtrerade till arbetsytan med säkerhet konfigurationer matchningsfel.

  ![Med OS-problem][5]

6. Välj en dator i listan för mer information. En annan sökresultatet öppnas med information som filtrerad endast för den datorn.

  ![Filtrerade för datorn][6]

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”reparera säkerhetskonfigurationer”. Se [anpassa OS säkerhetskonfigurationer](security-center-customize-os-security-config.md) att lära dig hur du anpassar security configuration bedömningar i Security Center.

Du kan granska uppsättning konfigurationsregler [här](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center använder CCE (Common Configuration uppräkning) för att tilldela unika identifierare för konfigurationsregler. Besök den [CCE](https://nvd.nist.gov/cce/index.cfm) webbplats för mer information.

Mer information om Security Center finns i följande resurser:

* [Plattformar som stöds i Azure Security Center](security-center-os-coverage.md) -visar en lista över Windows och Linux virtuella datorer som stöds.
* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor om Azure Security Center](security-center-faq.md) -finns vanliga frågor om hur du använder tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) -hittar du blogginlägg om säkerhet och Azure kompatibilitet.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
