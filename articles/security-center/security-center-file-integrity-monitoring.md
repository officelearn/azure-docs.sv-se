---
title: Övervakning av fil integritet i Azure Security Center | Microsoft Docs
description: Lär dig hur du konfigurerar FIM (File Integrity Monitoring) i Azure Security Center att använda den här genom gången.
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
ms.openlocfilehash: 46ff4d9c941af25fcec3a70d7a2e6da95da59f32
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106703"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Övervakning av fil i Azure Security Center
Lär dig hur du konfigurerar FIM (File Integrity Monitoring) i Azure Security Center att använda den här genom gången.

## <a name="what-is-fim-in-security-center"></a>Vad är FIM i Security Center?
File Integrity Monitoring (FIM), även kallat ändrings övervakning, undersöker filer och register över operativ system, program vara och andra för ändringar som kan tyda på ett angrepp. En jämförelse metod används för att avgöra om filens aktuella tillstånd skiljer sig från den senaste genomsökningen av filen. Du kan utnyttja den här jämförelsen för att avgöra om giltiga eller misstänkta ändringar har gjorts i dina filer.

Security Centers övervakning av fil integriteten verifierar integriteten för Windows-filer, Windows-registret och Linux-filer. Du väljer de filer som du vill övervaka genom att aktivera FIM. Security Center övervakar filer med FIM aktiverat för aktivitet som:

- Skapa och ta bort filer och register
- Fil ändringar (ändringar i fil storlek, åtkomst kontrol listor och hash-värde för innehållet)
- Register ändringar (ändringar i storlek, åtkomst kontrol listor, typ och innehåll)

Security Center rekommenderar att entiteter övervakas, som du enkelt kan aktivera FIM på. Du kan också definiera egna FIM-principer eller entiteter som ska övervakas. Den här genom gången visar hur.

> [!NOTE]
> FIM-funktionen (File Integrity Monitoring) fungerar för Windows-och Linux-datorer och virtuella datorer och finns på standard nivån för Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md). FIM överför data till Log Analytics-arbetsytan. Data avgifterna gäller baserat på mängden data som du överför. Mer information finns i [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/log-analytics/) .

FIM använder Azure Ändringsspårning-lösningen för att spåra och identifiera ändringar i din miljö. När fil integritets övervakning har Aktiver ATS har du en **ändringsspårning** resurs av typen **lösning**. Information om frekvens information om data insamling finns i [ändringsspårning information om data insamling](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) för Azure ändringsspårning.

> [!NOTE]
> Om du tar bort **ändringsspårning** resursen inaktiverar du även funktionen för övervakning av fil integritet i Security Center.

## <a name="which-files-should-i-monitor"></a>Vilka filer ska jag övervaka?
Du bör tänka på vilka filer som är kritiska för ditt system och dina program när du väljer vilka filer som ska övervakas. Överväg att välja filer som du inte förväntar dig att ändra utan att planera. Om du väljer filer som ofta ändras av program eller operativ system (t. ex. loggfiler och textfiler) skapas många brus som gör det svårt att identifiera ett angrepp.

Security Center innehåller följande lista över rekommenderade objekt som ska övervakas baserat på kända angrepps mönster. Detta inkluderar filer och Windows-registernycklar. Alla nycklar är under HKEY_LOCAL_MACHINE (HKLM) i tabellen.)

|**Linux-filer**|**Windows-filer**|**Windows-registernycklar**|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell-mappar|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell-mappar|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell-mappar|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell-mappar|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|

## <a name="using-file-integrity-monitoring"></a>Använda övervakning av fil integritet
1. Öppna instrumentpanelen för **Security Center**.
2. I det vänstra fönstret under **Avancerat moln skydd**väljer du **fil integritets övervakning**.
![Instrumentpanelen i Security Center][1]

**Övervakning av fil integritet** öppnas.
  ![Instrumentpanelen i Security Center][2]

Följande information anges för varje arbets yta:

- Totalt antal ändringar som inträffat under den senaste veckan (du kan se ett bindestreck "-" om FIM inte är aktiverat på arbets ytan)
- Totalt antal datorer och virtuella datorer som rapporterar till arbets ytan
- Geografisk plats för arbets ytan
- Azure-prenumeration som arbets ytan är under

Följande knappar kan också visas för en arbets yta:

- ![Aktivera ikon][3] Anger att FIM inte är aktiverat för arbets ytan. Genom att välja arbets ytan kan du aktivera FIM på alla datorer under arbets ytan.
- ![Ikonen][4] uppgradera plan indikerar att arbets ytan eller prenumerationen inte körs under Security Center standard nivån. Din prenumeration måste köra standard för att använda FIM-funktionen.  Genom att välja arbets ytan kan du uppgradera till standard. Mer information om standard nivån och hur du uppgraderar finns i [Uppgradera till Security Center standard nivå för ökad säkerhet](security-center-pricing.md).
- En tom (det finns ingen knapp) innebär att FIM redan är aktiverat på arbets ytan.

Under **övervakning av fil integritet**kan du välja en arbets yta för att aktivera FIM för den arbets ytan, Visa instrument panelen för övervakning av fil integritet för den arbets ytan eller [Uppgradera](security-center-pricing.md) arbets ytan till standard.

## <a name="enable-fim"></a>Aktivera FIM
Så här aktiverar du FIM på en arbets yta:

1. Under **övervakning av fil integritet**väljer du en arbets yta med knappen **Aktivera** .
2. **Aktivera övervakning av fil integritet** öppnas och visar antalet Windows-och Linux-datorer under arbets ytan.

   ![Aktivera övervakning av fil integritet][5]

   De rekommenderade inställningarna för Windows och Linux visas också.  Expandera **Windows-filer**, **register**-och **Linux-filer** för att se en fullständig lista över rekommenderade objekt.

3. Avmarkera alla rekommenderade entiteter som du inte vill använda FIM på.
4. Välj **Använd övervakning av fil integritet** för att aktivera FIM.

> [!NOTE]
> Du kan ändra inställningarna när som helst. Mer information finns i redigera övervakade entiteter nedan.


## <a name="view-the-fim-dashboard"></a>Visa FIM-instrumentpanelen
Instrument panelen för **fil integritets övervakning** visas för arbets ytor där FIM är aktiverat. FIM-instrumentpanelen öppnas när du har aktiverat FIM på en arbets yta eller när du väljer en arbets yta i fönstret för **övervakning av fil integritet** som redan har FIM aktiverat.

![Instrument panel för fil integritets övervakning][6]

FIM-instrumentpanelen för en arbets yta visar följande information:

- Totalt antal datorer som är anslutna till arbets ytan
- Totalt antal ändringar som inträffat under den valda tids perioden
- En analys av ändrings typ (filer, registret)
- En analys av ändrings kategorin (ändrad, tillagd, borttagen)

Genom att välja filter överst på instrument panelen kan du använda den tids period som du vill se ändringarna för.

![Filter för tids period][7]

Fliken **datorer** (visas ovan) visar en lista över alla datorer som rapporterar till den här arbets ytan. För varje dator visas följande på instrument panelen:

- Totalt antal ändringar som inträffat under den valda tids perioden
- En analys av totalt antal ändringar som fil ändringar eller register ändringar

**Loggs ökningen** öppnas när du anger ett dator namn i Sök fältet eller väljer en dator som visas på fliken datorer. loggs ökning visar alla ändringar som har gjorts under den valda tids perioden för datorn. Du kan expandera en ändring för mer information.

![Loggsökning][8]

Fliken **ändringar** (visas nedan) listar alla ändringar för arbets ytan under den valda tids perioden. För varje entitet som har ändrats visar instrument panelen:

- Dator som ändringen utfördes på
- Typ av ändring (register eller fil)
- Ändrings kategori (ändrad, tillagd, borttagen)
- Datum och tid för ändring

![Ändringar för arbets ytan][9]

**Ändrings information** öppnas när du anger en ändring i Sök fältet eller väljer en entitet som visas på fliken **ändringar** .

![Ändra information][10]

## <a name="edit-monitored-entities"></a>Redigera övervakade entiteter

1. Gå tillbaka till **instrument panelen för fil integritets övervakning** och välj **Inställningar**.

   ![Inställningar][11]

   **Arbets ytans konfiguration** öppnas och visar tre flikar: **Windows-registret**, **Windows-filer**och **Linux-filer**. Varje flik visar de entiteter som du kan redigera i den kategorin. Security Center identifierar om FIM är aktiverat (sant) eller inte aktiverat (falskt) för varje entitet i listan.  Genom att redigera entiteten kan du aktivera eller inaktivera FIM.

   ![Konfiguration av arbets yta][12]

2. Välj ett identitets skydd. I det här exemplet valde vi ett objekt under Windows-registret. **Redigera för ändringsspårning** öppnas.

   ![Redigera eller spåra ändringar][13]

Under **Redigera för ändringsspårning** kan du:

- Aktivera (true) eller inaktivera (falskt) fil integritets övervakning
- Ange eller ändra enhetens namn
- Ange eller ändra värdet eller sökvägen
- Ta bort entiteten, ignorera ändringen eller spara ändringen

## <a name="add-a-new-entity-to-monitor"></a>Lägg till en ny entitet som ska övervakas
1. Gå tillbaka till **instrument panelen för fil integritets övervakning** och välj **Inställningar** högst upp. **Arbets ytans konfiguration** öppnas.
2. Under **arbets ytans konfiguration**väljer du fliken för den typ av enhet som du vill lägga till: Windows-registret, Windows-filer eller Linux-filer. I det här exemplet valde vi **Linux-filer**.

   ![Lägg till ett nytt objekt som ska övervakas][14]

3. Välj **Lägg till**. **Lägg till för ändringsspårning** öppnas.

   ![Ange begärd information][15]

4. Ange den begärda informationen på sidan **Lägg till** och välj **Spara**.

## <a name="disable-monitored-entities"></a>Inaktivera övervakade entiteter
1. Gå tillbaka till instrument panelen för **fil integritets övervakning** .
2. Välj en arbets yta där FIM är aktiverat. En arbets yta är aktive rad för FIM om knappen Aktivera eller uppgraderings plan saknas.

   ![Välj en arbets yta där FIM är aktiverat][16]

3. Under övervakning av fil integritet väljer du **Inställningar**.

   ![Välj inställningar][17]

4. Under **arbets ytans konfiguration**väljer du en grupp där **aktive rad** är inställt på sant.

   ![Konfiguration av arbets yta][18]

5. Under **Redigera för ändringsspårning** Window set **aktiverat** på falskt.

   ![Ange som aktive rad till falskt][19]

6. Välj **Spara**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Övervakning av mappar och Sök vägar med jokertecken

Använd jokertecken för att förenkla spårningen mellan kataloger. Följande regler gäller när du konfigurerar övervakning av mappar med jokertecken:
-   Jokertecken krävs för att spåra flera filer.
-   Jokertecken kan endast användas i det sista segmentet i en sökväg, till exempel C:\folder\file eller/etc/*. conf
-   Om en miljö variabel innehåller en sökväg som inte är giltig kommer verifieringen att lyckas, men sökvägen Miss lyckas När inventeringen körs.
-   När du anger sökvägen bör du undvika allmänna sökvägar, till\*exempel c:. *, vilket leder till att för många mappar passerar.

## <a name="disable-fim"></a>Inaktivera FIM
Du kan inaktivera FIM. FIM använder Azure Ändringsspårning-lösningen för att spåra och identifiera ändringar i din miljö. Genom att inaktivera FIM tar du bort Ändringsspårning-lösningen från den valda arbets ytan.

1. Om du vill inaktivera FIM går du tillbaka till instrument panelen för **fil integritets övervakning** .
2. Välj en arbetsyta.
3. Under **övervakning av fil integritet**väljer du **inaktivera**.

   ![Inaktivera FIM][20]

4. Inaktivera genom att välja **ta bort** .

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att använda FIM (File Integrity Monitoring) i Security Center. Mer information om Security Center finns på följande sidor:

* [Ange säkerhets principer](tutorial-security-policy.md) --lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
* [Hantera säkerhets rekommendationer](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
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