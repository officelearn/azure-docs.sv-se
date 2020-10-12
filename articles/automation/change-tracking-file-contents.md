---
title: Hantera Ändringsspårning och inventering i Azure Automation
description: Den här artikeln beskriver hur du använder Ändringsspårning och inventering för att spåra program-och Microsoft-tjänsteändringar i din miljö.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185610"
---
# <a name="manage-change-tracking-and-inventory"></a>Hantera ändringsspårning och inventering

När du lägger till en ny fil eller register nyckel som ska spåras aktiverar Azure Automation för [ändringsspårning och inventering](change-tracking.md). Den här artikeln beskriver hur du konfigurerar spårning, granskar spårnings resultat och hanterar aviseringar när ändringar upptäcks.

Innan du använder procedurerna i den här artikeln bör du kontrol lera att du har aktiverat Ändringsspårning och inventering på dina virtuella datorer med någon av följande metoder:

* [Aktivera Ändringsspårning och inventering från ett Automation-konto](automation-enable-changes-from-auto-acct.md)
* [Aktivera Ändringsspårning och inventering genom att bläddra i Azure Portal](automation-enable-changes-from-browse.md)
* [Aktivera Ändringsspårning och inventering från en runbook](automation-enable-changes-from-runbook.md)
* [Aktivera Ändringsspårning och inventering från en virtuell Azure-dator](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Begränsa omfånget för distributionen

Ändringsspårning och inventering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska ta emot ändringar. Mer information finns i [begränsa ändringsspårning och distributions omfång för lager](automation-scope-configurations-change-tracking.md).

## <a name="track-files"></a>Spåra filer

Du kan använda Ändringsspårning och inventering för att spåra ändringar i filer och mappar/kataloger. Det här avsnittet beskriver hur du konfigurerar fil spårning i Windows och på Linux.

### <a name="configure-file-tracking-on-windows"></a>Konfigurera fil spårning i Windows

Använd följande steg för att konfigurera fil spårning på Windows-datorer:

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. 
2. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
3. På sidan konfiguration av arbets yta väljer du **Windows-filer**och klickar sedan på **+ Lägg** till för att lägga till en ny fil som ska spåras.
4. I fönstret Lägg till Windows-fil för Ändringsspårning anger du informationen för filen eller mappen som ska spåras och klickar på **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om inställningen tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på filen som ska spåras.        |
    |Group     | Ett grupp namn för logisk gruppering av filer.        |
    |Ange sökväg     | Sökvägen för att söka efter filen, till exempel **c:\Temp \\ \* . txt**. Du kan också använda miljövariabler, till exempel `%winDir%\System32\\\*.*` .       |
    |Sökvägstyp     | Typ av sökväg. Möjliga värden är File och Folder.        |    
    |Rekursion     | Sant om rekursion används vid sökning efter objektet som ska spåras och falskt annars.        |    
    |Ladda upp fil innehåll | Sant om du vill överföra fil innehåll på spårade ändringar och annars FALSE.|

5. Se till att du anger true för **Ladda upp fil innehåll**. Med den här inställningen aktive ras spårning av fil innehåll för den angivna fil Sök vägen.

### <a name="configure-file-tracking-on-linux"></a>Konfigurera fil spårning i Linux

Använd följande steg för att konfigurera fil spårning på Linux-datorer:

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. 
2. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
3. På sidan konfiguration av arbets yta väljer du **Linux-filer**och klickar sedan på **+ Lägg** till för att lägga till en ny fil som ska spåras.
4. I fönstret Lägg till Linux-fil för Ändringsspårning anger du informationen för filen eller katalogen som ska spåras och klickar på **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om inställningen tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på filen som ska spåras.        |
    |Group     | Ett grupp namn för logisk gruppering av filer.        |
    |Ange sökväg     | Sökvägen för att söka efter filen, till exempel **/etc/*. conf**.       |
    |Sökvägstyp     | Typ av sökväg. Möjliga värden är fil och katalog.        |
    |Rekursion     | Sant om rekursion används vid sökning efter objektet som ska spåras och falskt annars.        |
    |Använda Sudo     | Sant om du vill använda sudo vid sökning efter objektet och annars FALSE.         |
    |Länkar     | Inställning som avgör hur symboliska länkar ska hanteras när du bläddrar i kataloger. Möjliga värden:<br> IGNORE-ignorerar symboliska länkar och inkluderar inte de filer/kataloger som refereras.<br>Följ – följer de symboliska länkarna under rekursion och inkluderar även de filer/kataloger som refereras till.<br>Hantera – följer de symboliska länkarna och tillåter ändring av returnerat innehåll.<br>**Obs:** Alternativet hantera rekommenderas inte eftersom det inte stöder hämtning av fil innehåll.    |
    |Ladda upp fil innehåll | Sant om du vill överföra fil innehåll på spårade ändringar och annars FALSE. |

5. Se till att du anger true för **Ladda upp fil innehåll**. Med den här inställningen aktive ras spårning av fil innehåll för den angivna fil Sök vägen.

   ![Lägg till Linux-fil](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Spåra fil innehåll

Med fil innehålls spårning kan du visa innehållet i en fil före och efter en spårad ändring. Funktionen sparar fil innehållet till ett [lagrings konto](../storage/common/storage-account-overview.md) när varje ändring sker. Här följer några regler som du följer när du spårar fil innehåll:

* Ett standard lagrings konto som använder distributions modellen för Resource Manager krävs för att lagra fil innehåll. 
* Använd inte lagrings konton för Premium-och klassiska distributions modeller. Se [om Azure Storage-konton](../storage/common/storage-account-create.md).
* Du kan bara ansluta lagrings kontot till ett Automation-konto.
* [Ändringsspårning och inventering](change-tracking.md) måste vara aktiverat i ditt Automation-konto.

### <a name="enable-tracking-for-file-content-changes"></a>Aktivera spårning av ändringar i fil innehåll

Använd följande steg för att aktivera spårning av ändringar i fil innehåll:

1. I Azure Portal öppnar du ditt Automation-konto och väljer sedan **ändrings spårning** under **konfigurations hantering**.
2. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
3. Välj **fil innehåll** och klicka på **Länka**. Det här alternativet öppnar fönstret Lägg till innehålls plats för Ändringsspårning.

   ![Lägg till innehålls plats](./media/change-tracking-file-contents/enable.png)

4. Välj den prenumeration och det lagrings konto som ska användas för att lagra filens innehåll. 

5. Om du vill aktivera spårning av fil innehåll för alla befintliga spårade filer väljer du **på** för att **Ladda upp fil innehåll för alla inställningar**. Du kan ändra den här inställningen för varje fil Sök väg senare.

   ![Ange lagrings konto](./media/change-tracking-file-contents/storage-account.png)

6. Ändringsspårning och inventering visar URI: er för lagrings konto och signatur för delad åtkomst (SAS) när ändrings spårning av fil innehåll aktive ras. Signaturerna upphör att gälla efter 365 dagar och du kan återskapa dem genom att klicka på **Återskapa**.

   ![Lista konto nycklar](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visa innehållet i en spårad fil

När Ändringsspårning och inventeringen upptäcker en ändring av en spårad fil kan du Visa filens innehåll i fönstret ändra information.  

![Lista ändringar](./media/change-tracking-file-contents/change-list.png)

1. I Azure Portal öppnar du ditt Automation-konto och väljer sedan **ändrings spårning** under **konfigurations hantering**.

2. Välj en fil i listan över ändringar och välj **Visa fil innehålls ändringar** för att se innehållet i filen. I fönstret ändrings information visas fil informationen standard före och efter för varje egenskap.

   ![Ändra information](./media/change-tracking-file-contents/change-details.png)

3. Du visar fil innehållet i en sida-vid-sida-vy. Du kan välja **infogade** för att visa en infogad vy över ändringarna.

## <a name="track-registry-keys"></a>Spåra register nycklar

Använd följande steg för att konfigurera register nyckel spårning på Windows-datorer:

1. I Azure Portal öppnar du ditt Automation-konto och väljer sedan **ändrings spårning** under **konfigurations hantering**. 
2. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
3. På sidan konfiguration av arbets yta väljer du **Windows-registret**.
4. Klicka på **+ Lägg** till för att lägga till en ny register nyckel som ska spåras.
5. I fönstret Lägg till Windows-register för Ändringsspårning anger du informationen för nyckeln som ska spåras och klickar sedan på **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om en inställning tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på register nyckeln som ska spåras.        |
    |Group     | Grupp namn för register nycklar för logisk gruppering.        |
    |Windows-registernyckel   | Nyckel namn med sökväg, till exempel `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Sök efter loggar för ändrings poster

Du kan utföra olika sökningar mot Azure Monitor loggar för ändrings poster. När sidan ändrings spårning är öppen klickar du på **Log Analytics** för att öppna sidan loggar. Följande tabell innehåller exempel på loggs ökningar för ändrings poster.

|Söka i data  |Beskrivning  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Visar de senaste inventerings posterna för Microsoft-tjänster som har ställts in på auto men som har rapporter ATS som stoppade. Resultaten är begränsade till den senaste posten för det angivna program namnet och datorn.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Visar ändrings poster för borttagen program vara.|

## <a name="create-alerts-on-changes"></a>Skapa aviseringar vid ändringar

I följande exempel visas att filen **c:\Windows\System32\drivers\etc\hosts** har ändrats på en dator. Filen är viktig eftersom Windows använder den för att matcha värdnamn med IP-adresser. Den här åtgärden åsidosätter DNS och kan leda till anslutnings problem. Det kan också leda till omdirigering av trafik till skadliga eller på annat sätt farliga webbplatser.

![Diagram som visar ändringar i värd filen](./media/change-tracking-file-contents/changes.png)

Vi ska använda det här exemplet för att diskutera stegen för att skapa aviseringar vid en ändring.

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**och väljer sedan **Log Analytics**. 
2. Leta efter innehålls ändringar i **värd** filen med frågan i loggar Sök `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Den här frågan söker efter innehålls ändringar för filer med fullständigt kvalificerade Sök vägs namn som innehåller ordet `hosts` . Du kan också fråga efter en viss fil genom att ändra Sök vägs delen till dess fullständigt kvalificerade form, till exempel med hjälp av `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. När frågan returnerar resultatet klickar du på **ny varnings regel** i logg sökningen för att öppna sidan Skapa avisering. Du kan också navigera till den här sidan via **Azure Monitor** i Azure Portal. 

4. Kontrol lera frågan igen och ändra aviserings logiken. I det här fallet vill du att aviseringen ska utlösas om det inte finns någon ändring som upptäcks på alla datorer i miljön.

    ![Ändra till fråga för att spåra ändringar av hosts-filen](./media/change-tracking-file-contents/change-query.png)

5. När aviserings logiken har angetts tilldelar du åtgärds grupper för att utföra åtgärder som svar på utlösningen av aviseringen. I det här fallet ställer vi in e-postmeddelanden som ska skickas och ett ITSM-kvitto (IT Service Management) skapas. 

    ![Konfigurerar åtgärds gruppen för avisering vid ändring](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Nästa steg

* Information om scope-konfigurationer finns i [begränsa ändringsspårning och distributions omfång för lager](automation-scope-configurations-change-tracking.md).
* Om du behöver söka i loggar som lagras i din Log Analytics-arbetsyta, se [loggs ökningar i Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md).
* Om du är färdig med distributioner kan du läsa [ta bort arbets ytan från Automation-kontot för ändringsspårning och inventering](automation-unlink-workspace-change-tracking.md).
* Information om hur du tar bort virtuella datorer från Ändringsspårning och inventering finns i [ta bort virtuella datorer från ändringsspårning och inventering](automation-remove-vms-from-change-tracking.md).
* Information om hur du felsöker funktions fel finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).
