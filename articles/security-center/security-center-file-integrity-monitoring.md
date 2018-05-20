---
title: Filen integritet övervakning i Azure Security Center (förhandsversion) | Microsoft Docs
description: " Lär dig hur du aktiverar övervakning integritet i Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: terrylan
ms.openlocfilehash: 722a4fd11f35f04ed22d73638f07d15c49ea3c26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="file-integrity-monitoring-in-azure-security-center-preview"></a>Filen integritet övervakning i Azure Security Center (förhandsgranskning)
Lär dig hur du konfigurerar filen integritet övervakning (FIM) i Azure Security Center med den här genomgången.

## <a name="what-is-fim-in-security-center"></a>Vad är FIM i Security Center?
Filen integritet övervakning (FIM), även kallat ändra övervakning, undersöker filer och register för operativsystemet och programvara för ändringar som kan tyda på ett angrepp. En jämförelsemetoden används för att avgöra om det aktuella tillståndet för filen skiljer sig från den senaste genomsökningen av filen. Du kan använda den här jämförelsen för att avgöra om giltig eller misstänkta ändringar har gjorts till dina filer.

Security Center-övervakning av integritet kontrollerar integriteten för Windows-filer, Windows-registret och Linux-filer. Du kan välja de filer som du vill övervakade genom att aktivera FIM. Security Center övervakar filer med FIM som aktiverats för aktiviteten:

- Skapa en fil- och registret och borttagning
- Filen ändringar (ändringar i storlek, åtkomstkontrollistor och hashvärdet för innehållet)
- Registret (förändringar i storlek, conrol listor, typ och innehållet)

Security Center rekommenderar enheter att övervaka, som du enkelt kan aktivera FIM på. Du kan också definiera egna FIM principer eller enheter som ska övervakas. Den här genomgången visar hur du gör.

> [!NOTE]
> Filen integritet övervakning (FIM)-funktionen fungerar för Windows och Linux-datorer och virtuella datorer och är tillgänglig på standardnivån av Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
FIM överför data till logganalys-arbetsytan. Data avgifter gäller baserat på mängden data som du överför. Se [logganalys priser](https://azure.microsoft.com/pricing/details/log-analytics/) vill veta mer.
>
>

> [!NOTE]
> FIM använder Azure ändringsspårning lösningen för att spåra och identifiera ändringar i din miljö. När filen integritet övervakning är aktiverad, har du en **ändringsspårning** resurs av typen lösning. Om du tar bort den **ändringsspårning** resurs måste du inaktivera filintegritet övervakning funktion i Security Center.
>
>

## <a name="which-files-should-i-monitor"></a>Vilka filer bör övervaka?
Du bör tänka på de filer som är viktiga för ditt system och program när du väljer vilka filer som ska övervaka. Du bör använda filer som du inte förväntar dig att ändra utan planera. Välja filer som ofta ändras av program eller operativsystem (till exempel loggfiler och textfiler) skapa mycket brus som gör det svårt att upptäcka en attack.

Security Center rekommenderar som du bör övervaka som standard enligt kända angreppsmönster som innehåller filer och registerposter ändringar.

## <a name="using-file-integrity-monitoring"></a>Med hjälp av filen integritet övervakning
1. Öppna instrumentpanelen för **Security Center**.
2. I det vänstra fönstret under **Defense Advanced molnet**väljer **integritet övervaka**.
![Instrumentpanelen Security Center][1]

**Filen integritet övervakning** öppnas.
  ![Instrumentpanelen Security Center][2]

Den här informationen är för varje arbetsyta:

- Totalt antal ändringar som gjorts under den senaste veckan (du kan se ett streck ”-” om FIM inte är aktiverad på arbetsytan)
- Totalt antal datorer och virtuella datorer som rapporterar till arbetsytan
- Geografisk plats på arbetsytan
- Azure-prenumeration som arbetsytan är under

Följande knappar kan även visas för en arbetsyta:

- ![Aktivera ikon][3] Anger att FIM inte har aktiverats för arbetsytan. Att välja arbetsytan kan du aktivera FIM på alla datorer under arbetsytan.
- ![Ikon för uppgraderingsplanen][4] anger arbetsyta eller prenumeration inte körs i Security Center standardnivån. Din prenumeration måste köras Standard för att använda FIM-funktionen.  Om du markerar arbetsytan kan du uppgradera till Standard. Mer information om standardnivån och hur du uppgraderar finns [uppgradera till standardnivån om Security Center för ökad säkerhet](security-center-pricing.md).
- Ett tomt (det finns ingen knapp) innebär att FIM är redan aktiverat på arbetsytan.

Under **integritet Filövervakning**, kan du välja en arbetsyta om du vill aktivera FIM för arbetsytan visa instrumentpanelen för övervakning av integriteten för den arbetsytan eller [uppgradera](security-center-pricing.md) arbetsytan till Standard.

## <a name="enable-fim"></a>Aktivera FIM
Aktivera FIM på en arbetsyta:

1. Under **integritet Filövervakning**, Välj en arbetsyta med den **aktivera** knappen.
2. **Aktivera övervakning av integritet** öppnas visar antalet Windows och Linux-datorer under arbetsytan.

   ![Aktivera övervakning av integritet][5]

   De rekommenderade inställningarna för Windows och Linux visas också.  Expandera **Windows-filer**, **registret**, och **Linux filer** att se en fullständig lista över rekommenderade objekt.

3. Avmarkera alla rekommenderade entiteter som du inte vill tillämpa FIM till.
4. Välj **gäller övervakning av integritet** att aktivera FIM.

> [!NOTE]
> Du kan ändra inställningarna när som helst. Se [redigera övervakade enheter](security-center-file-integrity-monitoring.md#edit-monitored-items) nedan om du vill veta mer.
>
>

## <a name="view-the-fim-dashboard"></a>Visa instrumentpanelen för FIM
Den **integritet övervaka** instrumentpanelen visar för arbetsytor där FIM är aktiverat. FIM-instrumentpanelen öppnas när du har aktiverat FIM på en arbetsyta eller när du väljer en arbetsyta i den **integritet övervaka** fönster som redan har aktiverat FIM.

![Instrumentpanelen för integritet övervakning av fil][6]

FIM-instrumentpanelen för en arbetsyta visas följande:

- Totalt antal datorer som är anslutna till arbetsytan
- Totalt antal ändringar som gjorts under den valda tidsperioden
- En detaljerad analys av Ändringstyp (filer, registret)
- En detaljerad analys av Ändra kategori (ändras, lägga till, tas bort)

Att välja Filter överst på instrumentpanelen kan du använda den tidsperiod som du vill se ändringar för.

![Tidsfiltret period][7]

Den **datorer** (se ovan) visar en lista över alla datorer som rapporterar till den här arbetsytan. För varje dator visas på instrumentpanelen:

- Totalt antal ändringar som gjorts under den valda tidsperioden
- En detaljerad analys av totalt antal ändringar som ändras eller ändringar i registret

**Logga Sök** öppnas när du anger ett datornamn i sökningen eller väljer en dator som finns under fliken datorer. Sök i loggen visar alla ändringar som gjorts under den valda perioden för datorn. Du kan expandera en ändring för mer information.

![Loggsökning][8]

Den **ändringar** (se nedan) visar alla ändringar för arbetsytan under den valda tidsperioden. För varje entitet som har ändrats, instrumentpanelen visar den:

- Ändringen som gjordes på datorn
- Typ av ändring (registret eller fil)
- Ändringskategori (ändras, lägga till, tas bort)
- Datum och tid för ändring

![Ändringar för arbetsytan][9]

**Ändra uppgifter** öppnas när du gör en ändring i sökningen eller väljer en entitet som visas under den **ändringar** fliken.

![Ändra uppgifter][10]

## <a name="edit-monitored-entities"></a>Redigera övervakas entiteter

1. Gå tillbaka till den **instrumentpanelen för övervakning av fil-integritet** och välj **inställningar**.

  ![Inställningar][11]

  **Arbetsytan Configuration** öppnas med tre flikar: **Windows-registret**, **Windows-filer**, och **Linux filer**. Varje flik visar de enheter som du kan redigera i den kategorin. För varje entitet som listas Security Center identifierar om FIM är aktiverad (SANT) eller inte (FALSKT) har aktiverats.  Om du redigerar entiteten kan du aktivera eller inaktivera FIM.

  ![Arbetsytans konfiguration][12]

2. Välj en identityprotection. I det här exemplet har vi valt ett objekt under Windows-registret. **Redigera för ändringsspårning** öppnas.

  ![Redigera eller ändringsspårning][13]

Under **Redigera för ändringsspårning** kan du:

- Aktivera (SANT) eller inaktivera (FALSKT) integritet övervakning
- Ange eller ändra entitetsnamnet på
- Ange eller ändra värdet eller sökvägen
- Ta bort entiteten, ignorera ändringen eller spara ändringen

## <a name="add-a-new-entity-to-monitor"></a>Lägg till en ny entitet att övervaka
1. Gå tillbaka till den **filen integirty övervakning instrumentpanel** och välj **inställningar** längst upp. **Arbetsytan Configuration** öppnas.
2. Under **arbetsytan Configuration**, Välj fliken för typ av enhet som du vill lägga till: Windows-registret, Windows-filer eller Linux-filer. Vi valde i det här exemplet **Linux filer**.

  ![Lägg till ett nytt objekt att övervaka][14]

3. Välj **Lägg till**. **Lägg till för ändringsspårning** öppnas.

  ![Ange information som efterfrågas][15]

4. På den **Lägg till** skriver den begärda informationen och välj **spara**.

## <a name="disable-monitored-entities"></a>Inaktivera övervakas entiteter
1. Gå tillbaka till den **integritet övervaka** instrumentpanelen.
2. Välj en arbetsyta där FIM är aktiverat. En arbetsyta är aktiverad för FIM om Aktivera knappen eller uppgradera planera knappen saknas.

  ![Välj en arbetsyta där FIM är aktiverat][16]

3. Under övervakning integritet väljer **inställningar**.

  ![Välj inställningar][17]

4. Under **arbetsytan Configuration**, väljer du en grupp där **aktiverad** har angetts till true.

  ![Arbetsytekonfiguration][18]

5. Under **Redigera för ändringsspårning** fönstret set **aktiverad** till False.

  ![Ange aktiverad till false][19]

6. Välj **Spara**.

## <a name="disable-fim"></a>Inaktivera FIM
Du kan inaktivera FIM. FIM använder Azure ändringsspårning lösningen för att spåra och identifiera ändringar i din miljö. Genom att inaktivera FIM du ta bort lösningen ändringsspårning från valda arbetsytan.

1. Om du vill inaktivera FIM återgå till den **integritet övervaka** instrumentpanelen.
2. Välj en arbetsyta.
3. Under **integritet Filövervakning**väljer **inaktivera**.

  ![Inaktivera FIM][20]

4. Välj **ta bort** att inaktivera.

## <a name="next-steps"></a>Nästa steg
I den här artikeln du lärt dig att använda filen integritet övervakning (FIM) i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Ställa in säkerhetsprinciper](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa](security-center-monitoring.md)– Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)– Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor om Security Center](security-center-faq.md)--finns vanliga frågor om hur du använder tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

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
