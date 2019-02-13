---
title: Övervakning av filintegritet i Azure Security Center | Microsoft Docs
description: " Lär dig hur du aktiverar Filintegritetsövervakning i Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 50f2c174f21243a70c830c1c30127e0629c81972
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110259"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Övervakning av filintegritet i Azure Security Center
Lär dig hur du konfigurerar filen integritet övervakning (FIM) i Azure Security Center med den här genomgången.

## <a name="what-is-fim-in-security-center"></a>Vad är FIM i Security Center?
Filen integritet övervakning (FIM), även känt som ändra övervakning, undersöker filer och register av operativsystem och programvara för ändringar som kan tyda på ett angrepp. En jämförelsemetod används för att fastställa om det aktuella tillståndet för filen skiljer sig från den senaste genomsökningen av filen. Du kan använda den här jämförelsen för att fastställa om giltiga eller misstänkta ändringar har gjorts till dina filer.

Security Center-Filintegritetsövervakning kontrollerar integriteten för Windows-filer, Windows-registret och Linux-filer. Du kan välja de filer som du vill övervakade genom att aktivera FIM. Security Center övervakar filer med FIM som aktiverats för aktiviteten som:

- Skapa en fil- och registret och borttagning
- Filändringar (ändringar i filstorlek och åtkomstkontrollistor innehållets hash-värde)
- Registret ändringar (ändringar i storlek, åtkomstkontrollistor, typ och innehållet)

Entiteter för att övervaka, som du kan enkelt aktivera FIM på rekommenderar Security Center. Du kan även definiera dina egna principer för FIM eller entiteter för att övervaka. Den här genomgången visar hur du gör.

> [!NOTE]
> Filen integritet övervakning (FIM)-funktionen fungerar för Windows och Linux-datorer och virtuella datorer och är tillgängligt på standardnivån i Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
FIM överför data till Log Analytics-arbetsytan. Data kan tillkomma, baserat på mängden data som du överför. Se [priserna för Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) vill veta mer.
>
>

> [!NOTE]
> FIM använder lösningen Azure ändringsspårning för att spåra och identifiera ändringar i din miljö. När Filintegritetsövervakning är aktiverad, har du en **ändringsspårning** resurs av typen lösning. Om du tar bort den **ändringsspårning** resurs kan du inaktivera Filintegritetsövervakning i Security Center.
>
>

## <a name="which-files-should-i-monitor"></a>Vilka filer bör övervaka?
Du bör tänka på de filer som är viktiga för dina system och program när du väljer vilka filer som ska övervakas. Överväga att välja filer som du inte tror att ändra utan att planera. Välja filer som ofta ändras av program eller operativsystem (till exempel loggfiler och textfiler) skapa mycket brus som gör det svårt att upptäcka en attack.

Security Center rekommenderar som du bör övervaka som standard enligt kända angreppsmönster som innehåller filer och register ändringar.

## <a name="using-file-integrity-monitoring"></a>Med hjälp av Filintegritetövervakning
1. Öppna instrumentpanelen för **Security Center**.
2. I det vänstra fönstret under **Avancerat Molnskydd**väljer **Filintegritetsövervakning**.
![Instrumentpanelen för Security Center][1]

**Övervakning av filintegritet** öppnas.
  ![Instrumentpanelen för Security Center][2]

Den här informationen är för varje arbetsyta:

- Totala antalet ändringar som gjorts under den senaste veckan (du kanske ser ett bindestreck ”-” om FIM inte är aktiverad på arbetsytan)
- Totala antalet datorer och virtuella datorer som rapporterar till arbetsytan
- Geografisk plats i arbetsytan
- Azure-prenumeration som arbetsytan finns under

Följande knappar får också anges för en arbetsyta:

- ![Aktivera ikon][3] Anger att FIM inte har aktiverats för arbetsytan. Att välja arbetsytan kan du aktivera FIM på alla datorer under arbetsytan.
- ![Uppgradera planen för ikonen][4] anger arbetsytan eller prenumeration inte körs i Security Center Standard-nivån. Prenumerationen måste köras Standard för att använda FIM-funktionen.  Att välja arbetsytan kan du uppgradera till Standard. Mer information om Standard-nivån och hur du uppgraderar finns [uppgradera till standardnivån i Security Center för ökad säkerhet](security-center-pricing.md).
- En tom (det finns ingen knapp) innebär att FIM redan är aktiverad på arbetsytan.

Under **Filintegritetsövervakning**, du kan välja en arbetsyta för att aktivera FIM för den arbetsytan kan visa instrumentpanelen för övervakning av filintegritet för den arbetsytan eller [uppgradera](security-center-pricing.md) arbetsytan till Standard.

## <a name="enable-fim"></a>Aktivera FIM
För att aktivera FIM på en arbetsyta:

1. Under **Filintegritetsövervakning**, Välj en arbetsyta med den **aktivera** knappen.
2. **Aktivera filintegritetsövervakning** öppnas visar antalet Windows och Linux-datorer under arbetsytan.

   ![Aktivera filintegritetsövervakning][5]

   De rekommenderade inställningarna för Windows och Linux visas också.  Expandera **Windows filer**, **registret**, och **Linux-filer** att se en fullständig lista över rekommenderade objekt.

3. Avmarkera alla rekommenderade entiteter som du inte vill använda FIM för att.
4. Välj **gäller filintegritetsövervakning** att aktivera FIM.

> [!NOTE]
> Du kan ändra inställningarna när som helst. Se [redigera övervakade enheter](security-center-file-integrity-monitoring.md#edit-monitored-items) nedan om du vill veta mer.
>
>

## <a name="view-the-fim-dashboard"></a>Visa instrumentpanelen för FIM
Den **filintegritetsövervakning** instrumentpanelen visas för arbetsytor där FIM är aktiverat. FIM-instrumentpanelen öppnas när du har aktiverat FIM på en arbetsyta, eller när du väljer en arbetsyta i den **Filintegritetsövervakning** fönster som redan har aktiverat FIM.

![Filen övervakning av filintegritet instrumentpanel][6]

FIM-instrumentpanel för en arbetsyta visas följande:

- Totalt antal datorer som är anslutna till arbetsytan
- Totala antalet ändringar som gjorts under den valda tidsperioden
- En detaljerad analys av Ändringstyp (filer, registret)
- En detaljerad analys av ändringskategori (ändras, har lagts till, tas bort)

Att välja Filter överst på instrumentpanelen kan du använda den tidsperiod som du vill se ändringar för.

![Period tidsfiltret][7]

Den **datorer** fliken (se ovan) visar en lista över alla datorer som rapporterar till den här arbetsytan. För varje dator visas på instrumentpanelen:

- Totalt antal ändringar som gjorts under den valda tidsperioden
- En detaljerad analys av totalt antal ändringar som filändringar eller ändringar i registret

**Loggsöknings-** öppnas när du anger ett datornamn i sökningen fältet eller välj en dator som visas under fliken datorer. Loggsökning visar alla ändringar som gjorts under den valda tidsperioden för datorn. Du kan expandera en ändring för mer information.

![Loggsökning][8]

Den **ändringar** fliken (se nedan) visar alla ändringar för arbetsytan under den valda tidsperioden. För varje entitet som har ändrats, instrumentpanelen visar den:

- Datorn då ändringen utfördes på
- Typ av ändring (registret eller fil)
- Ändringskategori (ändras, har lagts till, tas bort)
- Datum och tid för ändring

![Ändringar för arbetsytan][9]

**Ändringsinformation** öppnas när du gör en ändring i sökningen fältet eller välj en entitet som visas under den **ändringar** fliken.

![Information om ändringar][10]

## <a name="edit-monitored-entities"></a>Redigera övervakas entiteter

1. Gå tillbaka till den **instrumentpanel för Filintegritetsövervakning** och välj **inställningar**.

  ![Inställningar][11]

  **Arbetsytekonfiguration** öppnas visar tre flikar: **Windows-registret**, **Windows filer**, och **Linux-filer**. Varje flik visas de entiteter som du kan redigera i den kategorin. För varje entitet som listas Security Center identifierar om FIM är aktiverad (SANT) eller inte har aktiverats (FALSKT).  Redigera entiteten kan du aktivera eller inaktivera FIM.

  ![Arbetsytekonfiguration][12]

2. Välj en identityprotection. I det här exemplet har vi valt ett objekt under Windows-registret. **Redigera för ändringsspårning** öppnas.

  ![Redigera eller ändringsspårning][13]

Under **Redigera för ändringsspårning** kan du:

- Aktivera (SANT) eller inaktivera (FALSKT) filintegritetsövervakning
- Ange eller ändra enhetens namn
- Ange eller ändra värdet eller sökväg
- Ta bort entiteten, ignorera ändringen eller spara ändringen

## <a name="add-a-new-entity-to-monitor"></a>Lägg till en ny entitet för att övervaka
1. Gå tillbaka till den **instrumentpanel för filintegritetsövervakning** och välj **inställningar** högst upp. **Arbetsytekonfiguration** öppnas.
2. Under **Arbetsytekonfiguration**, Välj fliken för typ av enhet som du vill lägga till: Windows-registret, filer för Windows eller Linux-filer. I det här exemplet har vi valt **Linux-filer**.

  ![Lägg till ett nytt objekt att övervaka][14]

3. Välj **Lägg till**. **Lägg till för ändringsspårning** öppnas.

  ![Ange information som efterfrågas][15]

4. På den **Lägg till** skriver den begärda informationen och välj **spara**.

## <a name="disable-monitored-entities"></a>Inaktivera övervakas entiteter
1. Gå tillbaka till den **Filintegritetsövervakning** instrumentpanelen.
2. Välj en arbetsyta där FIM är aktiverad. En arbetsyta är aktiverad för FIM om Aktivera-knappen eller uppgradera planera knappen saknas.

  ![Välj en arbetsyta där FIM är aktiverat][16]

3. Välj under övervakning av filintegritet, **inställningar**.

  ![Välj inställningar][17]

4. Under **Arbetsytekonfiguration**, Välj en grupp där **aktiverad** har angetts till true.

  ![Arbetsytekonfiguration][18]

5. Under **Redigera för ändringsspårning** fönstret set **aktiverad** till False.

  ![Set-aktiverad på FALSKT][19]

6. Välj **Spara**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Mappen och sökvägen övervakning med jokertecken

Du kan använda jokertecken för att enklare att spåra kataloger. När du konfigurerar mappen övervakning med hjälp av jokertecken gäller följande regler:
-   Jokertecken krävs för att spåra flera filer.
-   Jokertecken kan bara användas i det sista segmentet i en sökväg, till exempel C:\folder\file eller /etc/*.conf
-   Om en miljövariabel innehåller en sökväg som inte är giltig, verifiering lyckas men sökvägen misslyckas när maskinvaruinventering körs.
-   När du anger sökvägen, undvika Allmänt sökvägar, till exempel c:\*. * som ger för många mappar är slut.

## <a name="disable-fim"></a>Inaktivera FIM
Du kan inaktivera FIM. FIM använder lösningen Azure ändringsspårning för att spåra och identifiera ändringar i din miljö. Genom att inaktivera FIM du ta bort lösningen ändringsspårning från valda arbetsytan.

1. Om du vill inaktivera FIM, gå tillbaka till den **Filintegritetsövervakning** instrumentpanelen.
2. Välj en arbetsyta.
3. Under **Filintegritetsövervakning**väljer **inaktivera**.

  ![Inaktivera FIM][20]

4. Välj **ta bort** att inaktivera.

## <a name="next-steps"></a>Nästa steg
I den här artikeln berättade om du använder filen integritet övervakning (FIM) i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa](security-center-monitoring.md)– Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)– Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Security Center vanliga frågor och svar](security-center-faq.md)--finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
