---
title: Åtgärda säkerhetskonfigurationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationen, ”åtgärda säkerhetskonfigurationer”.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: terrylan
ms.openlocfilehash: 74bfc3435ad6d247dabd3a7cbf2910ede5f8c8ca
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296252"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Åtgärda säkerhetskonfigurationer i Azure Security Center
Azure Security Center analyserar dagligen operativsystemet (OS) på dina virtuella datorer (VM) och en konfiguration som kan göra de virtuella datorerna och datorer som är mer sårbara för angrepp. Security Center rekommenderar att du löser säkerhetsproblem när Operativsystemets konfiguration matchar inte de rekommendera säkerhetsreglerna för konfiguration och den rekommenderar konfigurationsändringar för att åtgärda dessa sårbarheter.

Läs mer om vilka konfigurationer som övervakas, den [lista över rekommenderade konfigurationsregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Läs hur du anpassar säkerhetsutvärderingar för konfiguration i [anpassa OS-säkerhetskonfigurationer i Azure Security Center (förhandsversion)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
”Åtgärda säkerhetskonfigurationer” visas som en rekommendation i Security Center. Rekommendationen visas under **rekommendationer** > **Compute och appar**.

Det här exemplet beskriver ”åtgärda säkerhetskonfigurationer” rekommendationen under **Compute och appar**.
1. I Security Center, i den vänstra rutan väljer **Compute och appar**.  
  Den **Compute och appar** öppnas.

   ![Åtgärda säkerhetskonfigurationer][1]

2. Välj **åtgärda säkerhetskonfigurationer**.  
  Den **säkerhetskonfigurationer** öppnas.

   ![Fönstret ”säkerhetskonfigurationer”][2]

  Överst på instrumentpanelen visar:

  - **Misslyckade regler efter allvarlighetsgrad**: det totala antalet regler att Operativsystemets konfiguration misslyckades mellan dina virtuella datorer och datorer, indelade efter allvarlighetsgrad.
  - **Misslyckade regler efter typ**: det totala antalet regler att Operativsystemets konfiguration misslyckades mellan dina virtuella datorer och datorer, indelade efter typen.
  - **Windows-regler som inte**: det totala antalet regler misslyckades på grund av din Windows OS-konfigurationer.
  - **Linux-regler som inte**: det totala antalet regler misslyckades på grund av Linux OS-konfigurationer.

  Den nedre delen av instrumentpanelen visas alla misslyckade regler för dina virtuella datorer och datorer och allvarlighetsgraden för saknad uppdatering. Listan innehåller följande element:

  - **CCEID**: The CCE Unik identifierare för regeln. Security Center använder Common Configuration Enumeration (CCE) för att tilldela unika identifierare till konfigurationsregler.
  - **Namn på**: namnet på den misslyckade regeln.
  - **Regeltyp**: den *registernyckeln*, *säkerhetsprincip*, *granskningsprincip*, eller *IIS* regeltyp.
  - **Nej. VM: ar och datorer**: det totala antalet virtuella datorer och datorer som misslyckade regeln gäller för.
  - **Regeln allvarlighetsgrad**: The CCE värdet *kritisk*, *viktigt*, eller *varning*.
  - **Tillstånd**: det aktuella tillståndet för rekommendationen:

    - **Öppen**: Rekommendationen har inte utförts än.
    - **Pågår**: rekommendationen håller på att resurserna och ingen åtgärd krävs av dig.
    - **Löst**: rekommendationen har tillämpats. När problemet har lösts, inaktiveras posten.

3. Om du vill visa information om en regel som inte, väljer du den i listan.

   ![Detaljerad vy av en misslyckad konfigurationsregel][3]

   I den detaljerade vyn visas följande information:

   - **Namn på**: namnet på regeln.
   - **CCIED**: The CCE Unik identifierare för regeln.
   - **OS-version**: OS-versionen av den virtuella datorn eller datorn.
   - **Regeln allvarlighetsgrad**: The CCE värdet *kritisk*, *viktigt*, eller *varning*.
   - **Fullständig beskrivning**: beskrivning av regeln.
   - **Säkerhetsproblem**: förklaring av säkerhetsproblem eller risk om regeln inte tillämpas.
   - **Potentiell påverkan**: inverkan på företaget när regeln tillämpas.
   - **Motåtgärden**: anvisningarna för reparation.
   - **Förväntat värde**: det värde som förväntas när Security Center analyserar din konfiguration för VM OS mot regeln.
   - **Faktiskt värde**: det värde som returneras efter en analys av din VM Operativsystemets konfiguration mot regeln.
   - **Regeln åtgärden**: Regelåtgärd som används av Security Center under analysen av dina Virtuella Operativsystemets konfiguration mot regeln.

4. Överst i fönstret detaljerad vy väljer **Search**.  
  Sök öppnas en lista över arbetsytor som har virtuella datorer och datorer med matchningsfel för valda security-konfigurationer. Val av arbetsyta visas bara om den valda regeln gäller för flera virtuella datorer som är anslutna till olika arbetsytor.

   ![Listade arbetsytor][4]

5. Välj en arbetsyta.  
  En sökfråga för Log Analytics öppnas filtrerade till arbetsytan med säkerhet konfigurationer matchningsfel.

   ![Arbetsyta med OS-säkerhetsproblem][5]

6. Välj en dator i listan.  
  En ny sökresultat öppnas med information som filtrerad endast för den datorn.

   ![Detaljerad information om den valda datorn][6]

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”åtgärda säkerhetskonfigurationer”. Läs hur du anpassar säkerhetsutvärderingar för konfiguration i [anpassa OS-säkerhetskonfigurationer i Azure Security Center (förhandsversion)](security-center-customize-os-security-config.md).

Om du vill granska vilka konfigurationer som övervakas, se [lista över rekommenderade konfigurationsregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center använder Common Configuration Enumeration (CCE) för att tilldela unika identifierare till konfigurationsregler. Mer information går du till den [CCE](https://nvd.nist.gov/cce/index.cfm) plats.

Om du vill veta mer om Security Center finns i följande resurser:

* En lista över Windows och Linux-datorer som stöds finns i [plattformar som stöds i Azure Security Center](security-center-os-coverage.md).
* Läs hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper i [ange säkerhetsprinciper i Azure Security Center](security-center-policies.md).
* Läs hur rekommendationer hjälper dig att skydda dina Azure-resurser i [hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).
* Läs hur du övervakar hälsotillståndet för dina Azure-resurser i [övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md).
* Läs hur du hanterar och åtgärdar säkerhetsaviseringar i [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).
* Läs hur du övervakar dina partnerlösningars hälsostatus i [övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md).
* Svar på vanliga frågor och svar om tjänsten finns i [Azure Security Center vanliga frågor och svar](security-center-faq.md).
* För blogginlägg om Azure-säkerhet och regelefterlevnad, se [Azure Security-bloggen](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
