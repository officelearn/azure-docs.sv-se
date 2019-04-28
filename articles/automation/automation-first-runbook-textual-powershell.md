---
title: Min första PowerShell-runbook i Azure Automation
description: En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel PowerShell-runbook.
keywords: azure powershell, självstudier i powershell-skript, powershell-automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b08e1489cf337360e838a3b5d5531fa2d4c0073b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694325"
---
# <a name="my-first-powershell-runbook"></a>Min första PowerShell-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) i Azure Automation. Du börjar med en enkel runbook som du testar och publicerar medan du lär dig hur du spårar statusen för runbook-jobbet. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Slutligen kan göra du runbooken mer robust genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-quickstart-create-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Du stoppar och startar den här datorn så att den inte får vara en virtuell dator i produktionsmiljön.

## <a name="create-new-runbook"></a>Skapa en ny runbook

Börja med att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.
2. Klicka på **Runbooks** under **Processautomatisering** att öppna listan med runbooks.
3. Skapa en ny runbook genom att klicka på den **+ Lägg till en runbook** knappen och sedan **skapa en ny runbook**.
4. Ge runbooken namnet *MyFirstRunbook-PowerShell*.
5. I det här fallet ska du skapa en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) så Välj **Powershell** för **runbooktyp**.
6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="add-code-to-the-runbook"></a>Lägg till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. Den här genomgången ska skriva du direkt i runbooken.

1. Din runbook är tom typ *Write-Output ”Hello World”.* i innehållsdelen av skriptet.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"> </a> Testa runbook

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.

   Jobbets första status som *i kö* som anger att det väntar på en runbook worker i molnet ska bli tillgänglig. Flyttas till *startar* när ett arbetsobjekt begär jobbet, och sedan *kör* när runbook-jobbet börjar köras.  

4. När runbook-jobbet är klart visas dess utdata. I ditt fall bör du se *Hello World*.

   ![Utdata i testfönstret](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbooken

Det runbook-jobb som du har skapat är fortfarande i utkastläge. Det måste publiceras innan du kan köra den i produktion.  När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I ditt fall behöver du en publicerad version ännu eftersom du precis har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Om du rullar åt vänster för att visa runbooken på den **Runbooks** nu den visar en **Redigeringsstatusen** av **publicerad**.
1. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-PowerShell**.  
   Vi kan använda alternativen längs överkanten för att starta runbooken, visa runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
1. Du vill starta runbooken, så klicka på **starta** och klicka sedan på **Ok** när sidan starta Runbook öppnas.
1. En jobbsidan öppnas för runbook-jobbet som du skapade. Du kan stänga det här fönstret, men i det här fallet du lämna det öppet så att du kan titta på jobbets status.
1. Jobbets status visas i **jobbsammanfattning** och överensstämmer med de statusar som du såg när du testade runbooken.

   ![Jobbsammanfattning](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. När runbookens status visas *slutförd*under **översikt** klickar du på **utdata**. Fönstret utdata öppnas och du kan se din *Hello World*.

   ![Jobbutdata](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Stäng sidan utdata.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör endast se *Hello World* i utdata stream, men dessa utdata kan visa andra strömmar för runbook-jobb, till exempel utförlig och fel om runbooken skriver till dem.

   ![Alla loggar](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Stäng sidan strömmar och jobbsidan att återgå till sidan MyFirstRunbook-PowerShell.
1. Under **information**, klickar du på **jobb** att öppna fönstret jobb för runbook. Den här sidan visas alla jobb som skapats av denna runbook. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.

   ![Jobblista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Du kan klicka på det här jobbet för att öppna samma jobbfönster som du visade när du startade runbook-jobbet. Denna åtgärd kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Lägg till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det går inte att göra det, men om du inte har den autentisera med en Kör som-anslutning som skapas automatiskt när du skapar ditt automation-konto. Du använder Kör som-anslutningen med den **Connect-AzureRmAccount** cmdlet. Om du hanterar resurser över flera prenumerationer kan du behöva använda den **- AzureRmContext** parametern tillsammans med [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process
   
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Öppna textredigeraren genom att klicka på **redigera** på sidan MyFirstRunbook-PowerShell.
1. Du behöver inte den **Write-Output** rad längre, så gå vidare och ta bort den.
1. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** och **Login-AzureRmAccount** är nu alias för **Connect-AzureRMAccount**. Om den **Connect-AzureRMAccount** cmdlet inte finns kan du använda **Add-AzureRmAccount** eller **Login-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation Hänsyn till de senaste versionerna.

1. Klicka på **Testfönster** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Det här resultatet bekräftar att kör som-kontot är giltigt.

   ![Autentisera](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Lägg till kod för att starta en virtuell dator

Nu när din runbook autentiseras med din Azure-prenumeration, kan du hantera resurser. du lägger till ett kommando för att starta en virtuell dator. Du kan välja valfri virtuell dator i din Azure-prenumeration och nu du hårdkoda namnet i runbooken.

1. Efter *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* att ange namn och resursgruppsnamnet för den virtuella datorn ska starta.  

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Spara runbooken och klicka sedan på **Testfönster** så att du kan testa den.
1. Starta testet genom att klicka på **Starta**. När det är klart kontrollerar du att den virtuella datorn har startat.

## <a name="add-an-input-parameter"></a>Lägga till en indataparameter

Din runbook startar för närvarande den virtuella datorn som du hårdkodade i runbooken, men det skulle vara mer användbart om du anger den virtuella datorn när runbooken startar. Du lägger till indataparametrar för runbooken för att implementera den funktionen.

1. Lägga till parametrar för *VMName* och *ResourceGroupName* i runbooken och Använd dessa variabler med den **Start-AzureRmVM** cmdlet som i följande exempel.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som används i testet.
1. Stäng Testfönster.
1. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
1. Stoppa den virtuella dator som du startade i föregående steg.
1. Klicka på **OK** att starta runbooken. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.<br><br> ![Skicka parameter](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
1. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

## <a name="differences-from-powershell-workflow"></a>Skillnader från PowerShell Workflow

PowerShell-runbookflöden har samma livscykel, funktioner och hantering som runbookflöden för PowerShell-arbetsflöden, men det finns vissa skillnader och begränsningar:

1. PowerShell-runbooks körs snabbt jämfört med PowerShell Workflow-runbooks eftersom det inte krävs något kompileringssteg.
2. PowerShell Workflow-runbooks stöder kontrollpunkter, användningen av kontrollpunkter, PowerShell Workflow kan återuppta körningen från valfri punkt i runbooken. PowerShell-runbooks kan bara återuppta från början.
3. PowerShell Workflow-runbooks stöder parallell och seriell körning. PowerShell-runbooks kan bara köra kommandon seriellt.
4. I en PowerShell Workflow-runbook, en aktivitet, ett kommando eller ett skript kan blockera ha sitt eget körningsutrymme. I en PowerShell-runbook körs allt i ett skript i ett enda körningsutrymme. Det finns även vissa [syntaktiska skillnader](https://technet.microsoft.com/magazine/dn151046.aspx) mellan en intern PowerShell-runbook och en PowerShell Workflow-runbook.

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
