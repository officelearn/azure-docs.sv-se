---
title: "Insamling av data från logganalys med en runbook i Azure Automation | Microsoft Docs"
description: "Stegvis självstudiekurs som går igenom hur du skapar en runbook i Azure Automation för att samla in data i OMS-databas för analys av logganalys."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 59f674c9c6404da7f5384539189f41a4ba1a939a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Samla in data i logganalys med en Azure Automation-runbook
Du kan samla in data i logganalys mycket från olika källor, till exempel [datakällor](../log-analytics/log-analytics-data-sources.md) på agenter och även [data som samlas in från Azure](../log-analytics/log-analytics-azure-storage.md).  Det finns en scenarier men där du behöver samla in data som inte är tillgängligt via dessa källor som standard.  I dessa fall kan du använda den [HTTP Data Collector API: et](../log-analytics/log-analytics-data-collector-api.md) att skriva data till logganalys från valfri REST API-klient.  En vanlig metod för att utföra den här Datasamlingen använder en runbook i Azure Automation.   

Den här kursen går igenom processen för att skapa och schemalägga en runbook i Azure Automation för att skriva data till logganalys.


## <a name="prerequisites"></a>Krav
Det här scenariot kräver följande resurser som konfigurerats i din Azure-prenumeration.  Båda kan vara ett kostnadsfritt konto.

- [Logga Analytics-arbetsyta](../log-analytics/log-analytics-get-started.md).
- [Azure automation-konto](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Översikt över scenario
Den här självstudiekursen skriver du en runbook som samlar in information om Automation-jobb.  Azure Automation-Runbooks implementeras med PowerShell, så att du ska börja med att skriva och testa ett skript i Azure Automation-redigeraren.  När du har kontrollerat att du har samlat informationen som krävs kan du skriva data till logganalys och verifiera den anpassade datatypen.  Slutligen ska du skapa ett schema för att starta runbook med jämna mellanrum.

> [!NOTE]
> Du kan konfigurera Azure Automation för att skicka information om jobbets till logganalys utan denna runbook.  Det här scenariot används främst att stödja kursen och det rekommenderas att du skickar data till en test-arbetsyta.  


## <a name="1-install-data-collector-api-module"></a>1. Installera Data Collector API-modulen
Varje [begäran från API: et för HTTP-Data Collector](../log-analytics/log-analytics-data-collector-api.md#create-a-request) måste formateras på rätt sätt och innehålla ett authorization-huvud.  Du kan göra detta i din runbook, men du kan minska den kod som krävs med hjälp av en modul som förenklar processen.  En modul som du kan använda är [OMSIngestionAPI modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI) i PowerShell-galleriet.

Att använda en [modulen](../automation/automation-integration-modules.md) i en runbook måste den installeras i ditt Automation-konto.  Varje runbook i samma konto kan sedan använda funktionerna i modulen.  Du kan installera en ny modul genom att välja **tillgångar** > **moduler** > **lägga till en modul** i Automation-kontot.  

PowerShell-galleriet ger dig även om ett snabbt alternativ att distribuera en modul direkt till ditt automation-konto så att du kan använda alternativet för den här självstudiekursen.  

![OMSIngestionAPI modul](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Gå till [PowerShell-galleriet](https://www.powershellgallery.com/).
2. Sök efter **OMSIngestionAPI**.
3. Klicka på den **till Azure Automation** knappen.
4. Välj ditt automation-konto och klicka på **OK** att installera modulen.


## <a name="2-create-automation-variables"></a>2. Skapa variabler för Automation
[Automationsvariabler](..\automation\automation-variables.md) innehålla värden som kan användas av alla runbooks i ditt Automation-konto.  De göra runbooks mer flexibel genom att du kan ändra dessa värden utan att redigera den faktiska runbooken. Varje begäran från http-Data Collector API kräver ID och nyckel i OMS-arbetsytan och variabeln tillgångar är perfekt att lagra informationen.  

![Variabler](media/operations-management-suite-runbook-datacollect/variables.png)

1. Navigera till ditt Automation-konto i Azure-portalen.
2. Välj **variabler** under **delade resurser**.
2. Klicka på **lägga till en variabel** och skapa två variabler i följande tabell.

| Egenskap | Arbetsytan ID-värde | Nyckelvärdet för arbetsytan |
|:--|:--|:--|
| Namn | WorkspaceId | WorkspaceKey |
| Typ | Sträng | Sträng |
| Värde | Klistra in i ditt arbetsyte-ID för logganalys-arbetsytan. | Klistra in med primärt eller sekundärnyckeln i logganalys-arbetsytan. |
| Krypterade | Nej | Ja |



## <a name="3-create-runbook"></a>3. Skapa runbook

Azure Automation har en redigerare i portalen där du kan redigera och testa din runbook.  Du har möjlighet att använda Skriptredigeraren för att arbeta med [PowerShell direkt](../automation/automation-edit-textual-runbook.md) eller [skapar en grafisk runbook](../automation/automation-graphical-authoring-intro.md).  För den här självstudiekursen kommer du arbeta med ett PowerShell-skript. 

![Redigera runbooken](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Navigera till ditt Automation-konto.  
2. Klicka på **Runbooks** > **lägga till en runbook** > **skapa en ny runbook**.
3. Runbook-namnet skriver **samla in-Automation-jobb**.  Vilken runbooktyp av, Välj **PowerShell**.
4. Klicka på **skapa** skapa runbook och starta redigeraren.
5. Kopiera och klistra in följande kod i runbook.  Referera till kommentarerna i skriptet förklaring av koden.
    
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
        Add-AzureRmAccount `
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
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Testa runbook
Azure Automation innehåller en miljö till [testa din runbook](../automation/automation-testing-runbook.md) innan du publicerar den.  Du kan granska de data som samlas in av runbook och verifiera att den skrivs till logganalys som förväntat innan du publicerar den till produktion. 
 
![Testa runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Klicka på **spara** att spara runbook.
1. Klicka på **Test fönstret** öppna runbook i testmiljön.
3. Eftersom din runbook har parametrar uppmanas du att ange värden för dessa.  Ange namnet på resursgruppen och automatisering kontot som kommer att samla in information om jobbets från.
4. Klicka på **starta** att starta runbook.
3. Runbook startas med statusen **i kö** innan den går att **kör**.  
3. Runbook ska visa utförliga utdata med de jobb som samlas in i json-format.  Om inga jobb visas sedan kanske har inga jobb som skapats i automation-konto i den senaste timmen.  Försök att starta en runbook i automation-konto och utföra testet igen.
4. Kontrollera att resultatet inte visas några fel i kommandot post till logganalys.  Du bör ha ett meddelande som liknar följande.

    ![Post-utdata](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Kontrollera posterna i logganalys
När runbooken har slutförts i test och du har kontrollerat att utdata har tagits emot kan du verifiera att posterna har skapats med en [loggen Sök i logganalys](../log-analytics/log-analytics-log-searches.md).

![Loggutdata saknas](media/operations-management-suite-runbook-datacollect/log-output.png)

1. Välj logganalys-arbetsytan i Azure-portalen.
2. Klicka på **logga Sök**.
3. Skriv följande kommando `Type=AutomationJob_CL` och klicka på sökknappen. Observera att posttypen som innehåller _CL som inte anges i skriptet.  Det suffixet läggs automatiskt till loggtyp som indikerar att en anpassad logg-typen.
4. Du bör se en eller flera poster returneras som anger att runbooken fungerar som förväntat.


## <a name="6-publish-the-runbook"></a>6. Publicera en runbook
När du har kontrollerat att runbooken fungerar korrekt, måste du publicera den så att du kan köra den i produktion.  Du kan fortsätta att redigera och testa runbook utan att ändra den publicerade versionen.  

![Publicera runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Gå tillbaka till ditt automation-konto.
2. Klicka på **Runbooks** och välj **samla in-Automation-jobb**.
3. Klicka på **redigera** och sedan **publicera**.
4. Klicka på **Ja** när du ombeds bekräfta att du vill skriva över den tidigare publicerade versionen.

## <a name="7-set-logging-options"></a>7. Ange alternativ för loggning 
Test, var du kan visa [utförlig utdata](../automation/automation-runbook-output-and-messages.md#message-streams) eftersom du anger variabeln $VerbosePreference i skriptet.  Du måste ange loggningsegenskaperna för runbook om du vill visa detaljerade utdata för produktion.  För den runbook som används i den här självstudiekursen visas json-data som skickas till logganalys.

![Loggning och spårning](media/operations-management-suite-runbook-datacollect/logging.png)

1. I egenskaperna för din runbook **loggning och spårning** under **Runbook-inställningar**.
2. Ändra inställningen för **logga utförliga poster** till **på**.
3. Klicka på **Spara**.

## <a name="8-schedule-runbook"></a>8. Schemalägg runbook
Det vanligaste sättet att starta en runbook som samlar in övervakningsdata är att schemalägga startas automatiskt.  Det gör du genom att skapa en [schema i Azure Automation](../automation/automation-schedules.md) och kopplar den till din runbook.

![Schemalägg runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. I egenskaperna för din runbook, Välj **scheman** under **resurser**.
2. Klicka på **lägga till ett schema** > **länka ett schema till din runbook** > **skapa ett nytt schema**.
5. Ange följande värden för schemat och klicka på **skapa**.

| Egenskap | Värde |
|:--|:--|
| Namn | AutomationJobs-varje timme |
| Startar | Välj när minst 5 minuter efter den aktuella tiden. |
| Upprepning | Återkommande |
| Upprepas var | 1 timme |
| Ange förfallodatum | Nej |

När schemat skapas måste du ange parametervärden som kommer att användas varje gång det här schemat startar runbook.

6. Klicka på **konfigurera parametrar och körningsinställningar**.
7. Fyll i värden för din **ResourceGroupName** och **AutomationAccountName**.
8. Klicka på **OK**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Kontrollera runbook startar enligt schema
Varje gång en runbook startas [ett jobb skapas](../automation/automation-runbook-execution.md) och all utdata som loggas.  Faktum är är det här samma jobb som samlar in runbook.  Du kan kontrollera att runbook startar som förväntat genom att kontrollera jobb för runbook efter starttiden för schemat har överskridits.

![Jobb](media/operations-management-suite-runbook-datacollect/jobs.png)

1. I egenskaperna för din runbook, Välj **jobb** under **resurser**.
2. Du bör se en lista över jobb varje gång runbook startades.
3. Klicka på ett av jobb att visa information.
4. Klicka på **alla loggar** att visa loggfilerna och utdata från runbook.
5. Rulla ned för att hitta en post som liknar bilden nedan.<br>![Utförlig](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Klicka på den här posten för att visa detaljerad json-data som skickades till logganalys.



## <a name="next-steps"></a>Nästa steg
- Använd [Vydesigner](../log-analytics/log-analytics-view-designer.md) att skapa en vy som visar data som du har lagrat i logganalys-databasen.
- Paketera din runbook i en [hanteringslösning](operations-management-suite-solutions-creating.md) ska distribueras till kunder.
- Lär dig mer om [logganalys](https://docs.microsoft.com/azure/log-analytics/).
- Lär dig mer om [Azure Automation](https://docs.microsoft.com/azure/automation/).
- Lär dig mer om den [HTTP Data Collector API: et](../log-analytics/log-analytics-data-collector-api.md).