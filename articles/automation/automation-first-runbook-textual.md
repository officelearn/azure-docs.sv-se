---
title: Min första PowerShell-arbetsflödesrunbook i Azure Automation
description: Självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel text-runbook med hjälp av PowerShell Workflow.
keywords: powershell-arbetsflöde, powershell-arbetsflödesexempel, arbetsflöde powershell
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 33e9b68973aa399123fa9e62a2d0eea77c55add0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="my-first-powershell-workflow-runbook"></a>Min första PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) i Azure Automation. Du börjar med en enkel runbook som du vill testa och publicera samtidigt som förklarar hur du spåra statusen för runbook-jobbet. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Till sist du runbook mer robusta genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Förutsättningar
För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Du kan stoppa och starta den här datorn så att den inte får vara en produktion VM.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook
Börja med att skapa en enkel runbook som matar ut texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).

1. Klicka på **Runbooks** under **Processautomatisering** att öppna listan över runbooks.
2. Skapa en ny runbook genom att klicka på den **+ Lägg till en runbook** knappen och sedan **skapa en ny runbook**.
3. Ge runbooken namnet *MyFirstRunbook-Workflow*.
4. I det här fallet ska du skapa en [PowerShell-arbetsflödesrunbook](automation-runbook-types.md#powershell-workflow-runbooks) så väljer **Powershell-arbetsflöde** för **runbooktyp**.
5. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken
Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. Den här genomgången skriver direkt till runbook.

1. Din runbook är tom med bara de nödvändiga *arbetsflöde* nyckelord, namnet på din runbook och klammerparenteserna som encases hela arbetsflödet.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Skriv *Write-Output "Hello World."* mellan klammerparenteserna.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken
Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.

   Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Flyttas till *Start* när en arbetsprocess anspråk jobbet, och sedan *kör* när runbook faktiskt börjar köras.  

1. När runbook-jobbet är klart visas dess utdata. Du bör se i ditt fall *Hello World*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
2. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken
Det runbook-jobb som du har skapat är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I ditt fall har du inte en publicerad version ännu eftersom du just har skapat en runbook.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
2. Om du rulla åt vänster om du vill visa runbook i den **Runbooks** fönstret nu visas en **redigering Status** av **publicerade**.
3. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-Workflow**.  
   Vi kan använda alternativen längs överkanten för att starta runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
4. du vill starta runbook så klicka på **starta** och sedan **Ja** när du tillfrågas.<br><br> ![Starta runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Ett jobb-fönstret har öppnats för runbook-jobbet som du skapade. Du kan stänga det här fönstret, men i det här fallet du lämna den öppen så att du kan titta på jobbets förlopp.
6. Jobbet har statusen visas i **jobbsammanfattning** och matchar status som du såg när du har testat runbook.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret utdata öppnas och du kan se din *Hello World*.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Stäng utdatafönstret.
9. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör se *Hello World* i utdata stream, men detta kan du visa andra dataströmmar för en runbook-jobb, till exempel utförlig och fel om runbook skriver till dem.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Stäng fönstret Strömmar och fönstret Jobb för att återgå till fönstret MyFirstRunbook.
11. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Du bör bara se ett jobb visas eftersom du bara körde jobbet en gång.<br><br> ![Jobb](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Du kan klicka på det här jobbet för att öppna fönstret för samma jobb som du har visat när du startade runbook. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser
Du har testat och publicerat din runbook, men hittills märkvärdigheter användbart. Du vill ha den hantera Azure-resurser. Det kommer inte att kunna göra det, men om du inte har den autentisera med de autentiseringsuppgifter som anges i den [krav](#prerequisites). Det gör du med den **Connect-AzureRmAccount** cmdlet.

1. Öppna textredigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook-Workflow.
2. Du behöver den **Write-Output** rad längre, och nu måste du ta bort den.
3. Placera markören på en tom rad mellan klammerparenteserna.
4. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Klicka på **Test fönstret** så att du kan testa din runbook.
6. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Detta bekräftar att autentiseringsuppgifterna är giltiga.<br><br> ![Autentisera](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator
Nu när din runbook autentiseras till din Azure-prenumeration, kan du hantera resurser. Du lägger till ett kommando för att starta en virtuell dator. Du kan välja en virtuell dator i din Azure-prenumeration och nu du hardcoding namn i runbook.

1. Efter *Connect-AzureRmAccount*, typen *Start AzureRmVM-namnet 'VMName' - ResourceGroupName 'NameofResourceGroup'* att ange namnet och resursgruppens namn för den virtuella datorn ska starta.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Spara runbook och klicka sedan på **Test fönstret** så att du kan testa den.
3. Starta testet genom att klicka på **Starta**. När testet är klart kontrollerar du att den virtuella datorn har startat.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken
din runbook startar den virtuella datorn som du hårdkodad i runbook, men det är mer användbar om du kan ange den virtuella datorn när runbook startas. Du lägger till indataparametrar runbook för att tillhandahålla funktionen.

1. Lägg till parametrar för *VMName* och *ResourceGroupName* i runbooken och använd dessa variabler med cmdleten **Start-AzureRmVM** som i exemplet nedan.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Spara runbooken och öppna Testfönster. Du kan nu ange värden för de två inkommande variabler som finns i testet.
3. Stäng Testfönster.
4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
5. Stoppa den virtuella dator som du startade i föregående steg.
6. Starta runbooken genom att klicka på **Starta**. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.<br><br> ![Starta runbook](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.  

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
