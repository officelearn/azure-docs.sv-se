<properties
    pageTitle="Min första PowerShell-runbook i Azure Automation | Microsoft Azure"
    description="En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel PowerShell-runbook."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="azure powershell, powershell script tutorial, powershell automation"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/02/2016"
    ms.author="magoedte;sngun"/>

# Min första PowerShell-runbook

> [AZURE.SELECTOR] - [Grafiskt](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell-arbetsflöde](automation-first-runbook-textual.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) i Azure Automation. Vi börjar med en enkel runbook som vi testar och publicerar medan vi förklarar hur du spårar statusen för runbook-jobbet. Sedan ändrar vi vår runbook så att den hanterar Azure-resurser, i vårt exempel ska den starta en virtuell dator i Azure. Sedan ska vi göra runbooken mer robust genom att lägga till runbook-parametrar.

## Krav

För att kunna genomföra den här kursen behöver du följande:

-   En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller <a href="/pricing/free-account/" target="_blank">[registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
-   Ett [Automation-konto för Azure](automation-security-overview.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
-   En virtuell dator i Azure. Eftersom vi ska stoppa och starta den här datorn bör den inte finnas i produktionsmiljön.

## Steg 1 – Skapa en ny runbook

Vi börjar genom att skapa en enkel runbook som visar texten *Hello World*.

1.  Öppna ditt Automation-konto på Azure-portalen.  
    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).
2.  Öppna listan med runbooks genom att klicka på panelen **Runbooks**.  
    ![Runbook-kontroll](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3.  Skapa en ny runbook genom att klicka på knappen **Lägg till en runbook** och sedan på **Skapa en ny runbook**.
4.  Ge runbooken namnet *MyFirstRunbook-PowerShell*.
5.  Eftersom vi ska skapa en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) väljer du **Powershell** för **Typ av runbook**.  
    ![Typ av runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6.  Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, runbooks och resurser från bibliotekskontrollen och lägga till dem i runbooken med eventuella relaterade parametrar. I den här genomgången ska vi skriva koden direkt i runbooken.

1.  Vår runbook är tom än så länge. Skriv *Write-Output "Hello World."*.  
    ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2.  Spara runbooken genom att klicka på **Spara**.  
    ![Knappen Spara](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## Steg 3 – Testa runbooken

Innan vi publicerar runbooken så att den blir tillgänglig i produktionsmiljön vill vi testa den och kontrollera att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1.  Öppna testfönstret genom att klicka på **Testfönster**.  
    ![Testfönster](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2.  Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
3.  Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.  
    Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
4.  När runbook-jobbet är klart visas dess utdata. I detta fall bör du se *Hello World*.  
    ![Utdata i testfönstret](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5.  Gå tillbaka till arbetsytan genom att stänga testfönstret.

## Steg 4 – Publicera och starta runbooken

Den runbook som vi precis har skapat är fortfarande i utkastläge. Vi måste publicera den innan vi kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I vårt fall har vi ingen publicerad version än eftersom vi precis har skapat runbooken.

1.  Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.  
    ![Knappen Publicera](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2.  Om du rullar åt vänster för att visa runbooken i fönstret **Runbooks** visas den nu med **redigeringsstatusen** **Publicerad**.
3.  Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-PowerShell**.  
    Vi kan använda alternativen längs överkanten för att starta runbooken, visa runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
4.  Eftersom vi bara vill starta runbooken klickar vi på **Starta** och sedan på **OK** när bladet Starta runbook öppnas.  
    ![Knappen Starta](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5.  Ett jobbfönster öppnas för runbook-jobbet som vi precis skapade. Vi kan stänga det här fönstret, men ska i stället lämna det öppna så att vi kan titta på jobbets status.
6.  Jobbets status visas i **Jobbsammanfattning** och överensstämmer med de statusar som vi såg när vi testade runbooken.  
    ![Jobbsammanfattning](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7.  När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret Utdata öppnas och *Hello World* visas.  
    ![Jobbutdata](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8.  Stäng utdatafönstret.
9.  Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Vi bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbooken skriver till dem.  
    ![Alla loggar](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10. Stäng fönstret Strömmar och fönstret Jobb för att återgå till fönstret MyFirstRunbook-PowerShell.
11. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Vi bör endast se ett jobb i listan eftersom vi bara körde jobbet en gång.  
    ![Jobblista](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12. Du kan klicka på det här jobbet för att öppna samma jobbfönster som vi visade när vi startade runbooken. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Vi har testat och publicerat vår runbook, men hittills gör den egentligen inget användbart. Vi vill att den ska hantera Azure-resurser. Dock kan den inte göra det såvida vi inte konfigurerar den att autentisera med hjälp av autentiseringsuppgifterna som avses i [kravavsnittet](#prerequisites). Vi gör det med cmdleten **Add-AzureRmAccount**.

1.  Öppna textredigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook PowerShell.  
    ![Redigera runbooken](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2.  Eftersom vi inte behöver raden **Write-Output** längre kan du ta bort den.
3.  Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt ”Kör som”-konto för Automation:

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ``` 
<br>
4.  Klicka på **Testfönster** så att vi kan testa runbooken.
5.  Starta testet genom att klicka på **Starta**. När testet är klart visas utdata med grundläggande information från ditt konto. Detta bekräftar att autentiseringsuppgifterna är giltiga. <br> ![Autentisera](media/automation-first-runbook-textual-powershell/runbook-auth-results.png)

## Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när våra runbook autentiseras med vår Azure-prenumeration kan vi hantera resurser. Vi ska lägga till ett kommando för att starta en virtuell dator. Du kan välja valfri virtuell dator i Azure-prenumerationen och vi ska hårdkoda namnet i cmdleten.

1.  Efter *Add-AzureRmAccount* skriver du *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* och anger namnet och resursgruppsnamnet för den virtuella dator som ska starta.  
    
    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID `$Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint 
     Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
     ```
<br>
2.  Spara runbooken och klicka sedan på **Testfönster** så att vi kan testa den.
3.  Starta testet genom att klicka på **Starta**. När testet är klart kontrollerar du att den virtuella datorn har startat.

## Steg 7 – Lägga till en indataparameter i runbooken

Vår runbook startar för närvarande den virtuella datorn som vi hårdkodade i runbooken, men den skulle vara mer användbar om vi kunde ange den virtuella datorn när runbooken startar. Nu ska vi lägga till indataparametrar för runbooken för att implementera den funktionen.

1.  Lägg till parametrar för *VMName* och *ResourceGroupName* i runbooken och använd dessa variabler med cmdleten **Start-AzureRmVM** som i exemplet nedan.  
    
    ```
    Param(
       [string]$VMName,
       [string]$ResourceGroupName
    )
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID `$Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint 
     Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
     ```
<br> 
2.  Spara runbooken och öppna Testfönster. Observera att du nu kan ange värden för de två indatavariablerna som ska användas i testet.
3.  Stäng Testfönster.
4.  Klicka på **Publicera** för att publicera den nya versionen av runbooken.
5.  Stoppa den virtuella dator som du startade i föregående steg.
6.  Starta runbooken genom att klicka på **Starta**. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.  
    ![Skicka parameter](media/automation-first-runbook-textual-powershell/automation-pass-params.png)  
7.  När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

## Skillnader från PowerShell Workflow

PowerShell-runbooks har samma livscykel, funktioner och hantering som PowerShell Workflow-runbooks, men det finns vissa skillnader och begränsningar:

1.  PowerShell-runbooks körs snabbt jämfört med PowerShell Workflow-runbooks eftersom det inte krävs något kompileringssteg.
2.  PowerShell Workflow-runbooks stöder kontrollpunkter. Användningen av kontrollpunkter gör att PowerShell Workflow kan återuppta körningen från valfri punkt i runbooken, till skillnad från PowerShell-runbooks som endast kan återuppta körningen från början.
3.  PowerShell Workflow-runbooks stöder parallell och seriell körning medan PowerShell-runbooks endast kan köra kommandon seriellt.
4.  I en PowerShell Workflow-runbook kan en aktivitet, ett kommando eller ett skriptblock ha sitt eget körningsutrymme, men i en PowerShell-runbook körs allt i ett skript i ett enda körningsutrymme. Det finns även vissa [syntaktiska skillnader](https://technet.microsoft.com/magazine/dn151046.aspx) mellan en intern PowerShell-runbook och en PowerShell Workflow-runbook.

## Nästa steg

-   Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
-   Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
-   Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
-   Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)



<!--HONumber=jun16_HO2-->


