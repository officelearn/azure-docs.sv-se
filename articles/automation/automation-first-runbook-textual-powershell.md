---
title: Min första PowerShell-runbook i Azure Automation
description: En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel PowerShell-runbook.
keywords: azure powershell, självstudier i powershell-skript, powershell-automation
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f5d2f3634545001dc6dc1419530223b5a1a85a3
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435799"
---
# <a name="my-first-powershell-runbook"></a>Min första PowerShell-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) i Azure Automation. Du börjar med en enkel runbook som du testar och publicerar medan du lär dig hur du spårar statusen för runbook-jobbet. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Slutligen kan göra du runbooken mer robust genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Förutsättningar
För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook
Börja med att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).

1. Klicka på **Runbooks** under **Processautomatisering** att öppna listan med runbooks.
2. Skapa en ny runbook genom att klicka på den **+ Lägg till en runbook** knappen och sedan **skapa en ny runbook**.
3. Ge runbooken namnet *MyFirstRunbook-PowerShell*.
4. I det här fallet ska du skapa en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) så Välj **Powershell** för **runbooktyp**.
5. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken
Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. Den här genomgången ska skriva du direkt i runbooken.

1. Din runbook är tom typ *Write-Output ”Hello World”.* i innehållsdelen av skriptet.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken
Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.

   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig. Flyttas till *startar* när ett arbetsobjekt begär jobbet, och sedan *kör* när runbook-jobbet börjar köras.  

1. När runbook-jobbet är klart visas dess utdata. I ditt fall bör du se *Hello World*.<br><br> ![Utdata i testfönstret](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken
Det runbook-jobb som du har skapat är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion.  När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I ditt fall behöver du en publicerad version ännu eftersom du precis har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
2. Om du rullar åt vänster för att visa runbooken på den **Runbooks** nu den visar en **Redigeringsstatusen** av **publicerad**.
3. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-PowerShell**.  
   Vi kan använda alternativen längs överkanten för att starta runbooken, visa runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
4. Du vill starta runbooken, så klicka på **starta** och klicka sedan på **Ok** när sidan starta Runbook öppnas.
5. En jobbsidan öppnas för runbook-jobbet som du skapade. Du kan stänga det här fönstret, men i det här fallet du lämna det öppet så att du kan titta på jobbets status.
6. Jobbets status visas i **jobbsammanfattning** och överensstämmer med de statusar som du såg när du testade runbooken.<br><br> ![Jobbsammanfattning](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. När runbookens status visas *slutförd*under **översikt** klickar du på **utdata**. Fönstret utdata öppnas och du kan se din *Hello World*.<br><br> ![Jobbutdata](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Stäng sidan utdata.
9. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbook-jobbet skriver till dem.<br><br> ![Alla loggar](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Stäng sidan strömmar och jobbsidan att återgå till sidan MyFirstRunbook-PowerShell.
11. Under **information**, klickar du på **jobb** att öppna fönstret jobb för runbook. Här visas alla jobb som skapats av den här runbooken. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.<br><br> ![Jobblista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Du kan klicka på det här jobbet för att öppna samma jobbfönster som du visade när du startade runbook-jobbet. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser
Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det går inte att göra det, men om du inte har den autentisera med autentiseringsuppgifterna som avses den [krav](#prerequisites). du gör det med den **Connect-AzureRmAccount** cmdlet.

1. Öppna textredigeraren genom att klicka på **redigera** på sidan MyFirstRunbook-PowerShell.
2. Du behöver inte den **Write-Output** rad längre, så gå vidare och ta bort den.
3. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** och **Login-AzureRmAccount** är nu alias för **Connect-AzureRMAccount**. Om den **Connect-AzureRMAccount** cmdlet inte finns kan du använda **Add-AzureRmAccount** eller **Login-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation Hänsyn till de senaste versionerna.

4. Klicka på **Testfönster** så att du kan testa runbooken.
5. Starta testet genom att klicka på **Starta**. När testet är klart visas utdata som liknar de nedan, med grundläggande information från ditt konto. Detta bekräftar att autentiseringsuppgifterna är giltiga.<br><br> ![Autentisera](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator
Nu när din runbook autentiseras med din Azure-prenumeration, kan du hantera resurser. du lägger till ett kommando för att starta en virtuell dator. Du kan välja valfri virtuell dator i din Azure-prenumeration och nu du hårdkoda namnet i runbooken.

1. Efter *Connect-AzureRmAccount*, typ *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* att ange namn och resursgruppsnamnet för den virtuella datorn ska starta.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Spara runbooken och klicka sedan på **Testfönster** så att du kan testa den.
3. Starta testet genom att klicka på **Starta**. När testet är klart kontrollerar du att den virtuella datorn har startat.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken
Din runbook startar för närvarande den virtuella datorn som du hårdkodade i runbooken, men det skulle vara mer användbart om du anger den virtuella datorn när runbooken startar. Du lägger till indataparametrar för runbooken för att implementera den funktionen.

1. Lägga till parametrar för *VMName* och *ResourceGroupName* i runbooken och Använd dessa variabler med den **Start-AzureRmVM** cmdlet som i följande exempel.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som används i testet.
2. Stäng Testfönster.
3. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
4. Stoppa den virtuella dator som du startade i föregående steg.
5. Klicka på **OK** att starta runbooken. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.<br><br> ![Skicka parameter](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

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

