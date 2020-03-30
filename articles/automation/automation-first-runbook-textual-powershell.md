---
title: Min första PowerShell-runbook i Azure Automation
description: En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel PowerShell-runbook.
keywords: azure powershell, självstudier i powershell-skript, powershell-automation
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367320"
---
# <a name="my-first-powershell-runbook"></a>Min första PowerShell-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) i Azure Automation. Börja med en enkel runbook som du kan testa och publicera, samtidigt som du lär dig hur du spårar status för runbook-jobbet. Ändra sedan runbooken för att faktiskt hantera Azure-resurser, i det här fallet starta en virtuell Azure-dator. Slutför självstudien för att göra runbooken mer robust genom att lägga till runbook-parametrar.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-quickstart-create-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör det inte vara en virtuell produktionsdator.
* Om det behövs [importerar du Azure-moduler](shared-resources/modules.md) eller [uppdateringsmoduler](automation-update-azure-modules.md) baserat på de cmdlets som du använder.

## <a name="differences-from-powershell-workflow-runbooks"></a>Skillnader från PowerShell-arbetsflödeskörningsböcker

PowerShell-runbooks har samma livscykel, funktioner och hantering som PowerShell Workflow runbooks. Det finns dock vissa skillnader och begränsningar.

| Egenskap  | PowerShell-runböcker | PowerShell-arbetsflödeskörningsböcker |
| ------ | ----- | ----- |
| Hastighet | Kör snabbt eftersom de inte använder ett kompileringssteg. | Spring långsammare. |
| Kontrollpunkter | Stöd inte för kontrollpunkter. En PowerShell-runbook kan bara återuppta åtgärden från början. | Använd kontrollpunkter, som gör att en arbetsbok kan återupptas från vilken punkt som helst. |
| Körning av kommando | Endast stöd för seriell körning. | Stöd för både seriell och parallell körning.|
| Runspace | En enda runspace kör allt i ett skript. | Ett separat runspace kan användas för en aktivitet, ett kommando eller ett skriptblock. |

Utöver dessa skillnader har PowerShell-runbooks vissa [syntaktiska skillnader](https://technet.microsoft.com/magazine/dn151046.aspx) från PowerShell Workflow runbooks.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook

Börja med att skapa en enkel `Hello World`runbook som matar ut texten .

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbooks** under **Process Automation** om du vill öppna listan över runbooks.
3. Skapa en ny runbook genom att välja **Skapa en runbook**.
4. Ge runbooken namnet **MyFirstRunbook-PowerShell**.
5. I det här fallet ska du skapa en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks). Välj **PowerShell** för **runbooktyp**.
6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. För den här självstudien ska du skriva kod direkt i runbooken.

1. Runbooken är för närvarande tom. Skriv `Write-Output "Hello World"` in skriptets brödtext.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Steg 3 - Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktionen bör du testa den för att se till att den fungerar som den ska. När du testar en runbook körs dess utkastversion och du kan visa dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3. Observera att ett [runbook-jobb](automation-runbook-execution.md) skapas och att dess status visas i fönstret.

   Jobbstatusen börjar `Queued`som , vilket anger att jobbet väntar på att en runbook-arbetare i molnet ska bli tillgänglig. Statusen ändras till `Starting` när en anställd gör anspråk på jobbet. Slutligen blir `Running` statusen när runbooken faktiskt börjar köras.

4. När runbook-jobbet är klart visar testfönstret utdata. I det här `Hello World`fallet ser du .

   ![Utdata i testfönstret](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Runbook som du har skapat är fortfarande i utkastläge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Bläddra åt vänster för att visa runbooken på sidan Runbooks och observera att värdet **för redigeringsstatus** är inställt på **Publicerad**.
1. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-PowerShell**.
   
   Alternativen överst gör att du kan starta runbooken nu, schemalägga en framtida starttid eller skapa en [webhook](automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.
1. Välj **Start** och sedan **Ja** när du uppmanas att starta runbooken. 
1. Ett jobbfönster öppnas för det runbook-jobb som har skapats. Även om du kan stänga den här rutan lämnar du den öppen just nu så att du kan titta på jobbets förlopp. Jobbstatusen visas i **Projektsammanfattning**och möjliga statusar beskrivs för testning av runbooken.

   ![Jobbsammanfattning](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. När runbook-statusen visar `Completed`klickar du på **Utdata** `Hello World` för att öppna utdatasidan, där du kan se den visas.

   ![Jobbutdata](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Stäng utdatasidan.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör `Hello World` bara se i utdataströmmen.

    Observera att fönstret Strömmar kan visa andra strömmar för ett runbook-jobb, till exempel Utförliga och Felströmmar, om runbooken skriver till dem.

   ![Alla loggar](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Stäng fönstret Strömmar och jobbfönstret för att återgå till sidan **MyFirstRunbook-PowerShell.**
1. Klicka på **Jobb** under **Information**om du vill öppna sidan Jobb för den här runbooken. På den här sidan visas alla jobb som skapas av runbooken. Du bör bara se ett jobb som anges, eftersom du bara har kört jobbet en gång.

   ![Jobblista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Klicka på jobbnamnet om du vill öppna samma jobbfönster som du visade när du startade runbooken. Använd det här fönstret om du vill visa information om alla jobb som skapats för runbooken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. För att kunna göra detta måste runbooken kunna autentisera med det Kör som-konto som skapades automatiskt när du skapade ditt Automation-konto.

Som visas i exemplet nedan görs run as-anslutningen med cmdleten [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Om du hanterar resurser för flera prenumerationer `AzContext` måste du använda parametern med [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> För `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är `Connect-AzAccount`alias för . Du kan använda dessa cmdlets eller så kan du [uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du just har skapat ett nytt Automation-konto.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Öppna textredigeraren genom att klicka på **Redigera** på sidan **MyFirstRunbook-PowerShell.**
1. Du behöver inte `Write-Output` linjen längre. Bara gå vidare och ta bort den.
1. Skriv in eller kopiera och klistra in följande kod, som hanterar autentiseringen med ditt Automation Run As-konto.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Klicka på **Testfönstret** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta**. När den är klar bör du se utdata som liknar följande och visa grundläggande information från ditt konto. Det här utdata bekräftar att körningskontot är giltigt.

   ![Autentisera](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din runbook autentiserar till din Azure-prenumeration kan du hantera resurser. Nu ska vi lägga till ett kommando för att starta en virtuell dator. Du kan välja vilken virtuell dator som helst i din Azure-prenumeration och bara hårdkoda det namnet i runbook för tillfället.

1. I runbook-skriptet lägger du till [Start-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) för att starta den virtuella datorn. Som visas nedan startar cmdleten en `VMName` virtuell dator med `ResourceGroupName`namnet och med en resursgrupp med namnet .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Spara runbooken och klicka sedan på **Testfönstret** så att du kan testa den.
1. Klicka på **Start** för att påbörja testet. När den är klar kontrollerar du att den virtuella datorn har startat.

## <a name="step-7---add-an-input-parameter"></a>Steg 7 - Lägga till en indataparameter

Runbooken startar för närvarande den virtuella datorn som du hårdkodade i runbooken. Runbooken blir mer användbar om du anger den virtuella datorn när runbooken startas. Nu ska vi lägga till indataparametrar i runbooken för att tillhandahålla den funktionen.

1. I textredigeraren `Start-AzVM` ändrar du cmdleten `VMName` så `ResourceGroupName`att variablerna för parametrarna och . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som du använder i testet.
1. Stäng Testfönster.
1. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
1. Stoppa den virtuella datorn som du startade tidigare.
1. Starta runbooken genom att klicka på **Starta**. 
1. Skriv in värdena för **VMNAME** och **RESOURCEGROUPNAME** för den virtuella datorn som du ska starta och klicka sedan på **OK**.<br><br> ![Skicka parameter](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. När runbooken är klar kontrollerar du att den virtuella datorn har startats.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](/powershell/scripting/overview).
* Kom igång med grafiska runbooks finns i [Min första grafiska runbook](automation-first-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell Workflow runbooks finns i [Min första PowerShell-arbetsflödeskörningsbok](automation-first-runbook-textual.md).
* Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation-runbooktyper](automation-runbook-types.md).
* Mer information om supportfunktionen för PowerShell-skript finns i [Stöd för Inbyggt PowerShell-skript i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
