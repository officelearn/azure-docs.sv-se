---
title: Hantera Ändringsspårning och inventering i Azure Automation
description: Den här artikeln beskriver hur du använder Ändringsspårning och inventering för att spåra program-och Microsoft-tjänsteändringar som inträffar i din miljö.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 7dc218416e3b91998a6f06a62670ff8d2158d533
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509163"
---
# <a name="manage-change-tracking-and-inventory"></a>Hantera ändringsspårning och inventering

När du lägger till en ny fil eller register nyckel som ska spåras aktiverar Azure Automation det för funktionen [ändringsspårning och inventering](change-tracking.md) . Den här artikeln innehåller procedurer för att arbeta med den här funktionen.

## <a name="enable-the-full-change-tracking-and-inventory-solution"></a>Aktivera fullständig Ändringsspårning-och inventerings lösning

Om du har aktiverat [Azure Security Center fil integritets övervakning (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)kan du använda den fullständiga ändringsspårning och inventerings lösningen enligt beskrivningen nedan. Inställningarna tas inte bort av den här processen.

> [!NOTE]
> Att aktivera hela Ändringsspårning-och inventerings lösningen kan orsaka ytterligare kostnader. Se [Automation-prissättning](https://azure.microsoft.com/pricing/details/automation/).

1. Ta bort övervaknings lösningen genom att gå till arbets ytan och hitta den i [listan över installerade övervaknings lösningar](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Klicka på namnet på lösningen för att öppna sammanfattnings sidan och klicka sedan på **ta bort**, enligt beskrivningen i [ta bort en övervaknings lösning](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Om du vill aktivera lösningen igen går du till Automation-kontot och väljer **ändrings spårning** under **konfigurations hantering**.
4. Välj Log Analytics arbets yta och Automation-konto, bekräfta inställningarna för arbets ytan och klicka på **Aktivera**.

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>Publicera datorer till Ändringsspårning och inventering

Om du vill börja spåra ändringar måste du aktivera Ändringsspårning och inventering i Azure Automation. Här är de rekommenderade och de sätt som stöds för att publicera dina datorer i den här funktionen: 

* [Publicera från en virtuell dator](automation-onboard-solutions-from-vm.md)
* [Publicera flera datorer från en webbläsare](automation-onboard-solutions-from-browse.md)
* [Publicera från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Publicera i en Azure Automation Runbook](automation-onboard-solutions.md)

## <a name="track-files"></a>Spåra filer

### <a name="configure-file-tracking-on-windows"></a>Konfigurera fil spårning i Windows

Använd följande steg för att konfigurera fil spårning på Windows-datorer:

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. 
2. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
3. På sidan konfiguration av arbets yta väljer du **Windows-filer**och klickar sedan på **+ Lägg** till för att lägga till en ny fil som ska spåras.
4. I fönstret Lägg till Windows-fil för Ändringsspårning anger du informationen för filen som ska spåras och klickar på **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om inställningen tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på filen som ska spåras.        |
    |Grupp     | Ett grupp namn för logisk gruppering av filer.        |
    |Ange sökväg     | Sökvägen för att söka efter filen, till exempel **\\\*c:\Temp. txt**. Du kan också använda miljövariabler, till exempel `%winDir%\System32\\\*.*`.       |
    |Sökvägstyp     | Typ av sökväg. Möjliga värden är fil och katalog.        |    
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
    |Grupp     | Ett grupp namn för logisk gruppering av filer.        |
    |Ange sökväg     | Sökvägen för att söka efter filen, till exempel **/etc/*. conf**.       |
    |Sökvägstyp     | Typ av sökväg. Möjliga värden är fil och katalog.        |
    |Rekursion     | Sant om rekursion används vid sökning efter objektet som ska spåras och falskt annars.        |
    |Använda Sudo     | Sant om du vill använda sudo vid sökning efter objektet och annars FALSE.         |
    |Länkar     | Inställning som avgör hur symboliska länkar ska hanteras när du bläddrar i kataloger. Möjliga värden:<br> IGNORE-ignorerar symboliska länkar och inkluderar inte de filer/kataloger som refereras.<br>Följ – följer de symboliska länkarna under rekursion och inkluderar även de filer/kataloger som refereras till.<br>Hantera – följer de symboliska länkarna och tillåter ändring av returnerat innehåll. **Obs!** det här alternativet rekommenderas inte eftersom det inte stöder hämtning av fil innehåll.    |
    |Ladda upp fil innehåll | Sant om du vill överföra fil innehåll på spårade ändringar och annars FALSE. |

5. Se till att du anger true för **Ladda upp fil innehåll**. Med den här inställningen aktive ras spårning av fil innehåll för den angivna fil Sök vägen.

   ![Lägg till Linux-fil](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Spåra fil innehåll

Med fil innehålls spårning kan du visa innehållet i en fil före och efter en spårad ändring. Funktionen sparar fil innehållet till ett lagrings konto när varje ändring sker. Här följer några regler som du följer när du spårar fil innehåll:

* Ett standard lagrings konto som använder distributions modellen för Resource Manager krävs för att lagra fil innehåll. 

* Använd inte lagrings konton för Premium-och klassiska distributions modeller. Se [om Azure Storage-konton](../storage/common/storage-create-storage-account.md).

* Det lagrings konto som du använder kan endast anslutas till ett Automation-konto.

* [Ändringsspårning och inventering](change-tracking.md) är aktiverat i ditt Automation-konto.

### <a name="enable-tracking-for-file-content-changes"></a>Aktivera spårning av ändringar i fil innehåll

1. I Azure Portal öppnar du ditt Automation-konto och väljer sedan **ändrings spårning** under **konfigurations hantering**.
2. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
3. Välj **fil innehåll** och klicka på **Länka**. Det här alternativet öppnar fönstret Lägg till innehålls plats för Ändringsspårning.

   ![Aktivera innehålls plats](./media/change-tracking-file-contents/enable.png)

4. Välj den prenumeration och det lagrings konto som ska användas för att lagra filens innehåll. 

5. Om du vill aktivera spårning av fil innehåll för alla befintliga spårade filer väljer du **på** för att **Ladda upp fil innehåll för alla inställningar**. Du kan ändra den här inställningen för varje fil Sök väg senare.

   ![Ange lagrings konto](./media/change-tracking-file-contents/storage-account.png)

6. Ändringsspårning och inventering visar URI: er för lagrings konto och signatur för delad åtkomst (SAS) när ändrings spårning av fil innehåll aktive ras. Signaturerna upphör att gälla efter 365 dagar och du kan återskapa dem genom att klicka på **Återskapa**.

   ![Lista konto nycklar](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visa innehållet i en spårad fil

När Ändringsspårning och inventeringen upptäcker en ändring av en spårad fil kan du Visa filens innehåll i fönstret ändra information.  

![Lista ändringar](./media/change-tracking-file-contents/change-list.png)

1. I Azure Portal öppnar du ditt Automation-konto och väljer sedan **ändrings spårning** under **konfigurations hantering**.

2. Välj en fil i listan över ändringar och välj **Visa fil innehålls ändringar** för att se innehållet i filen. I fönstret ändrings information visas fil informationen standard före och efter.

   ![Ändra information](./media/change-tracking-file-contents/change-details.png)

3. Du visar fil innehållet i en sida-vid-sida-vy. Du kan välja **infogade** för att visa en infogad vy över ändringarna.

## <a name="track-registry-keys"></a>Spåra register nycklar

Använd följande steg för att konfigurera register nyckel spårning på Windows-datorer:

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. 
2. Klicka på **Redigera inställningar** (kugg hjuls symbolen).
3. På sidan konfiguration av arbets yta väljer du **Windows-registret**.
4. Klicka på **+ Lägg** till för att lägga till en ny register nyckel som ska spåras.
5. I fönstret Lägg till Windows-register för Ändringsspårning anger du informationen för nyckeln som ska spåras och klickar sedan på **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om en inställning tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på register nyckeln som ska spåras.        |
    |Grupp     | Grupp namn för register nycklar för logisk gruppering.        |
    |Windows-registernyckel   | Nyckel namn med sökväg, till exempel **HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\explorer\user Shell Folders\Common Startup**.      |

## <a name="search-logs-for-change-records"></a>Sök efter loggar för ändrings poster

Du kan utföra olika sökningar mot Azure Monitor loggar för ändrings poster. När sidan ändrings spårning är öppen klickar du på **Log Analytics** för att öppna sidan loggar. Följande tabell innehåller exempel på loggs ökningar för ändrings poster.

|Söka i data  |Beskrivning  |
|---------|---------|
|ConfigurationData<br>&#124; där ConfigDataType = = "Microsoft-tjänster" och SvcStartupType = = Auto "<br>&#124; där SvcState = = "stoppad"<br>&#124; sammanfatta arg_max (TimeGenerated, *) av SoftwareName, dator         | Visar de senaste inventerings posterna för Microsoft-tjänster som har ställts in på auto men som har rapporter ATS som stoppade. Resultaten är begränsade till den senaste posten för det angivna program namnet och datorn.    |
|ConfigurationChange<br>&#124; där ConfigChangeType = = "Software" och ChangeCategory = = "borttaget"<br>&#124; order by TimeGenerated DESC|Visar ändrings poster för borttagen program vara.|

## <a name="create-alerts-on-changes"></a>Skapa aviseringar vid ändringar

I följande exempel visas att filen **C:\Windows\System32\drivers\etc\hosts** har ändrats på en dator. Filen är viktig eftersom Windows använder den för att matcha värdnamn med IP-adresser. Den här åtgärden åsidosätter DNS och kan leda till anslutnings problem. Det kan också leda till omdirigering av trafik till skadliga eller på annat sätt farliga webbplatser.

![Ett diagram som visar fil ändringen för hosts](./media/change-tracking-file-contents/changes.png)

Vi ska använda det här exemplet för att diskutera stegen för att skapa aviseringar vid en ändring.

1. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**och väljer sedan **Log Analytics**. 
2. Leta efter innehålls ändringar i **värd** filen med frågan `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`i loggar Sök. Den här frågan söker efter en innehålls ändring för filer med en fullständigt kvalificerad sökväg som innehåller ordet "hosts". Du kan också fråga efter en viss fil genom att ändra Sök vägs delen till dess fullständigt kvalificerade form, till exempel med `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`hjälp av.

3. När frågan returnerar önskade resultat klickar du på **ny varnings regel** i logg sökningen för att öppna sidan Skapa avisering. Du kan också navigera till den här sidan via **Azure Monitor** i Azure Portal. 

4. Kontrol lera frågan igen och ändra aviserings logiken. I det här fallet vill du att aviseringen ska utlösas om det inte finns någon ändring som upptäcks på alla datorer i miljön.

    ![Ändra till fråga för att spåra ändringar av hosts-filen](./media/change-tracking-file-contents/change-query.png)

5. När aviserings logiken har angetts tilldelar du åtgärds grupper för att utföra åtgärder som svar på aviseringen som utlöses. I det här fallet ställer vi in e-postmeddelanden som ska skickas och ett ITSM-kvitto (IT Service Management) skapas. 

    ![Konfigurerar åtgärds gruppen för avisering vid ändring](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Nästa steg

* Grundläggande information om Ändringsspårning och inventering finns i [Översikt över ändringsspårning och inventering](change-tracking.md).
* Information om hur du felsöker ändringar för en virtuell Azure-dator finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).
* Använd [loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md) om du vill visa detaljerade ändrings spårnings data.