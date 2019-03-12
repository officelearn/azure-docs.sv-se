---
title: Samla in Log Analytics-data med en runbook i Azure Automation | Microsoft Docs
description: Stegvis självstudie som visar hur du skapar en runbook i Azure Automation för att samla in data till databasen för analys av Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 4f6076407ea4745556e59d44bd37ab85288e6bd2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772946"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Samla in data i Log Analytics med en Azure Automation-runbook

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan samla in en betydande mängd data i Log Analytics från olika källor, inklusive [datakällor](../../azure-monitor/platform/agent-data-sources.md) på agenter och även [data som samlas in från Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md). Det finns en scenarier om när du behöver samla in data som inte är tillgängliga via dessa källor som standard. I dessa fall kan du använda den [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) att skriva data till Log Analytics från en REST API-klient. En vanlig metod att utföra den här Datasamlingen med hjälp av en runbook i Azure Automation.

Den här självstudien beskriver steg för att skapa och schemalägga en runbook i Azure Automation för att skriva data till Log Analytics.

## <a name="prerequisites"></a>Förutsättningar
Det här scenariot kräver följande resurser som konfigurerats i din Azure-prenumeration. Båda kan vara ett gratiskonto.

- [Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md).
- [Azure automation-konto](../..//automation/automation-quickstart-create-account.md).

## <a name="overview-of-scenario"></a>Översikt över scenariot
Den här självstudien skriver du en runbook som samlar in information om Automation-jobb. Azure Automation-Runbooks implementeras med PowerShell, så att du börjar med att skriva och testa ett skript i Azure Automation-redigeraren. När du har kontrollerat att du samlar in informationen som krävs kan du skriva dessa data till Log Analytics och verifiera en anpassad datatyp. Slutligen ska du skapa ett schema för att starta runbooken med jämna mellanrum.

> [!NOTE]
> Du kan konfigurera Azure Automation för att skicka jobbinformation till Log Analytics utan denna runbook. Det här scenariot används främst för självstudien och vi rekommenderar att du skickar data till en test-arbetsyta.

## <a name="1-install-data-collector-api-module"></a>1. Installera Data Collector API-modulen
Varje [begäran från HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md#create-a-request) måste formateras på rätt sätt och innehåller ingen auktoriseringsrubrik. Du kan göra detta i din runbook, men du kan minska mängden kod som krävs med hjälp av en modul som förenklar processen. En modul som du kan använda är [OMSIngestionAPI modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI) i PowerShell-galleriet.

Du använder en [modulen](../../automation/automation-integration-modules.md) i en runbook måste den installeras i ditt Automation-konto.  Valfri runbook i samma konto kan sedan använda funktionerna i modulen. Du kan installera en ny modul genom att välja **tillgångar** > **moduler** > **lägger till en modul** i ditt Automation-konto.

PowerShell-galleriet ger dig dock ett snabbt alternativ att distribuera en modul direkt till ditt automation-konto så att du kan använda alternativet för den här självstudiekursen.

![OMSIngestionAPI modul](media/runbook-datacollect/OMSIngestionAPI.png)

1. Gå till [PowerShell-galleriet](https://www.powershellgallery.com/).
2. Sök efter **OMSIngestionAPI**.
3. Klicka på den **distribuera till Azure Automation** knappen.
4. Välj ditt automation-konto och klicka på **OK** att installera modulen.

## <a name="2-create-automation-variables"></a>2. Skapa Automation-variabler
[Automationsvariabler](../../automation/automation-variables.md) innehålla värden som kan användas av alla runbooks i ditt Automation-konto. De gör runbooks mer flexibel genom att du kan ändra dessa värden utan att redigera den faktiska runbooken. Varje begäran från HTTP Data Collector API kräver ID och nyckel för Log Analytics-arbetsytan och variabler för tillgångar är perfekt för att lagra den här informationen.

![Variabler](media/runbook-datacollect/variables.png)

1. Gå till ditt Automation-konto i Azure-portalen.
2. Välj **variabler** under **delade resurser**.
2. Klicka på **Lägg till en variabel** och skapa de två variablerna i följande tabell.

| Egenskap  | ID-värdet för arbetsyta | Nyckelvärdet för arbetsyta |
|:--|:--|:--|
| Namn | WorkspaceId | WorkspaceKey |
| Type | String | String |
| Värde | Klistra in arbetsyte-ID för Log Analytics-arbetsytan. | Klistra in med primärt eller sekundärnyckeln för Log Analytics-arbetsytan. |
| Krypterade | Nej | Ja |

## <a name="3-create-runbook"></a>3. Skapa runbook

Azure Automation har en redigerare i portalen där du kan redigera och testa din runbook. Har du möjlighet att använda Skriptredigeraren för att arbeta med [PowerShell direkt](../../automation/automation-edit-textual-runbook.md) eller [skapa en grafisk runbook](../../automation/automation-graphical-authoring-intro.md). I den här självstudiekursen kommer du arbeta med ett PowerShell-skript.

![Redigera runbooken](media/runbook-datacollect/edit-runbook.png)

1. Gå till ditt Automation-konto.
2. Klicka på **Runbooks** > **Lägg till en runbook** > **skapa en ny runbook**.
3. Runbook-namn skriver du in **Collect-Automation-jobb**. Typ av runbook, Välj **PowerShell**.
4. Klicka på **skapa** att skapa runbooken och starta redigeraren.
5. Kopiera och klistra in följande kod i runbooken. Referera till kommentarerna i skriptet förklaring av koden.
    ```
    # Get information required for the automation account from parameter values when the runbook is started.
    Param
    (
        [Parameter(Mandatory = $True)]
        [string]$resourceGroupName,
        [Parameter(Mandatory = $True)]
        [string]$automationAccountName
    )
    
    # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
    # Code copied from the runbook AzureAutomationTutorial.
    $connectionName = "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
    Connect-AzAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    
    # Set the $VerbosePreference variable so that we get verbose output in test environment.
    $VerbosePreference = "Continue"
    
    # Get information required for Log Analytics workspace from Automation variables.
    $customerId = Get-AutomationVariable -Name 'WorkspaceID'
    $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
    
    # Set the name of the record type.
    $logType = "AutomationJob"
    
    # Get the jobs from the past hour.
    $jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
    
    if ($jobs -ne $null) {
        # Convert the job data to json
        $body = $jobs | ConvertTo-Json
    
        # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
        Write-Verbose $body
    
        # Send the data to Log Analytics.
        Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
    }
    ```

## <a name="4-test-runbook"></a>4. Test-runbook
Azure Automation innehåller en miljö till [testet](../../automation/automation-testing-runbook.md) innan du publicerar den. Du kan granska de data som samlas in av runbook och verifiera att den skriver till Log Analytics som förväntat innan du publicerar den till produktion.

![Test-runbook](media/runbook-datacollect/test-runbook.png)

6. Klicka på **spara** att spara runbook.
1. Klicka på **Testfönster** att öppna runbooken i testmiljön.
3. Eftersom din runbook har parametrar uppmanas du att ange värden för dessa. Ange namnet på resursgruppen och automation-konto som kommer att samla in jobbinformation från.
4. Klicka på **starta** i början av runbooken.
3. Runbooken startar med statusen **i kö** innan den går att **kör**.
3. Runbook ska visa utförliga utdata med de jobb som samlas in i json-format. Om inga jobb visas sedan kanske har inga jobb som skapats i automation-kontot under den senaste timmen. Försök att starta en runbook i automation-kontot och utför sedan testet igen.
4. Se till att utdata inte visar eventuella fel i kommandot inlägg till Log Analytics. Du bör ha ett meddelande som liknar följande.

    ![Publicera utdata](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Kontrollera posterna i Log Analytics
När runbooken har slutförts i testet, och du har kontrollerat att utdata togs emot, kan du verifiera att posterna har skapats med en [loggsökning i Log Analytics](../../azure-monitor/log-query/log-query-overview.md).

![Loggutdata](media/runbook-datacollect/log-output.png)

1. Välj Log Analytics-arbetsytan i Azure-portalen.
2. Klicka på **Loggsöknings**.
3. Skriv följande kommando `Type=AutomationJob_CL` och klickar på sökknappen. Observera att posttypen innehåller _CL som inte anges i skriptet. Det suffixet läggs automatiskt till loggtyp som visar att det är en typ av anpassad logg.
4. Du bör se en eller flera poster returneras som anger att runbooken fungerar som förväntat.

## <a name="6-publish-the-runbook"></a>6. Publicera en runbook
När du har kontrollerat att runbooken fungerar korrekt, måste du publicera den så att du kan köra den i produktion. Du kan fortsätta att redigera och testa runbooken utan att ändra den publicerade versionen.

![Publicera runbook](media/runbook-datacollect/publish-runbook.png)

1. Gå tillbaka till ditt automation-konto.
2. Klicka på **Runbooks** och välj **Collect-Automation-jobb**.
3. Klicka på **redigera** och sedan **publicera**.
4. Klicka på **Ja** när du ombeds bekräfta att du vill skriva över den tidigare publicerade versionen.

## <a name="7-set-logging-options"></a>7. Ange alternativ för loggning
För testning, kunde du se [utförliga utdata](../../automation/automation-runbook-output-and-messages.md#message-streams) eftersom du har angett variabeln $VerbosePreference i skriptet. För produktion måste du ange loggningsegenskaperna för en runbook om du vill visa utförliga utdata. För den runbook som används i den här självstudien visas json-data som skickas till Log Analytics.

![Loggning och spårning](media/runbook-datacollect/logging.png)

1. I egenskaperna för din runbook **loggning och spårning** under **Runbookinställningar**.
2. Ändra inställningen för **logga utförliga poster** till **på**.
3. Klicka på **Spara**.

## <a name="8-schedule-runbook"></a>8. Schemalägg runbook
Det vanligaste sättet att starta en runbook som samlar in övervakningsdata är att schemalägga den så att de körs automatiskt. Du kan göra detta genom att skapa en [schema i Azure Automation](../../automation/automation-schedules.md) och kopplar den till din runbook.

![Schemalägg runbook](media/runbook-datacollect/schedule-runbook.png)

1. I egenskaperna för din runbook, Välj **scheman** under **resurser**.
2. Klicka på **lägga till ett schema** > **länka ett schema till din runbook** > **skapa ett nytt schema**.
5. Skriv in följande värden för schemat och klicka på **skapa**.

| Egenskap  | Värde |
|:--|:--|
| Namn | Varje AutomationJobs timme |
| Startar | Välj när som helst på minst 5 minuter efter aktuell tid. |
| Upprepning | Återkommande |
| Återkommer varje | 1 timme |
| Ange förfallodatum | Nej |

När schemat skapas, måste du ange de parametervärden som ska användas varje gång som det här schemat startar runbook.

6. Klicka på **konfigurera parametrar och körningsinställningar**.
7. Fyll i värden för din **ResourceGroupName** och **AutomationAccountName**.
8. Klicka på **OK**.

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Kontrollera runbook startar enligt schema
Varje gång du startar en runbook [skapas ett jobb](../../automation/automation-runbook-execution.md) och all utdata som loggas. Faktum är är det här de samma jobb som samlar in runbook. Du kan kontrollera att runbooken startar som förväntat genom att kontrollera jobb för runbooken efter starttiden för schemat har överskridits.

![Jobb](media/runbook-datacollect/jobs.png)

1. I egenskaperna för din runbook, Välj **jobb** under **resurser**.
2. Du bör se en lista över jobb varje gång runbook startades.
3. Klicka på någon av de jobb som du vill visa detaljerad information.
4. Klicka på **alla loggar** att visa loggarna och utdata från runbooken.
5. Bläddra längst ned försöker hitta en post som liknar bilden nedan.<br>![Utförlig](media/runbook-datacollect/verbose.png)
6. Klicka på den här posten för att visa detaljerad json-data som har skickats till Log Analytics.

## <a name="next-steps"></a>Nästa steg
- Använd [Vydesigner](../../azure-monitor/platform/view-designer.md) att skapa en vy som visar data som du har lagrat i Log Analytics-databasen.
- Paketera din runbook i en [hanteringslösning](../../azure-monitor/insights/solutions-creating.md) att distribuera till kunder.
- Läs mer om [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Läs mer om [Azure Automation](https://docs.microsoft.com/azure/automation/).
- Läs mer om den [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md).
