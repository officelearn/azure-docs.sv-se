---
title: "Min första PowerShell Workflow-runbook i Azure Automation | Microsoft Docs"
description: "Självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel text-runbook med hjälp av PowerShell Workflow."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "powershell-arbetsflöde, powershell-arbetsflödesexempel, arbetsflöde powershell"
ms.assetid: 0002d7f7-e2b5-46e3-b5eb-4596b84fd526
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2017
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 739f7c0fe0cca03d80fa8b4bdadbf93b5da72a73
ms.lasthandoff: 03/27/2017


---
# <a name="my-first-powershell-workflow-runbook"></a>Min första PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> 
> 

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) i Azure Automation. Vi börjar med en enkel runbook som vi testar och publicerar medan vi förklarar hur du spårar statusen för runbook-jobbet. Sedan ändrar vi vår runbook så att den hanterar Azure-resurser, i vårt exempel ska den starta en virtuell dator i Azure. Slutligen ska vi göra runbooken mer robust genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Krav
För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller <a href="/pricing/free-account/" target="_blank">[registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom vi ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook
Vi börjar genom att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.  
   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.<br><br> ![Runbook-kontroll](media/automation-first-runbook-textual/runbooks-control-tiles.png)
3. Skapa en ny runbook genom att klicka på knappen **Lägg till en runbook** och sedan på **Skapa en ny runbook**.
4. Ge runbooken namnet *MyFirstRunbook-Workflow*.
5. Eftersom vi ska skapa en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) väljer du **Powershell-arbetsflöde** för **Typ av runbook**.<br><br> ![Ny runbook](media/automation-first-runbook-textual/new-runbook-properties.png)
6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken
Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här genomgången ska vi skriva koden direkt i runbooken.

1. Vår runbook är tom så när som på det obligatoriska *workflow*-nyckelordet, namnet på runbooken och klammerparenteserna som ska omge hela arbetsflödet.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Skriv *Write-Output "Hello World."* mellan klammerparenteserna.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Spara runbooken genom att klicka på **Spara**.<br><br> ![Spara runbook](media/automation-first-runbook-textual/automation-runbook-edit-controls-save.png)

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken
Innan vi publicerar runbooken så att den blir tillgänglig i produktionsmiljön vill vi testa den och kontrollera att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.<br><br> ![Testfönster](media/automation-first-runbook-textual/automation-runbook-edit-controls-test.png)
2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.  
   Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
4. När runbook-jobbet är klart visas dess utdata. I detta fall bör du se *Hello World*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken
Den runbook som vi precis har skapat är fortfarande i utkastläge. Vi måste publicera den innan vi kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I vårt fall har vi ingen publicerad version än eftersom vi precis har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.<br><br> ![Publicera](media/automation-first-runbook-textual/automation-runbook-edit-controls-publish.png)
2. Om du rullar åt vänster för att visa runbooken i fönstret **Runbooks** visas den nu med **redigeringsstatusen** **Publicerad**.
3. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-Workflow**.  
   Vi kan använda alternativen längs överkanten för att starta runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
4. Eftersom vi bara vill starta runbooken klickar du på **Starta** och sedan på **Ja** när du uppmanas att göra det.<br><br> ![Starta runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Ett jobbfönster öppnas för runbook-jobbet som vi precis skapade. Vi kan stänga det här fönstret, men ska i stället lämna det öppna så att vi kan titta på jobbets status.
6. Jobbets status visas i **Jobbsammanfattning** och överensstämmer med de statusar som vi såg när vi testade runbooken.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret Utdata öppnas och *Hello World* visas.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Stäng utdatafönstret.
9. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Vi bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbooken skriver till dem.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Stäng fönstret Strömmar och fönstret Jobb för att återgå till fönstret MyFirstRunbook.
11. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Vi bör endast se ett jobb i listan eftersom vi bara körde jobbet en gång.<br><br> ![Jobb](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Du kan klicka på det här jobbet för att öppna samma jobbfönster som vi visade när vi startade runbooken. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser
Vi har testat och publicerat vår runbook, men hittills gör den egentligen inget användbart. Vi vill att den ska hantera Azure-resurser. Dock kan den inte göra det såvida vi inte konfigurerar den att autentisera med hjälp av autentiseringsuppgifterna som avses i [kravavsnittet](#prerequisites). Vi gör det med cmdleten **Add-AzureAccount**.

1. Öppna textredigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook-Workflow.<br><br> ![Redigera runbook](media/automation-first-runbook-textual/automation-runbook-controls-edit.png)
2. Eftersom vi inte behöver raden **Write-Output** längre kan du ta bort den.
3. Placera markören på en tom rad mellan klammerparenteserna.
4. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Klicka på **Testfönster** så att vi kan testa runbooken.
6. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Detta bekräftar att autentiseringsuppgifterna är giltiga.<br><br> ![Autentisera](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator
Nu när våra runbook autentiseras med vår Azure-prenumeration kan vi hantera resurser. Vi ska lägga till ett kommando för att starta en virtuell dator. Du kan välja en virtuell dator i din Azure-prenumeration. Här kommer vi att hårdkoda namnet i runbooken.

1. Efter *Add-AzureRmAccount* skriver du *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* och anger namnet och resursgruppsnamnet för den virtuella dator som ska starta.  

   ```
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Spara runbooken och klicka sedan på **Testfönster** så att vi kan testa den.
3. Starta testet genom att klicka på **Starta**. När testet är klart kontrollerar du att den virtuella datorn har startat.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken
Vår runbook startar för närvarande den virtuella datorn som vi hårdkodade i runbooken, men den skulle vara mer användbar om vi kunde ange den virtuella datorn när runbooken startar. Nu ska vi lägga till indataparametrar för runbooken för att implementera den funktionen.

1. Lägg till parametrar för *VMName* och *ResourceGroupName* i runbooken och använd dessa variabler med cmdleten **Start-AzureRmVM** som i exemplet nedan.

   ```
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Spara runbooken och öppna Testfönster. Observera att du nu kan ange värden för de två indatavariablerna som ska användas i testet.
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

