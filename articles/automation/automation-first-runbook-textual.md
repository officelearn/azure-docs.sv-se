---
title: Min första PowerShell Workflow-Runbook i Azure Automation
description: Självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel text-runbook med hjälp av PowerShell Workflow.
keywords: powershell-arbetsflöde, powershell-arbetsflödesexempel, arbetsflöde powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: cdaadcfa3108ca847443e100bc624c2458c34115
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365962"
---
# <a name="my-first-powershell-workflow-runbook"></a>Min första PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) i Azure Automation. Du börjar med en enkel Runbook som du testar och publicerar samtidigt som du förklarar hur du spårar statusen för Runbook-jobbet. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Slutligen gör du runbooken mer robust genom att lägga till Runbook-parametrar.

## <a name="prerequisites"></a>Krav

För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. du stoppar och startar datorn så att den inte ska vara en virtuell produktions dator.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook

Du börjar med att skapa en enkel Runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).

1. Öppna listan över runbooks genom att klicka på **Runbooks** under **process automatisering** .
1. Skapa en ny Runbook genom att klicka på knappen **+ Lägg till en Runbook** och **skapa sedan en ny Runbook**.
1. Ge runbooken namnet *MyFirstRunbook-Workflow*.
1. I det här fallet ska du skapa en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) så välj **PowerShell-arbetsflöde** för **Runbook-typ**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här genom gången skriver du direkt i runbooken.

1. Din Runbook är för närvarande Tom med endast det obligatoriska *arbets flödets* nyckelord, namnet på din Runbook och de klammerparenteser som är i hela arbets flödet.

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
1. Starta testet genom att klicka på **Starta**. Det här alternativet bör vara det enda aktiverade alternativet.
1. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.

   Jobbets status börjar i *kö* , vilket betyder att det väntar på att en Runbook Worker i molnet ska vara tillgänglig. Den flyttar till *Start* när en arbets tagare anlitar jobbet och *Kör* sedan när runbooken faktiskt börjar köras.

1. När runbook-jobbet är klart visas dess utdata. I så fall bör du se *Hello World*.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Det runbook-jobb som du har skapat är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I så fall har du inte någon publicerad version än eftersom du nyss skapade runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Om du rullar åt vänster för att Visa Runbook i fönstret **Runbooks** nu visas **redigerings statusen** **publicerad**.
1. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-Workflow**.
   Vi kan använda alternativen längs överkanten för att starta runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
1. du vill bara starta runbooken genom att klicka på **Start** och sedan på **Ja** när du uppmanas till det.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Ett jobb fönster öppnas för det Runbook-jobb som du har skapat. Du kan stänga det här fönstret, men i det här fallet lämnar du det öppet så att du kan se jobbets förlopp.
1. Jobbets status visas i **jobb Sammanfattning** och matchar de status värden som du såg när du testade runbooken.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret utdata öppnas och du kan se din *Hello World*.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Stäng utdatafönstret.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se *Hello World* i utdataströmmen, men den här vyn kan visa andra strömmar för ett Runbook-jobb, till exempel utförlig och fel, om runbooken skriver till dem.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Stäng sidan strömmar och jobb sidan för att återgå till sidan MyFirstRunbook.
1. Klicka på **jobb** för att öppna sidan jobb för denna Runbook. Den här sidan visar alla jobb som skapats av denna Runbook. Du bör bara se ett jobb i listan eftersom du bara körde jobbet en gång.

   ![Jobb](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Du kan klicka på det här jobbet för att öppna samma jobb sida som du visade när du startade runbooken. Med den här åtgärden kan du gå tillbaka i tiden och Visa information om alla jobb som har skapats för en viss Runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det kan du inte göra, om du inte har autentiserat med de autentiseringsuppgifter som anges i [kraven](#prerequisites). Det gör du med cmdleten **Connect-AzAccount** .

1. Öppna textredigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook-Workflow.
2. du behöver inte längre **Skriv resultatet** , så gå vidare och ta bort den.
3. Placera markören på en tom rad mellan klammerparenteserna.
4. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzAccount** och **login-AzAccount** är nu alias för **Connect-AzAccount**. Om cmdleten **Connect-AzAccount** inte finns kan du använda **Add-AzAccount** eller **login-AzAccount**, eller så kan du [Uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna.

> [!NOTE]
> Du kan behöva [Uppdatera dina moduler](automation-update-azure-modules.md) även om du precis har skapat ett nytt Automation-konto.

1. Klicka på **test fönster** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Den här åtgärden bekräftar att autentiseringsuppgiften är giltig.

   ![Autentisera](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din Runbook autentiseras till din Azure-prenumeration kan du hantera resurser. du lägger till ett kommando för att starta en virtuell dator. Du kan välja en virtuell dator i din Azure-prenumeration och nu hårdkoda du namnet i runbooken. Om du hanterar resurser över flera prenumerationer måste du använda parametern **-AzContext** tillsammans med [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Efter *Connect-AzAccount*skriver du *Start-AzVM-Name "VMName"-ResourceGroupName "NameofResourceGroup"* och anger namn och resurs grupp namn för den virtuella datorn som ska startas.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Spara runbooken och klicka sedan på **test fönster** så att du kan testa den.
1. Starta testet genom att klicka på **Starta**. När det är klart kontrollerar du att den virtuella datorn har startat.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken

din Runbook startar för närvarande den virtuella datorn som du hårdkodad i runbooken, men det skulle vara mer användbart om du kan ange den virtuella datorn när runbooken startas. Du lägger till indataparametrar till Runbook för att tillhandahålla den funktionen.

1. Lägg till parametrar för *VMName* och *ResourceGroupName* i runbooken och Använd dessa variabler med cmdleten **Start-AzVM** som i exemplet nedan.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två variablerna som finns i testet.
3. Stäng Testfönster.
4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
5. Stoppa den virtuella dator som du startade i föregående steg.
6. Starta runbooken genom att klicka på **Starta**. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-pass-params.png)

7. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
