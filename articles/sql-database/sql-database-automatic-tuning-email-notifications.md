---
title: Automatisk justering i Azure SQL Database e-meddelanden instruktionsguide - | Microsoft Docs
description: Aktivera e-postaviseringar för Azure SQL Database fråga automatisk justering.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: f68097f7b97814bc24926b6fc1b0bb2a750855a2
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311278"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-postmeddelanden för automatisk justering

SQL Database justeringsrekommendationer genereras av Azure SQL Database [automatisk justering](sql-database-automatic-tuning.md). Den här lösningen övervakar kontinuerligt och analyserar arbetsbelastningar i SQL-databaser att tillhandahålla anpassade justeringsrekommendationer för varje enskild databas som rör skapande av index, borttagning av index och optimering av frågeplaner för körning.

SQL Database automatiska justeringsrekommendationer kan visas i den [Azure-portalen](sql-database-advisor-portal.md), hämtades med [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) anropar eller genom att använda [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) och [ PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) kommandon. Den här artikeln baseras på att använda ett PowerShell-skript för att hämta automatiska justeringsrekommendationer för.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatisera e-postmeddelanden för automatiska justeringsrekommendationer för

Följande lösning automatiserar sändning av e-postmeddelanden som innehåller automatiska justeringsrekommendationer för. Lösningen beskrivs består av automatiserar körningen av ett PowerShell-skript för att hämta justeringsrekommendationer med [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), och automatisering av schemaläggning e-jobb med hjälp av [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Skapa Azure Automation-konto

Det första steget är att skapa ett automation-konto och konfigurera den med Azure-resurser du använder för körning av PowerShell-skriptet för att använda Azure Automation. Läs mer om Azure Automation och dess funktioner i [komma igång med Azure automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Följ stegen nedan för att skapa Azure Automation-konto via metoden för att välja och konfigurera Automation-app från Marketplace:

- Logga in på Azure portal
- Klicka på ”**+ skapa en resurs**” i det övre vänstra hörnet
- Sök efter ”**Automation**” (tryck på RETUR)
- Klicka på appen Automation i sökresultaten

![Att lägga till Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- En gång i ”Skapa ett Automation-konto”-rutan, klicka på ”**skapa**”
- Fyll i informationen som krävs: Ange ett namn för det här automatiseringskontot, Välj din Azure-ID och Azure-prenumerationsresurser som ska användas för PowerShell-skriptkörning
- För den ”**skapa kör som-konto**” väljer **Ja** så här konfigurerar du typ av konto som skriptet körs under vilken PowerShell med hjälp av Azure Automation. Mer information om kontotyper finns [kör som-konto](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Avslutar skapandet av automation-kontot genom att klicka på **skapa**

> [!TIP]
> Registrera ditt Azure Automation-kontonamn, prenumerations-ID och resurser (till exempel kopiera och klistra in till anteckningar) exakt som den anges när du skapar Automation-app. Du behöver den här informationen senare.
>

Om du har flera Azure-prenumerationer som du vill skapa samma automatisering kan behöva du upprepa processen för dina andra prenumerationer.

## <a name="update-azure-automation-modules"></a>Uppdatera Azure Automation-moduler

PowerShell-skript för att hämta automatiska justeringen rekommendation använder [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) och [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) kommandon som uppdateringshantering för Azure-moduler till version 4 och senare krävs.

Följ dessa steg om du vill uppdatera Azure PowerShell-moduler:

- Öppnar du fönstret för Automation-appen och välj ”**moduler**” på menyn till vänster (Rulla nedåt som menyobjektet är under delade resurser).
- I fönstret moduler klickar du på ”**uppdatera Azure-moduler**” högst upp och vänta tills meddelandet ”Azure-moduler har uppdaterats” visas. Den här processen kan ta ett par minuter att slutföra.

![Uppdatera Azure automationsmoduler](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Version som krävs av AzureRM.Resources och i AzureRM.Sql moduler måste vara version 4 och senare.

## <a name="create-azure-automation-runbook"></a>Skapa Azure Automation-Runbook

Nästa steg är att skapa en Runbook i Azure Automation där PowerShell-skript för hämtning av justeringsrekommendationer finns.

Följ stegen nedan för att skapa en ny Azure Automation-runbook:

- Åtkomst till Azure Automation-kontot som du skapade i föregående steg
- En gång i fönstret automation-konto klickar du på den ”**Runbooks**” menyobjekt på vänster sida för att skapa en ny Azure Automation-runbook med PowerShell-skriptet. Mer information om hur du skapar automation-runbooks finns [skapa en ny runbook](../automation/manage-runbooks.md#create-a-runbook).
- Lägg till en ny runbook genom att klicka på den ”**+ Lägg till en runbook**” menyalternativet och sedan klicka på den ”**snabbt skapa – skapa en ny runbook**”.
- I Runbook-rutan skriver du namnet på din runbook (i det här exemplet ”**AutomaticTuningEmailAutomation**” används), Välj typ av runbook som **PowerShell** och skriva en beskrivning av Denna runbook för att beskriva syftet.
- Klicka på den **skapa** knappen för att skapa en ny runbook

![Lägg till Azure automation-runbook](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Följ dessa steg för att läsa in ett PowerShell-skript i runbook har skapats:

- I den ”**redigera PowerShell-Runbook**” fönstret, Välj ”**RUNBOOKS**” på menyn i konsolträdet och expandera vyn tills du ser namnet på din runbook (i det här exemplet ” **AutomaticTuningEmailAutomation**”). Välj denna runbook.
- På den första raden i ”Redigera PowerShell-Runbook” (från och med 1), kopiera och klistra in följande kod i PowerShell-skript. Den här PowerShell.skript tillhandahålls som – är att komma igång. Ändra skriptet till suite dina behov.

I rubriken för det angivna PowerShell-skriptet, du måste ersätta `<SUBSCRIPTION_ID_WITH_DATABASES>` med ditt Azure-prenumerations-ID. Läs hur du hämtar Azure prenumerations-ID i [hämta din Azure-prenumeration GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

När det gäller flera prenumerationer, du kan lägga till dem som kommaavgränsad egenskapen ”$subscriptions” i rubriken för skriptet.

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

Klicka på ”**spara**”-knappen i det övre högra hörnet att spara skriptet. När du är nöjd med skript, klickar du på den ”**publicera**” knappar för att publicera denna runbook.

I fönstret huvudsakliga runbook kan du klicka på den ”**starta**” för att **testa** skriptet. Klicka på den ”**utdata**” att visa resultatet av skriptet körs. Dessa utdata ska vara innehållet i din e-post. Exempel på utdata från skriptet kan ses i följande skärmbild.

![Visa automatiska justeringsrekommendationer med Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Se till att justera innehållet genom att anpassa PowerShell-skriptet efter dina behov.

PowerShell-skript för att hämta automatiska justeringsrekommendationer läses in i Azure Automation med stegen ovan. Nästa steg är att automatisera och schemalägga e-delivery-jobbet.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatisera e-jobb med Microsoft Flow

Skapa ett automation-flöde i Microsoft Flow som består av tre åtgärder (jobb) för att slutföra lösningen, som det sista steget:

1. ”**Azure Automation - skapa jobb**” – används för att köra PowerShell-skript för att hämta automatiska justeringen rekommendationer i Azure Automation-runbook
2. ”**Azure Automation - Get-jobbutdata**” – används för att hämta utdata från utförda PowerShell-skript
3. ”**Office 365 Outlook – skicka ett e-postmeddelande**” – används för att skicka e-postmeddelande. E-postmeddelanden skickas ut med Office 365-konto på den person som skapar flödet.

Mer information om funktionerna i Microsoft Flow finns [komma igång med Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Krav för det här steget är att registrera dig för [Microsoft Flow](https://flow.microsoft.com) konto och logga in. En gång i lösningen, följer du dessa steg för att ställa in en **nytt flöde**:

- Åtkomst ”**Mina flöden**”-menyn
- I Mina flöden, Välj den ”**+ skapa från tom**” längst upp på sidan
- Klicka på länken ”**Sök efter hundratals kopplingar och utlösare**” längst ned på sidan
- I fältet söktyp ”**upprepning**”, och välj ”**schema – återkommande**” från sökresultaten att schemalägga att e-delivery-jobbet ska köras.
- I fönstret upprepning i fältet frekvens väljer du schemaläggning frekvensen för det här flödet att köra, t.ex. skicka automatiserade e-post varje minut, timme, dag, vecka, osv.

Nästa steg är att lägga till tre jobb (skapa, är get-utdata och skicka e-post) till det nyligen skapade återkommande flödet. Följ dessa steg för att åstadkomma att lägga till de nödvändiga jobb till flödet:

1. Skapa åtgärd för att köra PowerShell-skript för att hämta justeringsrekommendationer

   - Välj ”**+ nytt steg**”, följt av ”**Lägg till en åtgärd**” i fönstret återkommande flöde
   - I fältet söktyp ”**automation**” och välj ”**Azure Automation – skapa jobb**” från sökresultaten
   - Konfigurera jobbegenskaper för i fönstret Skapa jobbet. För den här konfigurationen behöver du information om dina Azure-prenumeration-ID, resursgrupp och ett Automation-konto **tidigare inspelade** på den **Automation-konto fönstret**. Mer information om tillgängliga alternativ i det här avsnittet finns [Azure Automation - skapa jobbet](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Slutför att skapa den här åtgärden genom att klicka på ”**spara flöde**”

2. Skapa åtgärd för att hämta utdata från utförda PowerShell-skript

   - Välj ”**+ nytt steg**”, följt av ”**Lägg till en åtgärd**” i fönstret återkommande flöde
   - I rutan Sök datatyper ”**automation**” och välj ”**Azure Automation – Get-jobbutdata**” från sökresultaten. Mer information om tillgängliga alternativ i det här avsnittet finns [Azure Automation – Get-jobbutdata](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Fyll i fälten obligatoriskt (liknar skapar det föregående jobbet) – Fyll i din Azure-prenumeration-ID, resursgrupp och ett Automation-konto (som angetts i fönstret Automation-konto)
   - Klicka i fältet ”**jobb-ID**” för den ”**dynamiskt innehåll**” menyn visas. Från den här menyn väljer alternativet ”**jobb-ID**”.
   - Slutför att skapa den här åtgärden genom att klicka på ”**spara flöde**”

3. Skapa åtgärder för att skicka e-postmeddelande med hjälp av Office 365-integration

   - Välj ”**+ nytt steg**”, följt av ”**Lägg till en åtgärd**” i fönstret återkommande flöde
   - I rutan Sök datatyper ”**skicka ett e-postmeddelande**” och välj ”**Office 365 Outlook – skicka ett e-postmeddelande**” från sökresultaten
   - I den ”**till**” fälttyp den e-postadress som du behöver skicka e-postmeddelandet
   - I den ”**ämne**” fälttyp i din e-postmeddelandets ämne, till exempel ”automatiska justeringsrekommendationer för e-postavisering”
   - Klicka i fältet ”**brödtext**” för den ”**dynamiskt innehåll**” menyn visas. Från inom den här menyn under ”**hämta jobbutdata**”, Välj ”**innehåll**”
   - Slutför att skapa den här åtgärden genom att klicka på ”**spara flöde**”

> [!TIP]
> Skapa separata flöden för att skicka automatiserade e-postmeddelanden till olika mottagare. Ändra mottagarens e-postadress i fältet ”till” och ämnesraden för e-post i fältet ”ämne” i dessa ytterligare flöden. Skapa nya runbooks i Azure Automation med anpassade PowerShell-skript (som med en ändring av Azure-prenumerations-ID) kan ytterligare anpassning av automatiserade scenarier, till exempel till exempel e-posta separat mottagare på automatisk justering rekommendationer för olika prenumerationer.
>

Ovanstående avslutar steg som krävs för att konfigurera e-delivery-jobbet arbetsflödet. Hela flödet som består av tre åtgärder som har skapats visas i följande bild.

![Visa automatisk justering e-postaviseringar flöde](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Testa flödet genom att klicka på ”**kör nu**” i det övre högra hörnet i fönstret flöde.

Statistik för att köra de automatiska jobb, som visar framgången för e-postmeddelanden som skickas ut, kan ses från fönstret Flow analytics.

![Köra flödet för automatisk justering e-postmeddelanden](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Flow analytics är användbart för att övervaka framgången för jobbkörningar, och om det behövs för felsökning.  När det gäller felsökning kan kanske du också vill granska PowerShell-skript körningsloggen är tillgängliga via Azure Automation-app.

Det slutgiltiga resultatet för automatiserade e-postmeddelandet liknar följande e-postmeddelande tas emot efter att skapa och köra den här lösningen:

![Exempel på e-resultat från automatisk justering e-postaviseringar](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Du kan justera utdata och formatering av automatiserade e-postmeddelandet efter dina behov genom att justera PowerShell-skriptet.

Du kan också anpassa lösningen för att skapa e-postmeddelanden baserat på en specifik justering händelse och till flera mottagare för flera prenumerationer och -databaser, beroende på dina anpassade scenarier.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur automatisk justering kan hjälpa dig att förbättra databasens prestanda, se [automatisk justering i Azure SQL Database](sql-database-automatic-tuning.md).
- För att aktivera automatisk justering i Azure SQL Database för att hantera din arbetsbelastning, se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill granska och tillämpa automatiska justeringsrekommendationer manuellt finns i [hitta och tillämpa prestandarekommendationer](sql-database-advisor-portal.md).
