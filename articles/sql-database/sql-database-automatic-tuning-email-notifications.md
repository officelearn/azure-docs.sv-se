---
title: Automatisk justering Azure SQL Database e-postaviseringar instruktioner - | Microsoft Docs
description: "Azure SQL Database analyserar SQL-fråga och anpassas automatiskt till användare arbetsbelastning."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
ms.reviewer: carlrab
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 01/31/2018
ms.author: v-daljep
ms.openlocfilehash: 3598c61634bf039e5c9500cc9a4f6b08e4830ce5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>E-postaviseringar för automatisk justering

SQL-databas prestandajustering rekommendationer genereras av Azure SQL Database [automatisk justering](sql-database-automatic-tuning.md). Den här lösningen övervakar kontinuerligt och analyserar arbetsbelastningar för SQL-databaser att tillhandahålla anpassade justera rekommendationer för varje enskild databas som rör skapandet av index, ta bort indexet och optimering av frågeplaner för körning.

SQL-databas automatisk justering rekommendationer som kan visas i den [Azure-portalen](sql-database-advisor-portal.md), hämtas med [REST API](https://docs.microsoft.com/en-us/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) anropar eller med hjälp av [T-SQL](https://azure.microsoft.com/en-us/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) och [ PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction) kommandon. Den här artikeln är baserad på använda ett PowerShell-skript för att hämta automatisk justering rekommendationer.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatisera e-postaviseringar för automatisk justering rekommendationer

Följande lösning automatiserar sändning av e-postmeddelanden som innehåller rekommendationer för automatisk justering. Lösningen som beskrivs består av automatisera körningen av ett PowerShell-skript för att hämta prestandajustering rekommendationer med [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), och automatisering av schemaläggning e-jobb med hjälp av [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Skapa en Azure Automation-konto

Det första steget är att skapa ett automation-konto och konfigurera den med Azure-resurser ska användas för körning av PowerShell-skript för att använda Azure Automation. Läs mer om Azure Automation och dess funktioner i [komma igång med Azure automation](https://docs.microsoft.com/en-us/azure/automation/automation-offering-get-started).

Följ dessa steg om du vill skapa en Azure Automation-kontot via metoden för att välja och konfigurera Automation-app från Marketplace:

- Logga in på Azure portal
- Klicka på ”**+ skapa en resurs**” i det övre vänstra hörnet
- Sök efter ”**Automation**” (tryck på RETUR)
- Klicka på appen Automation i sökresultaten

![Lägga till Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- En gång i ”Skapa ett Automation konto”-rutan klickar du på ”**skapa**”
- Fyll i informationen som krävs: Ange ett namn för det här automatiseringskontot, Välj din Azure-prenumeration ID och Azure-resurser som ska användas för PowerShell-skriptkörning
- För den ”**skapa kör som-kontot Azure**” väljer **Ja** så här konfigurerar du typ av konto under vilket PowerShell-skriptet körs med hjälp av Azure Automation. Mer information om kontotyper finns [kör som-konto](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account)
- Avslutar skapandet av automation-kontot genom att klicka på **skapa**

> [!TIP]
> Registrera ditt Azure Automation-kontonamnet, prenumerations-ID och resurser (till exempel kopiera / klistra in till en anteckningar) exakt som den anges när du skapar appen Automation. Du behöver den här informationen senare.
>

Om du har flera Azure-prenumerationer som du vill skapa samma automatisering, måste du upprepa processen för dina övriga prenumerationer.

## <a name="update-azure-automation-modules"></a>Uppdatera Azure Automation-moduler

PowerShell-skript för att hämta automatisk justering rekommendation använder [Get-AzureRmResource](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Resources/Get-AzureRmResource) och [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) kommandon för vilka Azure-moduler att uppdatera den version 4 och senare krävs.

Följ dessa steg om du vill uppdatera Azure PowerShell-moduler:

- Öppnar du fönstret för Automation-appen och välj ”**moduler**” på menyn vänster (Rulla ned som det här menyalternativet under delade resurser).
- I fönstret moduler klickar du på ”**uppdatering Azure moduler**” högst upp och vänta tills meddelandet ”Azure-moduler har uppdaterats” visas. Den här processen kan ta ett par minuter att slutföra.

![Uppdatera Azure automation-moduler](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Version som krävs av AzureRM.Resources och AzureRM.Sql moduler måste vara version 4 och senare.

## <a name="create-azure-automation-runbook"></a>Skapa en Azure Automation-Runbook

Nästa steg är att skapa en Runbook i Azure Automation inom vilket PowerShell-skript för hämtning av justera rekommendationer finns.

Följ dessa steg om du vill skapa en ny Azure Automation-runbook:

- Få åtkomst till Azure Automation-kontot som du skapade i föregående steg
- En gång i fönstret automation-konto klickar du på den ”**Runbooks**” menyalternativ till vänster för att skapa en ny Azure Automation-runbook med PowerShell-skript. Mer information om hur du skapar automation-runbooks finns [att skapa en ny runbook](../automation/automation-creating-importing-runbook.md).
- Om du vill lägga till en ny runbook, klickar du på den ”**+ Lägg till en runbook**” menyalternativet och klicka på den ”**snabbt skapa – skapa en ny runbook**”.
- Ange namnet på din runbook i Runbook-fönstret (i det här exemplet ”**AutomaticTuningEmailAutomation**” används), Välj typ av runbook som **PowerShell** och skriva en beskrivning av Denna runbook för att beskriva sitt syfte.
- Klicka på den **skapa** för att skapa en ny runbook

![Lägg till Azure automation-runbook](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Följ dessa steg för att läsa in ett PowerShell-skript i runbook skapas:

- I den ”**redigera PowerShell-Runbook**” fönstret, Välj ”**RUNBOOKS**” på menyn i konsolträdet och expandera vyn tills du ser namnet på din runbook (i det här exemplet ” **AutomaticTuningEmailAutomation**”). Välj denna runbook.
- På den första raden ”redigera PowerShell runbook” (startar med 1) kopiera / klistra in följande kod i PowerShell-skript. Detta PowerShell-skript har angetts som-är att komma igång. Ändra skriptet till suite dina behov.

I rubriken på det angivna PowerShell-skriptet, måste du ersätta `<SUBSCRIPTION_ID_WITH_DATABASES>` med ditt Azure-prenumeration-ID. Information om hur du hämtar Azure prenumerations-ID finns [hämta ditt Azure-prenumeration GUID](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/).

Vid flera prenumerationer, du kan lägga till dem som kommaavgränsad egenskapen ”$subscriptions” i rubriken för skriptet.

```PowerShell
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
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

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
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
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

Klicka på ”**spara**”-knappen i det övre högra hörnet spara skriptet. När du är nöjd med skriptet klickar du på den ”**publicera**” för att publicera denna runbook. 

I fönstret huvudsakliga runbook kan du klicka på den ”**starta**” för att **testa** skriptet. Klicka på den ”**utdata**” att visa resultat av skript som körs. Den här utdatan kommer att innehållet i din e-post. På utdata från skriptet kan ses i följande skärmbild.

![Kör vy automatisk justering rekommendationer med Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Se till att justera innehållet genom att anpassa PowerShell-skript för dina behov.

Med stegen ovan PowerShell-skript för att hämta automatisk justering rekommendationer har lästs in i Azure Automation. Nästa steg är att automatisera och schemalägga e-delivery-jobb.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatisera e-jobb med Microsoft Flow

Skapa ett automation-flöde i Microsoft Flow som består av tre åtgärder (jobb) för att slutföra en lösning som det sista steget: 

1. ”**Azure Automation - jobbet för att skapa**” – används för att köra PowerShell-skript för att hämta automatisk justering rekommendationer i Azure Automation-runbook
2. ”**Azure Automation - Get-jobbutdata**” – används för att hämta utdata från det körda PowerShell-skriptet
3. ”**Office 365 Outlook – skicka ett e-post**” – används för att skicka e-post. E-postmeddelanden skickas ut med Office 365-konto på den person som skapar flödet.

Mer information om funktionerna i Microsoft Flow finns [komma igång med Microsoft Flow](https://docs.microsoft.com/en-us/flow/getting-started).

Förutsättning för det här steget är att registrera dig för [Microsoft Flow](https://flow.microsoft.com) konto och logga in. En gång i lösningen, Följ dessa steg att ställa in en **nya flödet**:

- Åtkomst ”**min flöden**” menyobjekt
- I min flöden, Välj den ”**+ skapa från tomt**” längst upp på sidan
- Klicka på länken ”**Sök efter hundratals kopplingar och utlösare**” längst ned på sidan
- I fältet söktyp ”**återkommande**”, och välj ”**schema - upprepning**” i sökresultatet att schemalägga leverans av e-jobbet ska köras.
- Välj schemaläggning frekvensen för det här flödet som send automated e-post varje minut, timma, dag, vecka och så vidare om du vill köra i rutan upprepning i fältet frekvens.

Nästa steg är att lägga till tre jobb (skapa, är get-utdata och skicka e-post) till det nyligen skapade återkommande flödet. Följ dessa steg för att åstadkomma lägga till nödvändiga jobb till flödet:

1. Skapa åtgärd för att köra PowerShell-skript för att hämta prestandajustering rekommendationer
- Välj ”**+ nytt steg**”, följt av ”**lägga till en åtgärd**” i fönstret återkommande flöde
- I fältet söktyp ”**automation**” och välj ”**Azure Automation – skapa jobbet**” i sökresultatet
- Konfigurera jobbegenskaper för i fönstret Skapa jobbet. För den här konfigurationen behöver du information om din Azure-prenumeration ID, resursgrupp och Automation-konto **inspelat** på den **Automation-konto fönstret**. Mer information om alternativen i det här avsnittet finns [Azure Automation - skapa jobbet](https://docs.microsoft.com/connectors/azureautomation/#Create_job).
- Att skapa den här åtgärden genom att klicka på ”**spara flödet**”

2. Skapa åtgärd för att hämta utdata från det körda PowerShell-skriptet
- Välj ”**+ nytt steg**”, följt av ”**lägga till en åtgärd**” i fönstret återkommande flöde
- I rutan Sök datatyper ”**automation**” och välj ”**Azure Automation-Get-jobbutdata**” i sökresultatet. Mer information om alternativen i det här avsnittet finns [Azure Automation-Get-jobbutdata](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output).
- Fylla i fälten krävs (ungefär som att skapa föregående) - Fyll i din Azure-prenumeration ID, resursgrupp och Automation-konto (som har angetts i fönstret Automation-konto)
- Klicka i fältet ”**jobb-ID**” för den ”**dynamiskt innehåll**” menyn ska visas. I den här menyn, Välj alternativet ”**jobb-ID**”.
- Att skapa den här åtgärden genom att klicka på ”**spara flödet**”

3. Skapa åtgärd att skicka e-post med Office 365-integration
- Välj ”**+ nytt steg**”, följt av ”**lägga till en åtgärd**” i fönstret återkommande flöde
- I rutan Sök datatyper ”**skickar ett e-**” och välj ”**Office 365 Outlook – skicka ett e-post**” i sökresultatet
- I den ”**till**” fältet Ange den e-postadress som du måste skicka e-postmeddelandet
- I den ”**ämne**” fälttyp i ämnet för e-post, till exempel ”automatisk justering rekommendationer e-postavisering”
- Klicka i fältet ”**brödtext**” för den ”**dynamiskt innehåll**” menyn ska visas. Från inom den här menyn under ”**hämta jobbutdata**”, Välj ”**innehåll**” 
- Att skapa den här åtgärden genom att klicka på ”**spara flödet**”

> [!TIP]
> Om du vill skicka automatiserade e-postmeddelanden till olika mottagare, skapa separata flöden. Ändra adressen till e-postmottagare i fältet ”till” och ämnesraden för e-post i fältet ”ämne” i dessa ytterligare flöden. Skapa nya runbooks i Azure Automation med anpassade PowerShell-skript (exempelvis med ändringen av Azure prenumerations-ID) gör att ytterligare anpassning automatiserade scenarier exempel till exempel e-posta separat mottagare på automatisk justering rekommendationer för olika prenumerationer.
>

Ovanstående slutsatsen stegen för att konfigurera e-leverans jobbet arbetsflödet. Hela flödet som består av tre åtgärder som har skapats visas i följande bild.

![Visa automatisk justering e-postaviseringar flöde](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Om du vill testa flödet, klicka på ”**kör nu**” i det övre högra hörnet i fönstret flödet.

Statistik för att köra automatiserade jobb kan visar framgången för e-postmeddelanden som skickas, kan ses från fönstret flödet analytics.

![Kör flödet för automatisk justering e-postaviseringar](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Flödet analytics är användbart för att övervaka framgången för jobbet körningar och om det behövs för felsökning.  Vid felsökning kan kanske du också vill undersöka PowerShell-skriptet körningsloggen tillgängligt via appen Azure Automation.

Det slutgiltiga resultatet för automatisk e-post liknar följande e-postmeddelandet tas emot efter att skapa och köra den här lösningen:

![Exempel på e-resultat från automatisk justering e-postaviseringar](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Genom att justera PowerShell-skriptet kan justera du utdata och formatering av automatisk e-post för dina behov.

Du kan också anpassa lösningen för att skapa e-postmeddelanden baserat på en viss prestandajustering händelse och till flera mottagare för flera databaser, beroende på dina egna scenarier och prenumerationer. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur automatisk justering kan hjälpa dig att förbättra prestanda för databasen finns [automatisk justering i Azure SQL Database](sql-database-automatic-tuning.md).
- För att aktivera automatisk justering i Azure SQL Database för att hantera din arbetsbelastning, se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill granska och Använd automatisk justering rekommendationer manuellt finns [söka efter och tillämpa rekommendationer](sql-database-advisor-portal.md).
