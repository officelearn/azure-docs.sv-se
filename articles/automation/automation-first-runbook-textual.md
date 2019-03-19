---
title: Min första PowerShell Workflow-runbook i Azure Automation
description: Självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel text-runbook med hjälp av PowerShell Workflow.
keywords: powershell-arbetsflöde, powershell-arbetsflödesexempel, arbetsflöde powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2bc4d4034d63ed190f6964caa2bccf1ad8590a9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57833823"
---
# <a name="my-first-powershell-workflow-runbook"></a>Min första PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) i Azure Automation. Du börjar med en enkel runbook som du testar och publicerar medan vi förklarar hur du spårar statusen för runbook-jobbet. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Till sist göra du runbooken mer robust genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Du stoppar och startar den här datorn så att den inte får vara en virtuell dator i produktionsmiljön.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook

Börja med att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).

1. Klicka på **Runbooks** under **Processautomatisering** att öppna listan med runbooks.
1. Skapa en ny runbook genom att klicka på den **+ Lägg till en runbook** knappen och sedan **skapa en ny runbook**.
1. Ge runbooken namnet *MyFirstRunbook-Workflow*.
1. I det här fallet ska du skapa en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) så Välj **Powershell Workflow** för **runbooktyp**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. Den här genomgången skriver direkt i runbooken.

1. Din runbook är tom det obligatoriska *arbetsflöde* nyckelordet, namnet på din runbook och klammerparenteserna som encases hela arbetsflödet.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Skriv *Write-Output "Hello World."* mellan klammerparenteserna.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
1. Starta testet genom att klicka på **Starta**. Det här alternativet ska vara det enda aktiverade alternativet.
1. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.

   Jobbet har statusen startar som *i kö* som anger att det väntar på en runbook worker i molnet ska bli tillgänglig. Flyttas till *startar* när ett arbetsobjekt begär jobbet, och sedan *kör* när runbook-jobbet börjar köras.  

1. När runbook-jobbet är klart visas dess utdata. I ditt fall bör du se *Hello World*.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Det runbook-jobb som du har skapat är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I ditt fall behöver du en publicerad version ännu eftersom du precis har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Om du rullar åt vänster för att visa runbooken på den **Runbooks** nu den visar en **Redigeringsstatusen** av **publicerad**.
1. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-Workflow**.  
   Vi kan använda alternativen längs överkanten för att starta runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
1. du bara vill starta runbooken klickar **starta** och sedan **Ja** när du tillfrågas.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Ett Jobbfönster öppnas för runbook-jobbet som du skapade. Du kan stänga det här fönstret, men i det här fallet du lämna det öppet så att du kan titta på jobbets status.
1. Jobbets status visas i **jobbsammanfattning** och överensstämmer med de statusar som du såg när du testade runbooken.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret utdata öppnas och du kan se din *Hello World*.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  

1. Stäng utdatafönstret.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör endast se *Hello World* i utdata stream, men den här vyn kan visa andra strömmar för runbook-jobb, till exempel utförlig och fel om runbooken skriver till dem.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Stäng sidan strömmar och jobbsidan att återgå till sidan MyFirstRunbook.
1. Klicka på **jobb** att öppna sidan jobb för runbook. Den här sidan visas alla jobb som skapats av denna runbook. Du bör endast se ett jobb eftersom du bara körde jobbet en gång.

   ![Jobb](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Du kan klicka på det här jobbet för att öppna sidan för samma jobb som du visade när du startade runbooken. Denna åtgärd kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det kan inte göra detta dock om du har autentiserats med autentiseringsuppgifterna som avses den [krav](#prerequisites). du gör det med den **Connect-AzureRmAccount** cmdlet.

1. Öppna textredigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook-Workflow.
2. Du behöver inte den **Write-Output** rad längre, så gå vidare och ta bort den.
3. Placera markören på en tom rad mellan klammerparenteserna.
4. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** och **Login-AzureRmAccount** är nu alias för **Connect-AzureRMAccount**. Om den **Connect-AzureRMAccount** cmdlet inte finns kan du använda **Add-AzureRmAccount** eller **Login-AzureRmAccount**, eller så kan du [uppdatera dina moduler ](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna.

> [!NOTE]
> Du kan behöva [uppdatera dina moduler](automation-update-azure-modules.md) även om du just har skapat ett nytt automation-konto.

1. Klicka på **Testfönster** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Den här åtgärden bekräftar att autentiseringsuppgifterna är giltiga.

   ![Autentisera](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din runbook autentiseras med din Azure-prenumeration, kan du hantera resurser. du lägger till ett kommando för att starta en virtuell dator. Du kan välja valfri virtuell dator i din Azure-prenumeration och nu är du hårdkoda namnet i runbooken. Om du hanterar resurser över flera prenumerationer, måste du använda den **- AzureRmContext** parametern tillsammans med [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

1. Efter *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* att ange namn och resursgruppsnamnet för den virtuella datorn ska starta.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzureRmContext $AzureContext
   }
   ```

1. Spara runbooken och klicka sedan på **Testfönster** så att du kan testa den.
1. Starta testet genom att klicka på **Starta**. När testet är klart kontrollerar du att den virtuella datorn har startat.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken

din runbook startar för närvarande den virtuella datorn som du hårdkodade i runbooken, men det skulle vara mer användbart om du kunde ange den virtuella datorn när runbooken startar. Du lägger till indataparametrar för runbooken för att implementera den funktionen.

1. Lägg till parametrar för *VMName* och *ResourceGroupName* i runbooken och använd dessa variabler med cmdleten **Start-AzureRmVM** som i exemplet nedan.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Spara runbooken och öppna Testfönster. Du kan nu ange värden för de två indatavariablerna som finns i testet.
3. Stäng Testfönster.
4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
5. Stoppa den virtuella dator som du startade i föregående steg.
6. Starta runbooken genom att klicka på **Starta**. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-pass-params.png)

7. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.  

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
