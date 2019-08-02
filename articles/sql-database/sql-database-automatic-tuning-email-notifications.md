---
title: Automatisk justering e-postmeddelanden instruktions guide – Azure SQL Database | Microsoft Docs
description: Aktivera e-postaviseringar för Azure SQL Database automatisk fråga-justering.
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
ms.openlocfilehash: df9390c00c34fce82de8cc17efb5cc3bce2e4e3d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569435"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-postmeddelanden för automatisk justering

SQL Database justerings rekommendationer genereras av Azure SQL Database [Automatisk justering](sql-database-automatic-tuning.md). Den här lösningen övervakar och analyserar arbets belastningarna i SQL-databaser med anpassade justerings rekommendationer för varje enskild databas som rör skapande av index, index borttagning och optimering av fråge körnings planer.

SQL Database automatiska justerings rekommendationer kan visas i [Azure Portal](sql-database-advisor-portal.md), hämtas med [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) -anrop eller med hjälp av [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) och [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) -kommandon. Den här artikeln baseras på hur du använder ett PowerShell-skript för att hämta rekommendationer för automatisk justering.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatisera e-postaviseringar för automatiska justerings rekommendationer

Följande lösning automatiserar sändningen av e-postmeddelanden som innehåller rekommendationer för automatisk justering. Den lösning som beskrivs består av automatisering av körning av ett PowerShell-skript för att hämta justerings rekommendationer med hjälp av [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)och automatisering av schemalagda e-postleverans jobb med [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Skapa Azure Automation konto

För att kunna använda Azure Automation är det första steget att skapa ett Automation-konto och konfigurera det med Azure-resurser som ska användas för körning av PowerShell-skriptet. Mer information om Azure Automation och dess funktioner finns i [komma igång med Azure Automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Följ dessa steg om du vill skapa Azure Automation konto genom att välja och konfigurera Automation-appen från Marketplace:

- Logga in på Azure Portal
- Klicka på " **+ skapa en resurs**" i det övre vänstra hörnet
- Sök efter "**Automation**" (tryck på RETUR)
- Klicka på Automation-appen i Sök resultaten

![Lägger till Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- När du är i fönstret "skapa ett Automation-konto" klickar du på "**skapa**"
- Fyll i den information som krävs: Ange ett namn för det här Automation-kontot, Välj ditt Azure-prenumerations-ID och Azure-resurser som ska användas för körning av PowerShell-skript
- För alternativet "**Skapa Azure kör som-konto**" väljer du **Ja** för att konfigurera den typ av konto som PowerShell-skriptet körs under med hjälp av Azure Automation. Mer information om konto typer finns i [Kör som-konto](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Avsluta skapandet av Automation-kontot genom att klicka på **skapa**

> [!TIP]
> Registrera ditt Azure Automation konto namn, prenumerations-ID och resurser (t. ex. kopiera och klistra in i ett anteckningar) precis som vid skapande av Automation-appen. Du behöver den här informationen senare.
>

Om du har flera Azure-prenumerationer för vilka du vill bygga samma automatisering måste du upprepa den här processen för dina andra prenumerationer.

## <a name="update-azure-automation-modules"></a>Uppdatera Azure Automation-moduler

PowerShell-skriptet för att hämta automatiska justerings rekommendationer använder [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) -och [Get-AzSqlDatabaseRecommendedAction-](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) kommandon som Azure-modul version 4 och senare krävs för.

- Om dina Azure-moduler behöver uppdateras, se [stöd för AZ-modulen i Azure Automation](../automation/az-modules.md).

## <a name="create-azure-automation-runbook"></a>Skapa Azure Automation Runbook

Nästa steg är att skapa en Runbook i Azure Automation inuti vilken PowerShell-skriptet för att hämta justerings rekommendationer finns.

Följ de här stegen för att skapa en ny Azure Automation Runbook:

- Öppna Azure Automation kontot som du skapade i föregående steg
- En gång i fönstret Automation-konto klickar du på meny alternativet "**Runbooks**" på vänster sida för att skapa en ny Azure Automation Runbook med PowerShell-skriptet. Mer information om hur du skapar Automation-runbooks finns i [skapa en ny Runbook](../automation/manage-runbooks.md#create-a-runbook).
- Om du vill lägga till en ny Runbook klickar du på meny alternativet " **+ Lägg till en Runbook**" och klickar sedan på "**Quick Create – skapa en ny Runbook**".
- I Runbook-fönstret skriver du namnet på din Runbook (för syftet med det här exemplet "**AutomaticTuningEmailAutomation**" används), väljer du typ av Runbook som **PowerShell** och skriver en beskrivning av denna Runbook för att beskriva dess syfte.
- Klicka på knappen **skapa** för att slutföra skapandet av en ny Runbook

![Lägg till Azure Automation-Runbook](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Följ dessa steg för att läsa in ett PowerShell-skript i Runbook som skapats:

- I fönstret "**Redigera PowerShell-Runbook**" väljer du "**RUNBOOKS**" i meny trädet och expanderar vyn tills du ser namnet på din Runbook (i det här exemplet "**AutomaticTuningEmailAutomation**"). Välj denna Runbook.
- På den första raden i "redigera PowerShell-Runbook" (från och med siffran 1) kopierar du följande PowerShell-skript kod. Det här PowerShell-skriptet tillhandahålls för att komma igång. Ändra skriptet så att det passar dina behov.

I rubriken för det angivna PowerShell-skriptet måste du ersätta `<SUBSCRIPTION_ID_WITH_DATABASES>` med ditt Azure-prenumerations-ID. Information om hur du hämtar ditt Azure-prenumerations-ID finns i [Hämta GUID för Azure](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/)-prenumerationen.

Om det finns flera prenumerationer kan du lägga till dem som kommaavgränsade till egenskapen "$subscriptions" i rubriken för skriptet.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
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

Klicka på knappen "**Spara**" i det övre högra hörnet för att spara skriptet. När du är nöjd med skriptet klickar du på knappen**publicera**för att publicera denna Runbook.

I den huvudsakliga Runbook-fönstret kan du välja att klicka på knappen "**Starta**" för att **testa** skriptet. Klicka på "**utdata**" för att visa resultatet av skriptet som körs. Dessa utdata kommer att bli innehållet i din e-post. Exempel resultatet från skriptet kan visas på följande skärm bild.

![Kör Visa rekommendationer för automatisk justering med Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Se till att justera innehållet genom att anpassa PowerShell-skriptet efter dina behov.

Med ovanstående steg laddas PowerShell-skriptet för att hämta automatiska justerings rekommendationer i Azure Automation. Nästa steg är att automatisera och schemalägga e-postleveransen.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatisera e-postjobben med Microsoft Flow

Slutför lösningen genom att skapa ett Automation-flöde i Microsoft Flow som består av tre åtgärder (jobb) som sista steg:

1. "**Azure Automation-skapa jobb**" – används för att köra PowerShell-skriptet för att hämta rekommendationer för automatisk justering i Azure Automation Runbook
2. "**Azure Automation-Hämta jobbets utdata**" – används för att hämta utdata från det exekverade PowerShell-skriptet
3. "**Office 365 Outlook – skicka ett e-postmeddelande**" – används för att skicka ut e-post. E-postmeddelanden skickas ut med Office 365-kontot för den person som skapar flödet.

Mer information om Microsoft Flow funktioner finns i [komma igång med Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Förutsättningen för det här steget är att registrera dig för [Microsoft Flow](https://flow.microsoft.com) konto och logga in. Följ de här stegen för att skapa ett **nytt flöde**när du är inne i lösningen:

- Öppna meny alternativet "**mina flöden**"
- I mina flöden väljer du länken " **+ skapa från Tom**" längst upp på sidan
- Klicka på länken**Sök efter hundratals kopplingar och**utlösare längst ned på sidan
- I Sök fältet typ "**upprepning**" och välj "**Schemalägg-upprepning**" från Sök resultatet för att schemalägga att e-postleveransen ska köras.
- I rutan upprepning i fältet Frekvens väljer du schemaläggnings frekvens för det här flödet som ska köras, till exempel skicka automatiskt e-postmeddelande varje minut, timme, dag, vecka osv.

Nästa steg är att lägga till tre jobb (skapa, Hämta utdata och skicka e-post) till det nyligen skapade återkommande flödet. Följ dessa steg om du vill lägga till de nödvändiga jobben i flödet:

1. Skapa åtgärd för att köra PowerShell-skript för att hämta justerings rekommendationer

   - Välj " **+ nytt steg**", följt av "**Lägg till en åtgärd**" i fönstret för upprepnings flöde
   - I Sök fältet typ "**Automation**" och välj "**Azure Automation – skapa jobb**" från Sök resultatet
   - I fönstret Skapa jobb konfigurerar du jobb egenskaperna. För den här konfigurationen behöver du information om ditt Azure-prenumerations-ID, resurs grupp och Automation-konto som **tidigare registrerats** i **fönstret Automation-konto**. Mer information om vilka alternativ som är tillgängliga i det här avsnittet finns i [Azure Automation-skapa jobb](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Slutför skapandet av den här åtgärden genom att klicka på "**Spara flöde**"

2. Skapa åtgärd för att hämta utdata från det exekverade PowerShell-skriptet

   - Välj " **+ nytt steg**", följt av "**Lägg till en åtgärd**" i fönstret för upprepnings flöde
   - I Sök den arkiverade typen "**Automation**" och välj "**Azure Automation – Hämta jobbets utdata**" från Sök resultaten. Mer information om vilka alternativ som är tillgängliga i det här avsnittet finns [Azure Automation – Hämta utdata för jobb](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Fyll i fälten som krävs (liknar att skapa föregående jobb) – Fyll i ditt Azure-prenumerations-ID, resurs grupp och Automation-konto (som anges i fönstret Automation-konto)
   - Klicka i fältet "**jobb-ID**" för att visa upp på menyn "**dynamiskt innehåll**". I den här menyn väljer du alternativet "**jobb-ID**".
   - Slutför skapandet av den här åtgärden genom att klicka på "**Spara flöde**"

3. Skapa åtgärd för att skicka ut e-post med Office 365-integrering

   - Välj " **+ nytt steg**", följt av "**Lägg till en åtgärd**" i fönstret för upprepnings flöde
   - I Sök den arkiverade typen "**Skicka ett e-postmeddelande**" och välj "**Office 365 Outlook – skicka ett e-postmeddelande**" från Sök resultatet
   - I fältet**till**anger du den e-postadress som du vill skicka e-postmeddelandet till
   - I fält typen "**subject**" i ämnet för ditt e-postmeddelande, till exempel "automatiska justerings rekommendationer e-postmeddelande"
   - Klicka i fältet "**brödtext**" för att visa upp på menyn "**dynamiskt innehåll**". I den här menyn, under "**Hämta jobbets utdata**", väljer du "**innehåll**"
   - Slutför skapandet av den här åtgärden genom att klicka på "**Spara flöde**"

> [!TIP]
> Skapa separata flöden för att skicka automatiska e-postmeddelanden till olika mottagare. I dessa ytterligare flöden ändrar du mottagarens e-postadress i fältet till och e-postmeddelandets ämnesrad i fältet ämne. Om du skapar nya Runbooks i Azure Automation med anpassade PowerShell-skript (till exempel med ändring av Azure-prenumerations-ID) kan du göra ytterligare anpassningar av automatiserade scenarier, till exempel e-posta separata mottagare för automatisk justering rekommendationer för separata prenumerationer.
>

Ovan följer steg som krävs för att konfigurera arbets flödet för e-postleverans. Hela flödet som består av tre åtgärder som skapats visas i följande bild.

![Visa flöde för automatisk justering av e-postmeddelanden](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Testa flödet genom att klicka på "**Kör nu**" i det övre högra hörnet i flödes fönstret.

Statistik för att köra automatiserade jobb, som visar att lyckade e-postaviseringar skickas ut, visas i fönstret flödes analys.

![Flöde som körs för automatisk justering av e-postaviseringar](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Flödes analysen är till hjälp för att övervaka lyckade jobb körningar och om det behövs för fel sökning.  Vid fel sökning kan du också behöva granska körnings loggen för PowerShell-skript som är tillgänglig via Azure Automation app.

De slutliga utdata från det automatiserade e-postmeddelandet ser ut ungefär som i följande e-postmeddelande när du har skapat och kört lösningen:

![Exempel på e-postutdata från automatisk justering e-postmeddelanden](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Genom att justera PowerShell-skriptet kan du justera utdata och formatering för det automatiserade e-postmeddelandet efter behov.

Du kan anpassa lösningen ytterligare för att bygga e-postmeddelanden baserat på en bestämd justerings händelse och till flera mottagare för flera prenumerationer eller databaser, beroende på dina anpassade scenarier.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur automatisk justering kan hjälpa dig att förbättra databas prestandan, se [Automatisk justering i Azure SQL Database](sql-database-automatic-tuning.md).
- Om du vill aktivera automatisk justering i Azure SQL Database för att hantera din arbets belastning, se [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Information om hur du manuellt granskar och använder automatiska justerings rekommendationer finns i [hitta och tillämpa prestanda rekommendationer](sql-database-advisor-portal.md).
