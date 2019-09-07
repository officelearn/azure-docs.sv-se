---
title: Min första PowerShell-Runbook i Azure Automation
description: En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel PowerShell-runbook.
keywords: azure powershell, självstudier i powershell-skript, powershell-automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae9daaf797d3d82200ee094b63bad1f5c1ff68cc
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743825"
---
# <a name="my-first-powershell-runbook"></a>Min första PowerShell-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) i Azure Automation. Du börjar med en enkel Runbook som du testar och publicerar medan du lär dig hur du spårar statusen för Runbook-jobbet. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Slutligen gör du runbooken mer robust genom att lägga till Runbook-parametrar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-quickstart-create-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Du stoppar och startar datorn så att den inte ska vara en virtuell produktions dator.
* Du kan behöva [Uppdatera dina Azure-moduler](automation-update-azure-modules.md) baserat på de cmdlets du använder.

## <a name="create-new-runbook"></a>Skapa ny Runbook

Du börjar med att skapa en enkel Runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.
2. Öppna listan över runbooks genom att klicka på **Runbooks** under **process automatisering** .
3. Skapa en ny Runbook genom att klicka på knappen **+ Lägg till en Runbook** och **skapa sedan en ny Runbook**.
4. Ge runbooken namnet *MyFirstRunbook-PowerShell*.
5. I det här fallet ska du skapa en PowerShell- [Runbook](automation-runbook-types.md#powershell-runbooks) så välj **PowerShell** som **Runbook-typ**.
6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="add-code-to-the-runbook"></a>Lägg till kod i Runbook

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här genom gången skriver du direkt i runbooken.

1. Din Runbook är tom för tillfället, Skriv *Write-output "Hello World".* i innehållsdelen av skriptet.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a> Testa runbooken

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.

   Jobbets status börjar i *kö* , vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Den flyttar till *Start* när en arbets tagare anlitar jobbet och *Kör* sedan när runbooken faktiskt börjar köras.

4. När runbook-jobbet är klart visas dess utdata. I så fall bör du se *Hello World*.

   ![Utdata i testfönstret](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbooken

Det runbook-jobb som du har skapat är fortfarande i utkastläge. Den måste publiceras innan du kan köra den i produktion.  När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I så fall har du inte någon publicerad version än eftersom du nyss skapade runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Om du rullar åt vänster för att Visa Runbook i fönstret **Runbooks** nu visas **redigerings statusen** **publicerad**.
1. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-PowerShell**.
   Vi kan använda alternativen längs överkanten för att starta runbooken, visa runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
1. Du vill starta runbooken, så klicka på **Start** och sedan på **OK** när sidan starta Runbook öppnas.
1. En jobb sida öppnas för det Runbook-jobb som du skapade. Du kan stänga det här fönstret, men i det här fallet lämnar du det öppet så att du kan se jobbets förlopp.
1. Jobbets status visas i **jobb Sammanfattning** och matchar de status värden som du såg när du testade runbooken.

   ![Jobbsammanfattning](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. När Runbook-statusen *har slutförts*klickar du på **utdata**under **Översikt** . Fönstret utdata öppnas och du kan se din *Hello World*.

   ![Jobbutdata](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Stäng sidan utdata.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se *Hello World* i utdataströmmen, men utdata kan visa andra strömmar för ett Runbook-jobb, till exempel utförlig och fel, om Runbook skriver till dem.

   ![Alla loggar](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Stäng sidan strömmar och jobb sidan för att återgå till MyFirstRunbook-PowerShell-sidan.
1. Klicka på **jobb** under **information**för att öppna fönstret jobb för denna Runbook. Den här sidan visar alla jobb som skapats av denna Runbook. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.

   ![Jobblista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Du kan klicka på det här jobbet för att öppna samma jobbfönster som du visade när du startade runbook-jobbet. Med den här åtgärden kan du gå tillbaka i tiden och Visa information om alla jobb som har skapats för en viss Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Lägg till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det går inte att göra om du inte har autentiserat med en kör som-anslutning som skapas automatiskt när du skapar ett Automation-konto. Du använder kör som-anslutningen med cmdleten **Connect-AzureRmAccount** . Om du hanterar resurser över flera prenumerationer måste du använda parametern **-AzureRmContext** tillsammans med [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Öppna text redigeraren genom att klicka på **redigera** på MyFirstRunbook-PowerShell-sidan.
1. Du behöver inte längre **Skriv resultatet** , så gå vidare och ta bort den.
1. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** och **login-AzureRmAccount** är nu alias för **Connect-AzureRmAccount**. Om cmdleten **Connect-AzureRMAccount** inte finns kan du använda **Add-AzureRMAccount** eller **login-AzureRMAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto till de senaste versionerna.

1. Klicka på **test fönster** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Det här resultatet bekräftar att kör som-kontot är giltigt.

   ![Autentisera](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Lägg till kod för att starta en virtuell dator

Nu när din Runbook autentiseras till din Azure-prenumeration kan du hantera resurser. Du lägger till ett kommando för att starta en virtuell dator. Du kan välja vilken virtuell dator som helst i din Azure-prenumeration och nu hårdkoda du det namnet i runbooken.

1. Efter *Connect-AzureRmAccount*skriver du *Start-AzureRmVM-Name "VMName"-ResourceGroupName "NameofResourceGroup"* och anger namn och resurs grupp namn för den virtuella datorn som ska startas.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Spara runbooken och klicka sedan på **test fönster** så att du kan testa den.
1. Starta testet genom att klicka på **Starta**. När det är klart kontrollerar du att den virtuella datorn har startat.

## <a name="add-an-input-parameter"></a>Lägg till en indataparameter

Din Runbook startar för närvarande den virtuella datorn som du hårdkodad i runbooken, men det skulle vara mer användbart om du anger den virtuella datorn när runbooken startas. Du lägger till indataparametrar till Runbook för att tillhandahålla den funktionen.

1. Lägg till parametrar för *VMName* och *ResourceGroupName* i runbooken och Använd dessa variabler med cmdleten **Start-AzureRmVM** som i följande exempel.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som används i testet.
1. Stäng Testfönster.
1. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
1. Stoppa den virtuella dator som du startade i föregående steg.
1. Starta runbooken genom att klicka på **OK** . Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.<br><br> ![Skicka parameter](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

## <a name="differences-from-powershell-workflow"></a>Skillnader från PowerShell Workflow

PowerShell-runbookflöden har samma livscykel, funktioner och hantering som runbookflöden för PowerShell-arbetsflöden, men det finns vissa skillnader och begränsningar:

1. PowerShell-runbooks körs snabbt jämfört med PowerShell Workflow-runbooks eftersom det inte krävs något kompileringssteg.
2. PowerShell Workflow-Runbooks stöder kontroll punkter, med hjälp av kontroll punkter, Runbook-flöden för PowerShell-arbetsflöden kan återupptas från valfri punkt i runbooken. PowerShell-Runbooks kan bara fortsätta från början.
3. PowerShell Workflow-Runbooks stöder parallell och seriell körning. PowerShell-Runbooks kan bara köra kommandon seriellt.
4. I en PowerShell Workflow-Runbook kan en aktivitet, ett kommando eller ett skript block ha sin egen körnings utrymme. I en PowerShell-runbook körs allt i ett skript i en enda körnings utrymme. Det finns även vissa [syntaktiska skillnader](https://technet.microsoft.com/magazine/dn151046.aspx) mellan en intern PowerShell-runbook och en PowerShell Workflow-runbook.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](/powershell/scripting/overview).
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
