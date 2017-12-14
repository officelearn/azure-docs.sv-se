---
title: "Använda JSON-formaterad taggar för att schemalägga tillstånd för virtuell dator i Azure | Microsoft Docs"
description: "Den här artikeln visar hur du använder JSON strängar i taggar för att automatisera schemaläggningen av VM-start och stopp."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
ms.openlocfilehash: 9855921f4a3aa9cda8497b400d50a186d7162dc3
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Azure Automation-scenario: med JSON-formaterad taggar för att skapa ett schema för Virtuella Azure-start och stopp
Kunder vill ofta schemalägga start och stopp av virtuella datorer för att minska kostnaderna för prenumerationen eller stöder affärsmässiga och tekniska krav.

Följande scenario kan du konfigurera automatisk start och stopp av dina virtuella datorer med hjälp av en tagg som kallas schemat på en resursgruppsnivå eller virtuell dator i Azure. Det här schemat kan konfigureras från söndag till lördag med ett starttiden och avstängning tid.

Vi har några out box-alternativ. Exempel på dessa är:

* [Skaluppsättningar för den virtuella datorn](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) med Autoskala inställningar som gör att du kan skala in eller ut.
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) -tjänsten som har inbyggda möjligheterna för schemaläggning av åtgärder för start och stopp.

Dessa alternativ stöder dock endast specifika scenarier och kan inte användas för infrastruktur-som-en-tjänst (IaaS) virtuella datorer.

När taggen schema används för en resursgrupp, används den även för alla virtuella datorer i resursgruppen. Om ett schema används också direkt till en virtuell dator, företräde senaste schemat filer i följande ordning:

1. Schema som tillämpas på en resursgrupp
2. Schema som tillämpas på en resursgrupp och en virtuell dator i resursgruppen.
3. Schema som tillämpas på en virtuell dator

Det här scenariot huvudsakligen tar en JSON-sträng med ett bestämt format och lägger till den som värde för en tagg som kallas schema. Sedan en runbook visas alla resursgrupper och virtuella datorer och identifierar scheman för varje virtuell dator baserat på de scenarier som beskrivs ovan. Därefter loop genom de virtuella datorer som har scheman som är anslutna och utvärderar vilka måste åtgärdas. Till exempel avgör vilka virtuella datorer måste stoppas, stänga av eller ignoreras.

Dessa runbooks autentisera med hjälp av den [Azure kör som-konto](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Hämta runbooks för scenariot
Det här scenariot består av fyra runbooks i PowerShell-arbetsflöde som du kan hämta från den [TechNet-galleriet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) eller [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) databasen för det här projektet.

| Runbook | Beskrivning |
| --- | --- |
| Testa ResourceSchedule |Kontrollerar schema för varje virtuell dator och utför stängs av eller startas beroende på schemat. |
| Lägg till ResourceSchedule |Lägger till taggen schema till en virtuell dator eller resurs-grupp. |
| Uppdatera ResourceSchedule |Ändrar taggen befintliga schema genom att ersätta den med en ny. |
| Ta bort ResourceSchedule |Tar bort taggen schema från en virtuell dator eller resurs-grupp. |

## <a name="install-and-configure-this-scenario"></a>Installera och konfigurera det här scenariot
### <a name="install-and-publish-the-runbooks"></a>Installera och publicera runbooks
När du har hämtat runbooks, kan du importera dem med hjälp av proceduren i [skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Publicera varje runbook när den har importerats till ditt Automation-konto.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Lägga till ett schema ResourceSchedule Test-runbook
Följ dessa steg om du vill aktivera ett schema för ResourceSchedule Test-runbook. Det här är den runbook som verifierar vilka virtuella datorer ska startas, stänga av eller kvar.

1. Öppna ditt Automation-konto i Azure Portal och klicka sedan på den **Runbooks** panelen.
2. På den **Test ResourceSchedule** bladet, klickar du på den **scheman** panelen.
3. På den **scheman** bladet, klickar du på **lägga till ett schema**.
4. På den **scheman** bladet väljer **länka ett schema till din runbook**. Välj sedan **skapa ett nytt schema**.
5. På den **nytt schema** bladet, Skriv namnet på det här schemat, till exempel: *HourlyExecution*.
6. För schemat **starta**, inställning starttiden en timme.
7. Välj **återkommande**, och sedan för **upprepas varje intervall**väljer **1 timme**.
8. Kontrollera att **ställa in ett utgångsdatum** är inställd på **nr**, och klicka sedan på **skapa** att spara ditt nya schema.
9. På den **schema Runbook** alternativ bladet väljer **parametrar och körinställningar**. I Test-ResourceSchedule **parametrar** bladet anger du namnet på din prenumeration i den **SubscriptionName** fältet.  Det här är den enda parameter som krävs för runbook.  När du är klar klickar du på **OK**.

Schemat för runbook bör se ut ungefär så här när den har slutförts:

![Konfigurerade ResourceSchedule Test-runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>Formatera JSON-strängen
Den här lösningen i grunden tar en JSON sträng med ett bestämt format och lägger till den som värde för en tagg kallas du schema. En runbook visas alla resursgrupper och virtuella datorer och identifierar scheman för varje virtuell dator.

Runbook slinga över virtuella datorer som har kopplade scheman och kontrollerar du vilka åtgärder som ska vidtas. Följande är ett exempel på hur lösningarna som ska formateras:

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

Här är några detaljerad information om den här strukturen:

1. Formatet för den här JSON-strukturen är optimerad för att undvika 256 tecken begränsning av ett enda Taggvärde i Azure.
2. *TzId* representerar tidszonen för den virtuella datorn. Detta ID kan erhållas med hjälp av TimeZoneInfo .NET-klass i ett PowerShell-session--**[System.TimeZoneInfo]:: GetSystemTimeZones()**.

   ![GetSystemTimeZones i PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * Veckodagar representeras med ett numeriskt värde på noll till sex. Värdet noll är lika med söndag.
   * Starttiden representeras med den **S** attributet och dess värde är i ett 24-timmarsformat.
   * End eller avstängning tid representeras med den **E** attributet och dess värde är i ett 24-timmarsformat.

     Om den **S** och **E** attribut varje har värdet noll (0), den virtuella datorn att finnas kvar i dess nuvarande tillstånd vid tiden för utvärderingen.
3. Om du vill hoppa över utvärderingen för en viss dag i veckan Lägg inte till ett avsnitt för dagen i veckan. I följande exempel endast måndag utvärderas och andra dagar i veckan ignoreras:

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>Taggen resursgrupper eller virtuella datorer
Om du vill stänga av virtuella datorer, måste du tagga de virtuella datorerna eller resursgrupper där de är placerade. Virtuella datorer som inte har en schema-tagg utvärderas inte. Därför är inte de starta eller stänga av.

Det finns två sätt att taggen resursgrupper eller virtuella datorer med den här lösningen. Du kan göra det direkt från portalen. Eller så kan du använda Lägg till ResourceSchedule, Update-ResourceSchedule och ta bort ResourceSchedule runbooks.

### <a name="tag-through-the-portal"></a>Tagga via portalen
Följ dessa steg för att tagga en virtuell dator eller resursgrupp i portalen:

1. Förenkla JSON-strängen och kontrollera att det inte finns några blanksteg.  JSON-strängen ska se ut så här:

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. Välj den **taggen** ikonen för en virtuell dator eller resurs grupp för att tillämpa det här schemat.

   ![Alternativet för VM-tagg](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. Taggar har definierats efter ett nyckel/värde-par. Typen **schema** i den **nyckeln** fältet och klistra in JSON-strängen i den **värdet** fältet. Klicka på **Spara**. Din nya taggen bör nu visas i listan med taggar för din resurs.

   ![Taggen för VM-schema](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>Taggen från PowerShell
Alla importerade runbooks innehåller hjälpinformation i början av skriptet som beskriver hur du kör runbooks direkt från PowerShell. Du kan anropa runbooks Lägg till ScheduleResource och uppdatera ScheduleResource från PowerShell. Det gör du genom att ange nödvändiga parametrar att skapa eller uppdatera taggen schemat på en virtuell dator eller resurs utanför portalen.

För att skapa, lägga till och ta bort taggar via PowerShell, först [konfigurera din PowerShell-miljö för Azure](/powershell/azure/overview). När du har slutfört installationen, kan du fortsätta med följande steg.

### <a name="create-a-schedule-tag-with-powershell"></a>Skapa en schema-tagg med PowerShell
1. Öppna ett PowerShell-session. Använd sedan följande exempel autentisera med Kör som-konto och ange en prenumeration:

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Definiera ett schema för hash-tabell. Här är ett exempel på hur det ska konstrueras:

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. Definiera parametrar som krävs av runbook. I följande exempel tar utvecklar vi en virtuell dator:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    Om du ange en resursgrupp, ta bort den *VMName* parametern från $params hash-tabell enligt följande:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. Kör Lägg till ResourceSchedule runbook med följande parametrar för att skapa taggen schema:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. Om du vill uppdatera en resursgrupp eller virtuella tagg, köra den **uppdatering ResourceSchedule** runbook med följande parametrar:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>Ta bort en schema-tagg med PowerShell
1. Öppna ett PowerShell-session och kör följande om du vill autentisera med Kör som-konto och välj och ange en prenumeration:

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Definiera parametrar som krävs av runbook. I följande exempel tar utvecklar vi en virtuell dator:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    Om du tar bort en tagg från en resursgrupp, ta bort den *VMName* parametern från $params hash-tabell enligt följande:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Kör Remove-ResourceSchedule runbook för att ta bort taggen schema:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. Om du vill uppdatera en resursgrupp eller virtuella tagg, kör du ta bort ResourceSchedule runbook med följande parametrar:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> Vi rekommenderar att du Proaktiv övervakning av dessa runbooks (och tillstånd för virtuell dator) för att kontrollera att de virtuella datorerna stänga ned och starta därefter.
>

Om du vill visa information om runbook-jobbet Test ResourceSchedule i Azure portal, Välj den **jobb** sida vid sida av runbook. Jobbsammanfattningen visar indataparametrarna och utdataströmmen samt allmän information om jobbet och eventuella undantag som uppstår.

**Jobbsammanfattning** innehåller meddelanden från strömmarna utdata, varning och fel. Välj **Utdata** om du vill visa detaljerade resultat från runbook-körningen.

![Testa ResourceSchedule utdata](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>Nästa steg
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md).
* Mer information om runbook-typer och sina fördelar och begränsningar finns [Azure Automation runbook-typer](automation-runbook-types.md).
* Mer information om PowerShell-skript stödfunktioner finns [stöd för intern PowerShell-skriptet i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Läs mer om runbook-loggning och utdata i [Runbook-utdata och meddelanden i Azure Automation](automation-runbook-output-and-messages.md).
* Läs mer om Azure kör som-konto och hur du autentiserar dina runbooks genom att använda det i [autentisera runbooks med Kör som-kontot Azure](automation-sec-configure-azure-runas-account.md).
