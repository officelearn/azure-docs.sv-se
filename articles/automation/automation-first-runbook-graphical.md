---
title: "Min första grafisk runbook i Azure Automation | Microsoft Docs"
description: "En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel grafisk runbook."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: runbook, runbook-mall, runbook-automation, azure runbook
ms.assetid: dcb88f19-ed2b-4372-9724-6625cd287c8a
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/17/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: d06052099acbb5544581ef3e8e2007dd751a7544
ms.contentlocale: sv-se
ms.lasthandoff: 09/13/2017

---
# <a name="my-first-graphical-runbook"></a>Min första grafiska runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Den här självstudien beskriver steg för steg hur du skapar en [grafisk runbook](automation-runbook-types.md#graphical-runbooks) i Azure Automation.  Vi börjar med en enkel runbook som testar och publicerar medan vi förklarar hur du spårar statusen för runbook-jobbet.  Sedan ändrar vi vår runbook så att den hanterar Azure-resurser, i vårt exempel ska den starta en virtuell dator i Azure.  Därefter slutför vi självstudien och gör runbook-jobbet stabilare genom att lägga till runbook-parametrar och villkorliga länkar.

## <a name="prerequisites"></a>Krav
För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom vi ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook
Vi börjar med att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.  
   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot.  Du bör redan ha vissa tillgångar.  De flesta av dessa är de moduler som ingår automatiskt i ett nytt Automation-konto.  Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).
2. Öppna listan med runbookflöden genom att klicka på panelen med **runbookflöden**.<br> ![Runbook-kontroll](media/automation-first-runbook-graphical/runbooks-resources-tile.png)
3. Skapa en ny runbook genom att klicka på knappen **Lägg till en runbook** och sedan på **Skapa en ny runbook**.
4. Ge din runbook namnet *MyFirstRunbook-Graphical*.
5. Eftersom du ska skapa en [grafisk runbook](automation-graphical-authoring-intro.md) väljer du **Grafisk** för **Typ av runbook**.<br> ![Ny runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klicka på **Skapa** för att skapa den nya runbooken och öppna den grafiska redigeraren.

## <a name="step-2---add-activities-to-the-runbook"></a>Steg 2 – Lägga till aktiviteter i runbooken
Du kan lägga till aktiviteter i din runbook med hjälp av bibliotekskontrollen till vänster i redigeraren.  Vi ska lägga till en **Write-Output**-cmdlet som returnerar text från runbooken.

1. I bibliotekskontrollen klickar du i sökrutan och skriver **Write-Output**.  Sökresultaten visas nedan. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2. Rulla längst ned i listan.  Du kan antingen högerklicka på **Write-Output** och välja **Lägg till på ytan** eller klicka på ellipsen bredvid cmdleten och välja **Lägg till på ytan**.
3. Klicka på aktiviteten **Write-Output** på arbetsytan.  När du gör det öppnas bladet Konfigurationskontroll där du kan konfigurera aktiviteten.
4. **Etiketten** tilldelas som standard samma namn som cmdleten men vi kan ändra den till något mer beskrivande. Ändra den till *Skriv Hello World på skärmen*.
5. Klicka på **Parametrar** för att ange värden för cmdletens parametrar.  
   Vissa cmdlets har flera parameteruppsättningar och du måste välja vilken du vill använda. I detta fall har **Write-Output** bara en parameteruppsättning så du behöver inte välja någon. <br> ![Write-Output-egenskaper](media/automation-first-runbook-graphical/write-output-properties-b.png)
6. Välj **InputObject**-parametern.  Det här är parametern där vi anger vilken text som ska skickas till utdataströmmen.
7. I listrutan **Datakälla** väljer du **PowerShell-uttryck**.  Listrutan **Datakälla** innehåller olika källor som du använder för att fylla i ett parametervärde.  
   Du kan använda utdata från sådana källor som en annan aktivitet, en Automation-tillgång eller ett PowerShell-uttryck.  I detta fall vill vi bara mata ut texten *Hello World*. Vi kan använda ett PowerShell-uttryck och ange en sträng.
8. I rutan **Uttryck** skriver du *"Hello World"* och klickar på **OK** två gånger för att återgå till arbetsytan.<br> ![PowerShell-uttryck](media/automation-first-runbook-graphical/expression-hello-world.png)
9. Spara runbooken genom att klicka på **Spara**.<br> ![Spara runbook](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken
Innan vi publicerar runbooken så att den blir tillgänglig i produktionsmiljön vill vi testa den och kontrollera att den fungerar som den ska.  När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna bladet Test genom att klicka på **Testfönster**.<br> ![Testfönstret](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2. Starta testet genom att klicka på **Starta**.  Detta bör vara det enda aktiverade alternativet.
3. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas i rutan.  
   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig.  Därefter ändras statusen till *Startar* när en runbook-arbetsroll gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
4. När runbook-jobbet är klart visas dess utdata. I detta fall bör du se *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5. Stäng bladet Test för att återgå till arbetsytan.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken
Den runbook som vi har skapat är fortfarande i utkastläge. Vi måste publicera den innan vi kan köra den i produktion.  När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen.  I vårt fall har vi ingen publicerad version än eftersom vi precis har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.<br> ![Publicera](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2. Om du rullar åt vänster för att visa runbook-jobbet på bladet med **runbook-jobb** visas **redigeringsstatusen** **Publicerad**.
3. Bläddra till höger för att visa bladet för **MyFirstRunbook**.  
   Vi kan använda alternativen längs överkanten för att starta runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
4. Eftersom vi bara vill starta runbooken klickar du på **Starta** och sedan på **Ja** när du uppmanas att göra det.<br> ![Starta runbook](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5. Ett jobblad öppnas för runbook-jobbet som vi skapade.  Vi kan stänga det här bladet, men ska i stället lämna det öppet så att vi kan se jobbets förlopp.
6. Jobbets status visas i **Jobbsammanfattning** och överensstämmer med de statusar som vi såg när vi testade runbooken.<br> ![Jobbsammanfattning](media/automation-first-runbook-graphical/runbook-job-summary.png)
7. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Bladet **Utdata** öppnas och vi kan se *Hello World* i fönstret.<br> ![Jobbsammanfattning](media/automation-first-runbook-graphical/runbook-job-output.png)  
8. Stäng utdatabladet.
9. Klicka på **Alla loggar** för att öppna bladet Strömmar för runbook-jobbet.  Vi bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbooken skriver till dem.<br> ![Jobbsammanfattning](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10. Stäng bladet Alla loggar och bladet Jobb för att gå tillbaka till bladet MyFirstRunbook.
11. Öppna bladet Jobb för den här runbooken genom att klicka på **Jobb**.  Här visas alla jobb som skapats av det här runbook-jobbet. Vi bör endast se ett jobb i listan eftersom vi bara körde jobbet en gång.<br> ![Jobb](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12. Du kan klicka på det här jobbet för att öppna samma jobbfönster som vi visade när vi startade runbooken.  På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="step-5---create-variable-assets"></a>Steg 5 – Skapa variabler för tillgångar
Vi har testat och publicerat vår runbook, men hittills gör den egentligen inget användbart. Vi vill att den ska hantera Azure-resurser.  Innan vi konfigurerar runbook-jobbet för autentisering skapar vi en variabel som ska lagra prenumerations-ID:t och referera till det när vi har konfigurerat aktiviteten för autentisering i steg 6 nedan.  Genom att ta med en referens till prenumerationskontexten kan du enkelt arbeta mellan flera prenumerationer.  Kopiera prenumerations-ID:t från alternativet Prenumerationer vid navigeringsfönstret innan du fortsätter.  

1. Klicka på panelen **Tillgångar** på bladet Automation-konton. Bladet **Tillgångar** öppnas.
2. Klicka på panelen **Variabler** på bladet Tillgångar.
3. Klicka på **Lägg till en variabel** på bladet Variabler.<br>![Automation-variabel](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. Skriv **AzureSubscriptionId** i rutan **Namn** på bladet Ny variabel och skriv ditt prenumerations-ID i rutan **Värde**.  Behåll *sträng* som **Typ** och standardvärdet för **Kryptering**.  
5. Skapa variabeln genom att klicka på **Skapa**.  

## <a name="step-6---add-authentication-to-manage-azure-resources"></a>Steg 6 – Lägga till autentisering för att hantera Azure-resurser
Nu när vi har en variabel som ska innehålla vårt prenumerations-ID kan vi konfigurerar vår runbook för att autentisera med ”Kör som”-autentiseringsuppgifterna som avses i [kravavsnittet](#prerequisites).  Vi gör det genom att lägga till **Add-AzureRMAccount**-cmdleten och **tillgången** för Azures ”kör som”-anslutning på arbetsytan.  

1. Öppna den grafiska redigeraren genom att klicka på **Redigera** på bladet MyFirstRunbook.<br> ![Redigera runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2. Eftersom vi inte behöver **Skriv Hello World på skärmen** längre högerklickar du på den och väljer **Ta bort**.
3. Expandera **Anslutningar** i bibliotekskontrollen och lägg till **AzureRunAsConnection** på arbetsytan genom att välja **Lägg till på ytan**.
4. På arbetsytan väljer du **AzureRunAsConnection** och skriver **Hämta ”kör som”-anslutning** i textrutan **Etikett** i fönstret Konfigurationskontroll.  Det här är anslutningen
5. Skriv **Add-AzureRmAccount** i sökrutan i bibliotekskontrollen.
6. Lägg till **Add-AzureRmAccount** på arbetsytan.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7. Hovra över **Hämta ”kör som”-anslutning** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till **Add-AzureRmAccount**.  Pilen som du skapade är en *länk*.  Runbook-jobbet startar med **Hämta ”kör som”-anslutning** och kör sedan **Add-AzureRmAccount**.<br> ![Skapa länk mellan aktiviteter](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8. På arbetsytan väljer du **Add-AzureRmAccount** och skriver **Logga in i Azure** i textrutan **Etikett** i fönstret Konfigurationskontroll.
9. Klicka på **Parametrar**. Bladet Konfiguration av aktivitetsparameter öppnas.
10. **Add-AzureRmAccount** har flera parameteruppsättningar så vi måste välja en innan vi kan ange parametervärden.  Klicka på **Parameteruppsättning** och välj parameteruppsättningen **ServicePrincipalCertificatewithSubscriptionId**.
11. När du har valt parameteruppsättningen visas parametrarna på bladet Konfiguration av aktivitetsparameter.  Klicka på **APPLICATIONID**.<br> ![Lägg till Azure RM-kontoparametrar](media/automation-first-runbook-graphical/add-azurermaccount-params.png)
12. På bladet Parametervärde väljer du **Aktivitetsutdata** för **Datakälla** och väljer **Hämta ”kör som”-anslutning** i listan. I textrutan **Fältsökväg** skriver du **ApplicationId** och klickar sedan på **OK**.  Vi anger namnet på egenskapen för Fältsökväg eftersom aktiviteten matar ut ett objekt med flera egenskaper.
13. Klicka på **CERTIFICATETHUMBPRINT** och välj **Aktivitetsutdata** för **Datakälla** på bladet Parametervärde.  Välj **Hämta ”kör som”-anslutning** i listan. I textrutan **Fältsökväg** skriver du **CertificateThumbrprint** och klickar på **OK**.
14. Klicka på **SERVICEPRINCIPAL** och välj **ConstantValue** för **Datakälla** på bladet Parametervärde. Klicka på alternativet **Sant** och sedan på **OK**.
15. Klicka på **TENANTID** och välj **Aktivitetsutdata** för **Datakälla** på bladet Parametervärde.  Välj **Hämta ”kör som”-anslutning** i listan. I textrutan **Fältsökväg** skriver du **TenantId** och klickar på **OK** två gånger.  
16. Skriv **Set-AzureRmContext** i sökrutan i bibliotekskontrollen.
17. Lägg till **Ange AzureRmContext** på arbetsytan.
18. På arbetsytan väljer du **Set-AzureRmContext** och skriver **Ange prenumerations-ID** i textrutan **Etikett** i fönstret Konfigurationskontroll.
19. Klicka på **Parametrar**. Bladet Konfiguration av aktivitetsparameter öppnas.
20. **Set-AzureRmContext** har flera parameteruppsättningar så vi måste du välja en innan vi kan ange parametervärden.  Klicka på **Parameteruppsättning** och välj parameteruppsättningen **SubscriptionId**.  
21. När du har valt parameteruppsättningen visas parametrarna på bladet Konfiguration av aktivitetsparameter.  Klicka på **SubscriptionID**
22. Välj **Variabel tillgång** för **Datakälla** på bladet Parametervärde och välj **AzureSubscriptionId** i listan. Avsluta med att klicka på **OK** två gånger.   
23. Hovra över **Inloggning i Azure** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till **Ange prenumerations-ID**.

Din runbook bör se ut ungefär så här nu: <br>![Konfiguration av runbook-autentisering](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Steg 7 – Lägga till aktivitet för att starta en virtuell dator
Nu lägger vi till en **Start-AzureRmVM**-aktivitet för att starta en virtuell dator.  Du kan välja valfri virtuell dator i Azure-prenumerationen och sedan hårdkodar du namnet i cmdleten.

1. Skriv **StartsAzureRm** i sökrutan för bibliotekskontrollen.
2. Lägg till **Start-AzureRmVM** på arbetsytan och klicka på och dra det under **Ange prenumerations-ID**.
3. Hovra över **Ange prenumerations-ID** tills en cirkel visas längst ned i formen.  Klicka på cirkeln och dra pilen till **Start-AzureRmVM**.
4. Välj **Start-AzureRmVM**.  Klicka på **Parametrar** och sedan på **Parameteruppsättning** för att visa uppsättningarna för **Start-AzureRmVM**.  Välj parameteruppsättningen **ResourceGroupNameParameterSetName**. Observera att **ResourceGroupName** och **Name** visas med utropstecken.  Det betyder att de är obligatoriska parametrar.  Observera också att båda förväntar strängvärden.
5. Välj **Name**.  Välj **PowerShell-uttryck** för **Datakälla** och skriv namnet på den virtuella datorn, omgivet av dubbla citattecken, som vi ska starta med det här runbook-jobbet.  Klicka på **OK**.<br>![Parametervärde för Name i Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6. Välj **ResourceGroupName**. Använd **PowerShell-uttryck** för **Datakälla** och skriv namnet på resursgruppen omgivet av dubbla citattecken.  Klicka på **OK**.<br> ![Start-AzureRmVM-parametrar](media/automation-first-runbook-graphical/startazurermvm-params.png)
7. Klicka på Testfönster så att vi kan testa runbooken.
8. Starta testet genom att klicka på **Starta**.  När det är klart kontrollerar du att den virtuella datorn har startat.

Din runbook bör se ut ungefär så här nu: <br>![Konfiguration av runbook-autentisering](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters-to-the-runbook"></a>Steg 8 – Lägga till ytterligare indataparametrar för runbooken
Vårt runbook-jobb startar den virtuella datorn i resursgruppen som vi angav i cmdleten **Start-AzureRmVM**.  Vårt runbook-jobb skulle vara mer användbart om vi kunde ange när runbook-jobbet startar.  Nu lägger vi till indataparametrar för runbook-jobbet för att implementera den funktionen.

1. Öppna den grafiska redigeraren genom att klicka på **Redigera** i fönstret **MyFirstRunbook**.
2. Klicka på **Indata och utdata** och sedan på **Lägg till indata** för att öppna fönstret Indataparameter för runbook.<br> ![Indata och utdata för runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Ange *VMName* för **Namn**.  Behåll *sträng* för **Typ**, men ändra **Obligatorisk** till *Ja*.  Klicka på **OK**.
4. Skapa en andra obligatorisk indataparameter kallad *ResourceGroupName* och klicka sedan på **OK** för att stänga fönstret **Indata och utdata**.<br> ![Indataparametrar för Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Välj aktiviteten **Start-AzureRmVM** och klicka på **Parametrar**.
6. Ändra **Datakälla** för **Namn** till **Indata för runbook** och välj sedan **VMName**.<br>
7. Ändra **Datakälla** för **ResourceGroupName** till **Indata för runbook** och välj sedan **ResourceGroupName**.<br> ![Start-AzureVM-parametrar](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Spara runbooken och öppna Testfönster.  Observera att du nu kan ange värden för de två indatavariablerna som du använder i testet.
9. Stäng Testfönster.
10. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
11. Stoppa den virtuella dator som du startade i föregående steg.
12. Starta runbooken genom att klicka på **Starta**.  Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.<br> ![Starta runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

## <a name="step-9---create-a-conditional-link"></a>Steg 9 – Skapa en villkorlig länk
Nu ändrar vi runbook-jobbet så att det endast försöker starta den virtuella datorn om den inte redan startats.  Du gör detta genom att lägga till en **Get-AzureRmVM**-cmdlet till runbook-jobbet som hämtar den virtuella datorns status på instansnivå. Sedan lägger du till kodmodulen **Hämta status** för PowerShell Workflow med PowerShell-kodfragmentet för att kontrollera om den virtuella datorn körs eller är stoppad.  En villkorlig länk från modulen **Hämta status** kör bara **Start-AzureRmVM** om körningsstatusen är Stoppad.  Slutligen matar vi ut ett meddelande som meddelar om den virtuella datorn startades eller inte med hjälp av PowerShell-cmdleten Write-Output.

1. Öppna **MyFirstRunbook** i den grafiska redigeraren.
2. Ta bort länken mellan **Ange prenumerations-ID** och **Start-AzureRmVM** genom att klicka på den och sedan trycka på *Del*.
3. Skriv **Get-AzureRm** i sökrutan i bibliotekskontrollen.
4. Lägg till **Get-AzureRmVM** på arbetsytan.
5. Välj **Get-AzureRmVM** och sedan **Parameteruppsättning** för att visa uppsättningarna för **Get-AzureRmVM**.  Välj parameteruppsättningen **GetVirtualMachineInResourceGroupNameParamSet**.  Observera att **ResourceGroupName** och **Name** visas med utropstecken.  Det betyder att de är obligatoriska parametrar.  Observera också att båda förväntar strängvärden.
6. Under **Datakälla** för **Namn** väljer du **Indata för runbook** och väljer sedan **VMName**.  Klicka på **OK**.
7. Under **Datakälla** för **ResourceGroupName** väljer du **Indata för runbook** och väljer sedan **ResourceGroupName**.  Klicka på **OK**.
8. Under **Datakälla** för **Status** väljer du **Konstant värde** och klickar sedan på **Sant**.  Klicka på **OK**.  
9. Skapa en länk från **Ange prenumerations-ID** till **Get-AzureRmVM**.
10. Expandera **Runbook-kontroll** i bibliotekskontrollen och lägg till **Kod** på arbetsytan.  
11. Skapa en länk från **Get-AzureRmVM** till **Kod**.  
12. Klicka på **Kod** och ändra etiketten i fönstret Konfiguration till **Hämta status**.
13. Välj parametern **Kod**. Bladet **Kodredigerare** visas.  
14. Klistra in följande kodfragment i kodredigeraren:
    
     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```
15. Skapa en länk från **Hämta status** till **Start-AzureRmVM**.<br> ![Runbook med kodmodul](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. Klicka på länken och ändra **Använd villkor** till **Ja** i fönstret Konfiguration.   Observera att länken förvandlas till en streckad linje vilket betyder att målaktiviteten endast körs om villkoret är sant.  
17. För **Villkorsuttryck** skriver du *$ActivityOutput['Hämta status'] -eq "Stoppad"*.  Nu körs **Start-AzureRmVM** bara om den virtuella datorn har stoppats.
18. Expandera **Cmdlets** och sedan **Microsoft.PowerShell.Utility** i bibliotekskontrollen.
19. Lägg till **Write-Output** på arbetsytan två gånger.<br> ![Runbook med Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. I den första **Write-Output**-kontrollen klickar du på **Parametrar** och ändrar värdet för **Etikett** till *Meddela att den virtuella datorn har startat*.
21. För **InputObject** ändrar du **Datakälla** till **PowerShell-uttryck** och skriver uttrycket *"$VMName har startat."*.
22. I den andra **Write-Output**-kontrollen klickar du på **Parametrar** och ändrar värdet för **Etikett** till *Meddela att det inte gick att starta den virtuella datorn*
23. För **InputObject** ändrar du **Datakälla** till **PowerShell-uttryck** och skriver uttrycket *"Det gick inte att starta $VMName."*.
24. Skapa en länk från **Start-AzureRmVM** till **Meddela att den virtuella datorn har startat** och **Meddela att det inte gick att starta den virtuella datorn**.
25. Välj länken till **Meddela att den virtuella datorn har startat** och ändra **Använd villkor** till **Sant**.
26. För **Villkorsuttryck** skriver du *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*.  Nu körs den här Write-Output-kontrollen bara om den virtuella datorn har startats.
27. Välj länken till **Meddela att det inte gick att starta den virtuella datorn** och ändra **Använd villkor** till **Sant**.
28. För **Villkorsuttryck** skriver du *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true*.  Nu körs den här Write-Output-kontrollen bara om den virtuella datorn inte har startats.
29. Spara runbooken och öppna Testfönster.
30. Starta runbooken när den virtuella datorn är stoppad så bör den starta.

## <a name="next-steps"></a>Nästa steg
* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)


