---
title: Filintegritetsövervakning i Azure Security Center | Microsoft-dokument
description: Lär dig hur du konfigurerar FIM (File Integrity Monitoring) i Azure Security Center med den här genomgången.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 4d65ca8d97e1cca81886259d4f15cc880e45be9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604283"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Övervakning av fil i Azure Security Center
Lär dig hur du konfigurerar FIM (File Integrity Monitoring) i Azure Security Center med den här genomgången.

## <a name="what-is-fim-in-security-center"></a>Vad är FIM i Security Center?
Fim (File Integrity Monitoring), även känd som ändringsövervakning, undersöker filer och register över operativsystem, programprogram och andra för ändringar som kan tyda på en attack. En jämförelsemetod används för att avgöra om filens aktuella tillstånd skiljer sig från den senaste genomsökningen av filen. Du kan utnyttja den här jämförelsen för att avgöra om giltiga eller misstänkta ändringar har gjorts i dina filer.

Security Centers filintegritetsövervakning validerar integriteten för Windows-filer, Windows-register och Linux-filer. Du väljer de filer som du vill övervaka genom att aktivera FIM. Security Center övervakar filer med FIM aktiverat för aktiviteter som:

- Fil- och registergenerering och borttagning
- Filändringar (ändringar i filstorlek, åtkomstkontrollistor och hash-av innehållet)
- Registerändringar (ändringar i storlek, åtkomstkontrollistor, typ och innehåll)

Security Center rekommenderar entiteter att övervaka, som du enkelt kan aktivera FIM på. Du kan också definiera dina egna FIM-principer eller entiteter att övervaka. Den här genomgången visar hur.

> [!NOTE]
> Fim-funktionen (File Integrity Monitoring) fungerar för Windows- och Linux-datorer och virtuella datorer och är tillgänglig på standardnivån för Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md). FIM överför data till log analytics-arbetsytan. Dataavgifter tillkommer, baserat på mängden data du laddar upp. Mer information finns i [Priser för Logganalys.](https://azure.microsoft.com/pricing/details/log-analytics/)

FIM använder Azure Change Tracking-lösningen för att spåra och identifiera ändringar i din miljö. När filintegritetsövervakning är aktiverad har du en **ändringsspårningsresurs** av typen **Lösning**. Information om datainsamlingsfrekvens finns i [Information om datainsamling för ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) för Azure Change Tracking.

> [!NOTE]
> Om du tar bort resursen **Ändra spårning** inaktiverar du även funktionen Filintegritetsövervakning i Security Center.

## <a name="which-files-should-i-monitor"></a>Vilka filer ska jag övervaka?
Du bör tänka på de filer som är kritiska för ditt system och program när du väljer vilka filer som ska övervakas. Överväg att välja filer som du inte förväntar dig att ändra utan planering. Om du väljer filer som ofta ändras av program eller operativsystem (till exempel loggfiler och textfiler) skapas mycket brus som gör det svårt att identifiera en attack.

Security Center rekommenderar vilka filer du ska övervaka som standard enligt kända attackmönster som innehåller fil- och registerändringar.

## <a name="using-file-integrity-monitoring"></a>Använda filintegritetsövervakning
1. Öppna instrumentpanelen för **Security Center**.
2. Välj **Filintegritetsövervakning**i den vänstra rutan under **Avancerat molnförsvar**.
![Instrumentpanelen i Security Center][1]

**Filintegritetsövervakningen** öppnas.
  ![Instrumentpanelen i Security Center][2]

Följande information finns för varje arbetsyta:

- Totalt antal ändringar som inträffade under den senaste veckan (du kan se ett streck "-" om FIM inte är aktiverat på arbetsytan)
- Totalt antal datorer och virtuella datorer som rapporterar till arbetsytan
- Arbetsytans geografiska läge
- Azure-prenumeration som arbetsytan är under

Följande knappar kan också visas för en arbetsyta:

- ![Ikonen Aktivera][3] Anger att FIM inte är aktiverat för arbetsytan. Om du väljer arbetsytan kan du aktivera FIM på alla datorer under arbetsytan.
- ![Uppgraderingsplanikonen][4] Anger att arbetsytan eller prenumerationen inte körs under Security Centers standardnivå. Om du vill använda FIM-funktionen måste prenumerationen köra Standard.  Om du väljer arbetsytan kan du uppgradera till Standard. Mer information om standardnivån och hur du uppgraderar finns i [Uppgradera till Security Centers standardnivå för ökad säkerhet](security-center-pricing.md).
- En tom (det finns ingen knapp) innebär att FIM redan är aktiverat på arbetsytan.

Under **Filintegritetsövervakning**kan du välja en arbetsyta för att aktivera FIM för den arbetsytan, visa instrumentpanelen för filintegritetsövervakning för den arbetsytan eller [uppgradera](security-center-pricing.md) arbetsytan till Standard.

## <a name="enable-fim"></a>Aktivera FIM
Så här aktiverar du FIM på en arbetsyta:

1. Under **Filintegritetsövervakning**väljer du en arbetsyta med knappen **Aktivera.**
2. **Aktivera filintegritetsövervakning** öppnar som visar antalet Windows- och Linux-datorer under arbetsytan.

   ![Aktivera filintegritetsövervakning][5]

   De rekommenderade inställningarna för Windows och Linux visas också.  Expandera **Windows-filer,** **registerfiler**och **Linux-filer** för att se hela listan över rekommenderade objekt.

3. Avmarkera alla rekommenderade entiteter som du inte vill använda FIM på.
4. Välj **Använd filintegritetsövervakning** för att aktivera FIM.

> [!NOTE]
> Du kan ändra inställningarna när som helst. Mer information finns i Redigera övervakade entiteter nedan.
>
>

## <a name="view-the-fim-dashboard"></a>Visa FIM-instrumentpanelen
Instrumentpanelen **för filintegritetsövervakning** visar för arbetsytor där FIM är aktiverat. FIM-instrumentpanelen öppnas när du har aktiverat FIM på en arbetsyta eller när du väljer en arbetsyta i fönstret **Filintegritetsövervakning** som redan har FIM aktiverat.

![Instrumentpanel för övervakning av filintegritet][6]

Fim-instrumentpanelen för en arbetsyta visar följande information:

- Totalt antal datorer som är anslutna till arbetsytan
- Totalt antal ändringar som skett under den valda tidsperioden
- En uppdelning av ändringstyp (filer, register)
- En uppdelning av ändringskategori (ändrad, tillagd, borttagen)

Om du väljer Filter högst upp på instrumentpanelen kan du använda den tidsperiod som du vill se ändringar för.

![Filtret Tidsperiod][7]

På fliken **Datorer** (visas ovan) visas alla datorer som rapporterar till den här arbetsytan. För varje maskin visas instrumentpanelen:

- Totala ändringar som skett under den valda tidsperioden
- En uppdelning av totala ändringar som filändringar eller registerändringar

**Loggsökningen** öppnas när du anger ett datornamn i sökfältet eller väljer en dator som visas under fliken Datorer. Du kan expandera en ändring för mer information.

![Loggsökning][8]

På fliken **Ändringar** (visas nedan) visas alla ändringar för arbetsytan under den valda tidsperioden. För varje entitet som har ändrats listar instrumentpanelen följande:

- Dator som ändringen inträffade på
- Typ av ändring (register eller fil)
- Ändringskategori (ändrad, tillagd, borttagen)
- Datum och tid för ändring

![Ändringar för arbetsytan][9]

**Ändringsinformationen** öppnas när du anger en ändring i sökfältet eller väljer en entitet som visas under fliken **Ändringar.**

![Ändra information][10]

## <a name="edit-monitored-entities"></a>Redigera övervakade entiteter

1. Gå tillbaka till **instrumentpanelen för filintegritetsövervakning** och välj **Inställningar**.

   ![Inställningar][11]

   **Workspace Configuration** öppnas med tre flikar: **Windows-registret,** **Windows-filer**och **Linux-filer**. På fliken listas de entiteter som du kan redigera i den kategorin. För varje entitet som anges identifierar Security Center om FIM är aktiverat (sant) eller inte aktiverat (falskt).  Genom att redigera entiteten kan du aktivera eller inaktivera FIM.

   ![Konfiguration av arbetsyta][12]

2. Välj ett identitetsskydd. I det här exemplet valde vi ett objekt under Windows-registret. **Redigera för ändringsspårning** öppnas.

   ![Redigera eller ändra spårning][13]

Under **Redigera för ändringsspårning** kan du:

- Aktivera (Sant) eller inaktivera (falsk) filintegritetsövervakning
- Ange eller ändra entitetsnamnet
- Ange eller ändra värdet eller sökvägen
- Ta bort entiteten, ignorera ändringen eller spara ändringen

## <a name="add-a-new-entity-to-monitor"></a>Lägga till en ny entitet som ska övervakas
1. Gå tillbaka till **instrumentpanelen för filintegritetsövervakning** och välj **Inställningar** högst upp. **Konfigurationen av arbetsytan** öppnas.
2. Under **Konfiguration av arbetsyta**väljer du fliken för den typ av entitet som du vill lägga till: Windows-registret, Windows-filer eller Linux-filer. I det här exemplet valde vi **Linux-filer**.

   ![Lägga till ett nytt objekt som ska övervakas][14]

3. Välj **Lägg till**. **Lägg till för ändringsspårning** öppnas.

   ![Ange begärd information][15]

4. Skriv den begärda informationen på sidan **Lägg till** och välj **Spara**.

## <a name="disable-monitored-entities"></a>Inaktivera övervakade entiteter
1. Gå tillbaka till instrumentpanelen **för filintegritetsövervakning.**
2. Välj en arbetsyta där FIM är aktiverat. En arbetsyta är aktiverad för FIM om den saknar knappen Aktivera eller knappen Uppgradera plan.

   ![Välj en arbetsyta där FIM är aktiverat][16]

3. Under Filintegritetsövervakning väljer du **Inställningar**.

   ![Välj inställningar][17]

4. Under **Konfiguration av arbetsyta**väljer du en grupp där **Aktiverad** är inställd på true.

   ![Konfiguration av arbetsyta][18]

5. Under **Fönstret Redigera för ändringsspårning** inställd **aktiverad** till False.

   ![Ange aktiverad för false][19]

6. Välj **Spara**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Mapp- och sökvägsövervakning med jokertecken

Använd jokertecken för att förenkla spårningen mellan kataloger. Följande regler gäller när du konfigurerar mappövervakning med jokertecken:
-   Jokertecken krävs för att spåra flera filer.
-   Jokertecken kan bara användas i det sista segmentet i en sökväg, till exempel C:\folder\file eller /etc/*.conf
-   Om en miljövariabel innehåller en sökväg som inte är giltig, kommer valideringen att lyckas, men sökvägen misslyckas när lagret körs.
-   När du ställer in sökvägen undviker\*du allmänna sökvägar som c: .* som resulterar i att för många mappar genomdreras.

## <a name="disable-fim"></a>Inaktivera FIM
Du kan inaktivera FIM. FIM använder Azure Change Tracking-lösningen för att spåra och identifiera ändringar i din miljö. Genom att inaktivera FIM tar du bort lösningen Ändra spårning från den valda arbetsytan.

1. Om du vill inaktivera FIM går du tillbaka till instrumentpanelen **för filintegritetsövervakning.**
2. Välj en arbetsyta.
3. Under **Filintegritetsövervakning**väljer du **Inaktivera**.

   ![Inaktivera FIM][20]

4. Välj **Ta bort** om du vill inaktivera.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att använda FIM (File Integrity Monitoring) i Security Center. Mer information om Security Center finns på följande sidor:

* [Ställa in säkerhetsprinciper](tutorial-security-policy.md) – Lär dig hur du konfigurerar säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* Övervakning av [säkerhetshälsa](security-center-monitoring.md)– Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.
* [Hantera och svara på säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)– Lär dig hur du hanterar och svarar på säkerhetsaviseringar.
* [Övervakning av partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar hälsostatusen för dina partnerlösningar.
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
