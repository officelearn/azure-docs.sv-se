---
title: Automatisk justering av e-postmeddelanden om hur du ska vägleda
description: Aktivera e-postmeddelanden för automatisk frågejustering i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1dbcf953ad5f70c6ddf2a73eef2ea712f1e1278c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632087"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-postmeddelanden för automatisk justering

SQL Database tuning rekommendationer genereras av Azure SQL Database [Automatisk justering](sql-database-automatic-tuning.md). Den här lösningen övervakar och analyserar kontinuerligt arbetsbelastningar för SQL-databaser som ger anpassade justeringsrekommendationer för varje enskild databas som är relaterad till indexskapande, indexradering och optimering av frågekörningsplaner.

Automatiska justeringsrekommendationer för SQL-databas kan visas i [Azure-portalen,](sql-database-advisor-portal.md)hämtas med [REST API-anrop](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) eller med hjälp av [T-SQL-](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) och [PowerShell-kommandon.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) Den här artikeln bygger på att använda ett PowerShell-skript för att hämta automatiska justeringsrekommendationer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatisera e-postmeddelanden för rekommendationer för Automatisk justering

Följande lösning automatiserar sändningen av e-postmeddelanden som innehåller automatiska justeringsrekommendationer. Den beskrivna lösningen består av att automatisera körningen av ett PowerShell-skript för att hämta justeringsrekommendationer med [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)och automatisering av schemaläggning av e-postleveransjobb med [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Skapa Azure Automation-konto

Om du vill använda Azure Automation är det första steget att skapa ett automatiseringskonto och konfigurera det med Azure-resurser som ska användas för körning av PowerShell-skriptet. Mer information om Azure Automation och dess funktioner finns i [Komma igång med Azure automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Följ dessa steg för att skapa Azure Automation-konto med hjälp av metoden för att välja och konfigurera Automation-appen från Marketplace:

- Logga in på Azure-portalen
- Klicka på "**+ Skapa en resurs**" i det övre vänstra hörnet
- Sök efter "**Automation**" (tryck enter)
- Klicka på automationsappen i sökresultaten

![Lägga till Azure-automatisering](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- En gång i fönstret "Skapa ett automationskonto" klickar du på "**Skapa**"
- Fyll i den information som krävs: ange ett namn för det här automatiseringskontot, välj ditt Azure-prenumerations-ID och Azure-resurser som ska användas för körningen av PowerShell-skriptet
- För alternativet "**Skapa Azure Kör som konto**" väljer du **Ja** för att konfigurera den typ av konto under vilket PowerShell-skript körs med hjälp av Azure Automation. Mer information om kontotyper finns i [Kör som-konto](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Avsluta skapandet av automationskontot genom att klicka på **Skapa**

> [!TIP]
> Registrera ditt Azure Automation-kontonamn, prenumerations-ID och resurser (till exempel kopiera och klistra in på ett anteckningsblock) precis som du angav när du skapar Automation-appen. Du behöver den här informationen senare.
>

Om du har flera Azure-prenumerationer som du vill skapa samma automatisering för måste du upprepa den här processen för dina andra prenumerationer.

## <a name="update-azure-automation-modules"></a>Uppdatera Azure Automation-moduler

PowerShell-skriptet för att hämta rekommendation för automatisk justering använder [kommandona Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) och [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) för vilka Azure Module version 4 och högre krävs.

- Om dina Azure-moduler behöver uppdateras, se [Stöd för Az-modul i Azure Automation](../automation/az-modules.md).

## <a name="create-azure-automation-runbook"></a>Skapa Azure Automation Runbook

Nästa steg är att skapa en Runbook i Azure Automation där PowerShell-skriptet för hämtning av justeringsrekommendationer finns.

Så här skapar du en ny Azure Automation-runbook:

- Få tillgång till Det Azure Automation-konto som du skapade i föregående steg
- En gång i fönstret för automatiseringskonto klickar du på menyalternativet "**Runbooks**" till vänster för att skapa en ny Azure Automation-runbook med PowerShell-skriptet. Mer information om hur du skapar automatiseringskörningar finns i [Skapa en ny runbook](../automation/manage-runbooks.md#creating-a-runbook).
- Om du vill lägga till en ny runbook klickar du på menyalternativet "**+Lägg till en runbook**" och klickar sedan på "**Snabbskapa – Skapa en ny runbook**".
- I runbook-fönstret skriver du in namnet på runbooken (i det här exemplet används "**AutomaticTuningEmailAutomation**" ), typen av runbook som **PowerShell** och skriv en beskrivning av den här runbooken för att beskriva dess syfte.
- Klicka på knappen **Skapa** för att slutföra skapandet av en ny runbook

![Lägg till Azure automation runbook](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Så här läser du in ett PowerShell-skript i den skapad körningsboken:

- I fönstret**Redigera PowerShell Runbook**väljer du "**RUNBOOKS**" i menyträdet och utökar vyn tills du ser namnet på din runbook (i det här exemplet "**AutomaticTuningEmailAutomation**"). Välj den här runbooken.
- På den första raden i "Redigera PowerShell Runbook" (med början på nummer 1) kopierar du in följande PowerShell-skriptkod. Det här PowerShell-skriptet tillhandahålls i dess ädst. Ändra skriptet för att passa dina behov.

I huvudet på det medföljande PowerShell-skriptet måste du ersätta `<SUBSCRIPTION_ID_WITH_DATABASES>` med ditt Azure-prenumerations-ID. Mer information om hur du hämtar ditt Azure-prenumerations-ID finns i [Hämta ditt AZURE-prenumerations-GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

Om det finns flera prenumerationer kan du lägga till dem som kommaavgränsade i egenskapen "$subscriptions" i skriptets rubrik.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Klicka på knappen "**Spara**" i det övre högra hörnet för att spara skriptet. När du är nöjd med skriptet klickar du på knappen "**Publicera**" för att publicera den här runbooken.

I huvudkörningsfönstret kan du välja att klicka på knappen "**Start**" för att **testa** skriptet. Klicka på "**Utdata**" för att visa resultaten av skriptet som körs. Denna utgång kommer att vara innehållet i din e-post. Exempelutdata från skriptet kan ses i följande skärmbild.

![Kör automatiska justeringsrekommendationer för vyn med Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Se till att justera innehållet genom att anpassa PowerShell-skriptet efter dina behov.

Med ovanstående steg läses PowerShell-skriptet för att hämta automatiska justeringsrekommendationer in i Azure Automation. Nästa steg är att automatisera och schemalägga e-postleveransjobbet.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatisera e-postjobben med Microsoft Flow

För att slutföra lösningen, som det sista steget, skapa ett automatiseringsflöde i Microsoft Flow som består av tre åtgärder (jobb):

1. "**Azure Automation - Skapa jobb**" – som används för att köra PowerShell-skriptet för att hämta automatiska justeringsrekommendationer i Azure Automation-runbooken
2. "**Azure Automation - Get job output**" – används för att hämta utdata från det utförda PowerShell-skriptet
3. "**Office 365 Outlook – Skicka ett e-postmeddelande**" – som används för att skicka ut e-post. E-postmeddelanden skickas ut med office 365-kontot för den person som skapar flödet.

Mer information om Microsoft Flow-funktioner finns i [Komma igång med Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Förutsättningen för det här steget är att registrera dig för [Microsoft Flow-konto](https://flow.microsoft.com) och logga in. En gång inne i lösningen, följ dessa steg för att ställa in ett **nytt flöde:**

- Menyalternativet **"Mina flöden"**
- Inuti Mina flöden väljer du länken "**+Skapa från tomt**" högst upp på sidan
- Klicka på länken "**Sök efter hundratals kontakter och triggers**" längst ner på sidan
- I sökfältet skriver du "**återkommande**" och väljer "**Schema - Återkommande**" från sökresultaten för att schemalägga e-postleveransjobbet så att det körs.
- I fönstret Återkommande i fältet Frekvens väljer du schemaläggningsfrekvensen för det här flödet som ska köras, till exempel skicka automatisk e-post varje minut, timme, dag, vecka, etc.

Nästa steg är att lägga till tre jobb (skapa, få utdata och skicka e-post) till det nyligen skapade återkommande flödet. Så här utför du att lägga till de jobb som krävs i flödet:

1. Skapa åtgärd för att köra PowerShell-skript för att hämta justeringsrekommendationer

   - Välj "**+Nytt steg**", följt av "**Lägg till en åtgärd**" i fönstret Återkommande flöde
   - I sökfältet skriver du "**automation**" och väljer "**Azure Automation – Create job**" i sökresultaten
   - Konfigurera jobbegenskaperna i fönstret Skapa jobb. För den här konfigurationen behöver du information om ditt Azure-prenumerations-ID, Resursgrupp och Automation-konto **som tidigare registrerats** i **fönstret Automation-konto**. Mer information om tillgängliga alternativ i det här avsnittet finns i [Azure Automation - Create Job](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Slutför skapandet av den här åtgärden genom att klicka på "**Spara flöde**"

2. Skapa åtgärd för att hämta utdata från det utförda PowerShell-skriptet

   - Välj "**+Nytt steg**", följt av "**Lägg till en åtgärd**" i fönstret Återkommande flöde
   - I sök arkiverad typ "**automation**" och välj "**Azure Automation – Get job output**" från sökresultaten. Mer information om tillgängliga alternativ i det här avsnittet finns i [Azure Automation – Hämta jobbutdata](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Fyll i fält som krävs (liknande skapandet av föregående jobb) – fyll i ditt Azure-prenumerations-ID, Resursgrupp och Automation-konto (som anges i fönstret Automation-konto)
   - Klicka i fältet "**Jobb-ID**" för menyn "**Dynamiskt innehåll**" för att visa. Välj alternativet "**Jobb-ID**" i den här menyn.
   - Slutför skapandet av den här åtgärden genom att klicka på "**Spara flöde**"

3. Skapa åtgärd för att skicka e-post med Office 365-integrering

   - Välj "**+Nytt steg**", följt av "**Lägg till en åtgärd**" i fönstret Återkommande flöde
   - I sök arkiverad typ "**skicka ett e-postmeddelande**" och välj "**Office 365 Outlook - Skicka ett e-postmeddelande**" från sökresultaten
   - I fältet "**Till**" skriver du den e-postadress som du måste skicka e-postmeddelandet till
   - I fälttypen **"Ämne"** i ämnet för din e-post, till exempel "E-postmeddelande med automatisk justeringsrekommendationer"
   - Klicka inuti fältet "**Brödtext**" för att menyn "**Dynamiskt innehåll**" ska visas. Välj " Innehåll "**Get job output****Innehåll**"
   - Slutför skapandet av den här åtgärden genom att klicka på "**Spara flöde**"

> [!TIP]
> Om du vill skicka automatiska e-postmeddelanden till olika mottagare skapar du separata flöden. I dessa ytterligare flöden ändrar du mottagarens e-postadress i fältet "Till" och ämnesraden för e-post i fältet Ämne. Genom att skapa nya runbooks i Azure Automation med anpassade PowerShell-skript (till exempel med ändring av Azure-prenumerations-ID) kan du ytterligare anpassa automatiserade scenarier, till exempel att skicka separata mottagare via e-post till exempel separata mottagare på automatiska justeringsrekommendationer för separata prenumerationer.
>

Ovanstående avslutar steg som krävs för att konfigurera arbetsflödet för e-postleveransjobb. Hela flödet som består av tre åtgärder som byggts visas i följande bild.

![Visa flödet för automatisk justering av e-postmeddelanden](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

För att testa flödet, klicka på "**Kör nu**" i det övre högra hörnet inuti flödesrutan.

Statistik för att köra automatiserade jobb, som visar framgång för e-postmeddelanden som skickas ut, kan ses från fönstret Flödesanalys.

![Köra flöde för automatisk justering av e-postmeddelanden](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Flödesanalysen är användbar för att övervaka hur framgångsrikt jobbkörningar lyckas och om det behövs för felsökning.  Vid felsökning kanske du också vill undersöka powershell-skriptkörningsloggen som är tillgänglig via Azure Automation-appen.

Den slutliga utgången av den automatiska e-post ser ut ungefär som följande e-post som tas emot efter att ha byggt och kört den här lösningen:

![Exempel på e-postutdata från e-postmeddelanden för automatisk justering](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Genom att justera PowerShell-skriptet kan du justera utdata och formatering av den automatiska e-postmeddelandet efter dina behov.

Du kan ytterligare anpassa lösningen för att skapa e-postmeddelanden baserat på en specifik justeringshändelse och flera mottagare för flera prenumerationer eller databaser, beroende på dina anpassade scenarier.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur automatisk justering kan hjälpa dig att förbättra databasens prestanda, se [Automatisk justering i Azure SQL Database](sql-database-automatic-tuning.md).
- Information om hur du aktiverar automatisk justering i Azure SQL Database för att hantera din arbetsbelastning finns i [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Information om hur du manuellt granskar och tillämpar rekommendationer för automatisk justering finns i [Hitta och tillämpa prestandarekommendationer](sql-database-advisor-portal.md).
