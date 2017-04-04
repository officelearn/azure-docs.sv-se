---
title: "Min första PowerShell-runbook i Azure Automation | Microsoft Docs"
description: "En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel PowerShell-runbook."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "azure powershell, självstudier i powershell-skript, powershell-automation"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2017
ms.author: magoedte;sngun
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 4b32011b72acc647d4af44bb5ccbcaab408fb4d6
ms.lasthandoff: 03/27/2017


---
# <a name="my-first-powershell-runbook"></a>Min första PowerShell-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> 
> 

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) i Azure Automation. Vi börjar med en enkel runbook som vi testar och publicerar medan vi förklarar hur du spårar statusen för runbook-jobbet. Sedan ändrar vi vår runbook så att den hanterar Azure-resurser, i vårt exempel ska den starta en virtuell dator i Azure. Slutligen ska vi göra runbooken mer robust genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Krav
För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller <a href="/pricing/free-account/" target="_blank">[registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom vi ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook
Vi börjar genom att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.  
   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).
2. Öppna listan med runbookflöden genom att klicka på panelen med **runbookflöden**.<br><br> ![RunbooksControl](media/automation-first-runbook-textual-powershell/runbooks-control-tiles.png)  
3. Skapa en ny runbook genom att klicka på knappen **Lägg till en runbook** och sedan på **Skapa en ny runbook**.
4. Ge runbooken namnet *MyFirstRunbook-PowerShell*.
5. Eftersom vi ska skapa en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) väljer du **Powershell** för **Typ av runbook**.<br><br> ![Typ av runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken
Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här genomgången skriver vi direkt i runbooken.

1. Vår runbook är tom än så länge. Skriv *Write-Output "Hello World."*.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Spara runbooken genom att klicka på **Spara**.<br><br> ![Knappen Spara](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-save.png)  

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken
Innan vi publicerar runbooken så att den blir tillgänglig i produktionsmiljön vill vi testa den och kontrollera att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.<br><br> ![Testfönster](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-test.png)  
2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.  
   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
4. När runbook-jobbet är klart visas dess utdata. I detta fall bör du se *Hello World*.<br><br> ![Utdata i testfönstret](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken
Den runbook som vi har skapat är fortfarande i utkastläge. Vi måste publicera den innan vi kan köra den i produktion.  När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen.  I vårt fall har vi ingen publicerad version än eftersom vi precis har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.<br><br> ![Knappen Publicera](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-publish.png)  
2. Om du rullar åt vänster för att visa runbooken i fönstret **Runbooks** visas den nu med **redigeringsstatusen** **Publicerad**.
3. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-PowerShell**.  
   Vi kan använda alternativen längs överkanten för att starta runbooken, visa runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
4. Eftersom vi vill starta runbooken klickar vi på **Starta** och sedan på **OK** när bladet Starta runbook öppnas.<br><br> ![Knappen Starta](media/automation-first-runbook-textual-powershell/automation-runbook-controls-start.png)<br>    
5. Ett jobbfönster öppnas för runbook-jobbet som vi skapade. Vi kan stänga det här fönstret, men ska i stället lämna det öppet så att vi kan titta på jobbets status.
6. Jobbets status visas i **Jobbsammanfattning** och överensstämmer med de statusar som vi såg när vi testade runbooken.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret Utdata öppnas och *Hello World* visas.<br><br> ![Jobbutdata](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Stäng utdatafönstret.
9. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Vi bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbooken skriver till dem.<br><br> ![Alla loggar](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Stäng fönstret Strömmar och fönstret Jobb för att återgå till fönstret MyFirstRunbook-PowerShell.
11. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Vi bör endast se ett jobb i listan eftersom vi bara körde jobbet en gång.<br><br> ![Jobblista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Du kan klicka på det här jobbet för att öppna samma jobbfönster som vi visade när vi startade runbooken. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser
Vi har testat och publicerat vår runbook, men hittills gör den egentligen inget användbart. Vi vill att den ska hantera Azure-resurser. Dock kan den inte göra det såvida vi inte konfigurerar den att autentisera med hjälp av autentiseringsuppgifterna som avses i [kravavsnittet](#prerequisites). Vi gör det med cmdleten **Add-AzureRmAccount**.

1. Öppna textredigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook PowerShell.<br><br> ![Redigera runbook](media/automation-first-runbook-textual-powershell/automation-runbook-controls-edit.png)<br>   
2. Eftersom vi inte behöver raden **Write-Output** längre kan du ta bort den.
3. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Klicka på **Testfönster** så att vi kan testa runbooken.
5. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Detta bekräftar att autentiseringsuppgifterna är giltiga.<br><br> ![Autentisera](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator
Nu när våra runbook autentiseras med vår Azure-prenumeration kan vi hantera resurser. Vi ska lägga till ett kommando för att starta en virtuell dator. Du kan välja en virtuell dator i din Azure-prenumeration. Här kommer vi att hårdkoda namnet i runbooken.

1. Efter *Add-AzureRmAccount* skriver du *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* och anger namnet och resursgruppsnamnet för den virtuella dator som ska starta.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Spara runbooken och klicka sedan på **Testfönster** så att vi kan testa den.
3. Starta testet genom att klicka på **Starta**. När testet är klart kontrollerar du att den virtuella datorn har startat.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken
Vår runbook startar för närvarande den virtuella datorn som vi hårdkodade i runbooken, men det skulle vara mer praktiskt om vi angav den virtuella datorn när runbooken startar. Nu ska vi lägga till indataparametrar för runbooken för att implementera den funktionen.

1. Lägg till parametrar för *VMName* och *ResourceGroupName* i runbooken och använd dessa variabler med cmdleten **Start-AzureRmVM** som i exemplet nedan.  
   
   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
2. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som används i testet.
3. Stäng Testfönster.
4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
5. Stoppa den virtuella dator som du startade i föregående steg.
6. Starta runbooken genom att klicka på **Starta**. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.<br><br> ![Skicka parameter](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
7. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

## <a name="differences-from-powershell-workflow"></a>Skillnader från PowerShell Workflow
PowerShell-runbookflöden har samma livscykel, funktioner och hantering som runbookflöden för PowerShell-arbetsflöden, men det finns vissa skillnader och begränsningar:

1. PowerShell-runbooks körs snabbt jämfört med PowerShell Workflow-runbooks eftersom det inte krävs något kompileringssteg.
2. PowerShell Workflow-runbooks stöder kontrollpunkter. Användningen av kontrollpunkter gör att PowerShell Workflow kan återuppta körningen från valfri punkt i runbooken, till skillnad från PowerShell-runbooks som endast kan återuppta körningen från början.
3. PowerShell Workflow-runbooks stöder parallell och seriell körning medan PowerShell-runbooks endast kan köra kommandon seriellt.
4. I ett runbookflöde för ett PowerShell-arbetsflöde kan en aktivitet, ett kommando eller ett skriptblock ha sitt eget körningsutrymme, men i ett PowerShell-runbookflöde körs allt i ett skript i ett enda körningsutrymme. Det finns även vissa [syntaktiska skillnader](https://technet.microsoft.com/magazine/dn151046.aspx) mellan en intern PowerShell-runbook och en PowerShell Workflow-runbook.

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


