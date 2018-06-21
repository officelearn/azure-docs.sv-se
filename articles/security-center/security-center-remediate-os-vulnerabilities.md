---
title: Åtgärda säkerhetskonfigurationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskrivs hur du implementerar en rekommendation för Azure Security Center, ”reparera säkerhetskonfigurationer”.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2018
ms.author: terrylan
ms.openlocfilehash: 3af8f211c19fde9d2fc79f41fc13009570a9b4de
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285924"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Åtgärda säkerhetskonfigurationer i Azure Security Center
Azure Security Center analyserar dagligen operativsystem (OS) på virtuella datorer (VM) och för en konfiguration som kan göra de virtuella datorerna och datorer som är mer sårbara för angrepp. Security Center rekommenderar att du löser säkerhetsproblem när Operativsystemets konfiguration matchar inte de rekommenderade konfigurationen säkerhetsreglerna och rekommenderar konfigurationsändringar för att åtgärda dessa problem.

Läs mer om de specifika konfigurationer som övervakas av [lista över rekommenderade konfigurationsregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Information om hur du anpassar security configuration utvärderingar finns [anpassa OS säkerhetskonfigurationer i Azure Security Center (förhandsgranskning)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
”Åtgärda säkerhetskonfigurationer” visas som en rekommendation i Security Center. Rekommendationen visas under **rekommendationer** > **Compute**.

Det här exemplet innehåller rekommendationen ”reparera säkerhetskonfigurationer” under **Compute**.
1. I Security Center i den vänstra rutan, Välj **Compute**.  
  Den **Compute** öppnas.

   ![Åtgärda säkerhetskonfigurationer][1]

2. Välj **reparera säkerhetskonfigurationer**.  
  Den **säkerhetskonfigurationer** öppnas.

   ![Fönstret ”säkerhetskonfigurationer”][2]

  Den övre delen av instrumentpanelen visar:

  - **Kunde inte regler efter allvarlighetsgrad**: det totala antalet regler för att konfigurationen av OS misslyckades över dina virtuella datorer och datorer, nedbrutna efter allvarlighetsgrad.
  - **Det gick inte regler av typen**: det totala antalet regler för att konfigurationen av OS misslyckades över dina virtuella datorer och datorer, nedbrutna av typen.
  - **Kunde inte Windows-regler**: det totala antalet regler misslyckades på grund av Windows OS-konfigurationer.
  - **Kunde inte Linux regler**: det totala antalet regler misslyckades på grund av Linux OS-konfigurationer.

  Den nedre delen av instrumentpanelen visar alla misslyckade regler för dina virtuella datorer och datorer och hur allvarligt klassas. Listan innehåller följande element:

  - **CCEID**: den CCE Unik identifierare för regeln. Security Center använder Common Configuration Enumeration (CCE) för att tilldela konfigurationsregler unika identifierare.
  - **Namnet**: misslyckade regelns namn.
  - **Regeltyp**: den *registernyckeln*, *säkerhetsprincip*, *granskningsprincip*, eller *IIS* regeltyp.
  - **Nej. för virtuella datorer och datorer**: det totala antalet virtuella datorer och datorer som misslyckats regeln gäller för.
  - **Regeln allvarlighetsgrad**: värdet för det CCE *kritisk*, *viktigt*, eller *varning*.
  - **Tillstånd**: det aktuella tillståndet för rekommendationen:

    - **Öppen**: Rekommendationen har inte utförts än.
    - **Pågående**: rekommendationen används för närvarande i resurser och ingen åtgärd krävs av dig.
    - **Matcha**: rekommendationen har tillämpats. Transaktionen är bara tillgängligt när problemet är löst.

3. Om du vill visa information om misslyckade regeln väljer du den i listan.

   ![Detaljerad vy av en misslyckad konfigurationsregel][3]

   I den detaljerade vyn visas följande information:

   - **Namnet**: namnet på regeln.
   - **CCIED**: den CCE Unik identifierare för regeln.
   - **OS-version**: OS-versionen av den virtuella datorn eller en dator.
   - **Regeln allvarlighetsgrad**: värdet för det CCE *kritisk*, *viktigt*, eller *varning*.
   - **Fullständig beskrivning**: beskrivning av regeln.
   - **Säkerhetsproblem**: förklaring av säkerhetsproblem eller risk om regeln inte tillämpas.
   - **Potentiella påverkan**: marknadsfördelar när regeln har tillämpats.
   - **Motåtgärden**: steg.
   - **Förväntat värde**: det värde som förväntas när Security Center analyserar VM OS konfigurationen mot regeln.
   - **Faktiskt värde**: värdet som returneras när en analys av VM OS konfigurationen mot regeln.
   - **Regeln åtgärden**: åtgärden som används av Security Center under analysen av VM OS konfigurationen mot regeln.

4. Längst upp i fönstret detaljerad vy, Välj **Sök**.  
  Sök öppnas en lista över arbetsytor som har virtuella datorer och datorer med matchningsfel för valda säkerhet konfigurationer. Arbetsytan markeringen visas bara om den markerade regeln gäller för flera virtuella datorer som är anslutna till olika arbetsytor.

   ![Listan arbetsytor][4]

5. Välj en arbetsyta.  
  En sökfråga logganalys öppnas filtrerade till arbetsytan med säkerhet konfigurationer matchningsfel.

   ![Med OS-problem][5]

6. Välj en dator i listan.  
  Ett nytt sökresultat öppnas med information som filtrerad endast för den datorn.

   ![Detaljerad information om den valda datorn][6]

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”reparera säkerhetskonfigurationer”. Information om hur du anpassar security configuration utvärderingar finns [anpassa OS säkerhetskonfigurationer i Azure Security Center (förhandsgranskning)](security-center-customize-os-security-config.md).

De specifika konfigurationer som övervakas finns [lista över rekommenderade konfigurationsregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center använder Common Configuration Enumeration (CCE) för att tilldela konfigurationsregler unika identifierare. Mer information finns i [CCE](https://nvd.nist.gov/cce/index.cfm) plats.

Mer information om Security Center finns i följande resurser:

* En lista över Windows och Linux virtuella datorer som stöds, se [plattformar som stöds i Azure Security Center](security-center-os-coverage.md).
* Information om hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper finns [ställa in säkerhetsprinciper i Azure Security Center](security-center-policies.md).
* Information om hur rekommendationer hjälper dig att skydda dina Azure-resurser finns [hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).
* Information om hur du övervakar hälsotillståndet för dina Azure-resurser finns [övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md).
* Information om hur du hanterar och åtgärdar säkerhetsaviseringar finns [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).
* Information om hur du övervakar dina partnerlösningars hälsostatus finns [övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md).
* Svar på vanliga frågor och svar om tjänsten finns [Azure Security Center vanliga frågor och svar](security-center-faq.md).
* Blogginlägg om säkerhet och Azure kompatibilitet, se [Azure-säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
