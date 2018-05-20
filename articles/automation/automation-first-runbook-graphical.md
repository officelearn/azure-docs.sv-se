---
title: Min första grafisk runbook i Azure Automation
description: En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel grafisk runbook.
keywords: runbook, runbook-mall, runbook-automation, azure runbook
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6a9e6bbf3bc507a4d45af03c791104c9490cd36f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="my-first-graphical-runbook"></a>Min första grafiska runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Den här självstudien beskriver steg för steg hur du skapar en [grafisk runbook](automation-runbook-types.md#graphical-runbooks) i Azure Automation. Du börjar med en enkel runbook som testar och publicerar och lär dig hur du spårar statusen för runbook-jobbet. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Därefter slutför du självstudien och gör runbook-jobbet stabilare genom att lägga till runbook-parametrar och villkorliga länkar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="create-runbook"></a>Skapa runbook

Börja med att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).

2. Välj **Runbooks** under **PROCESSHANTERING** för att öppna listan med runbook-jobb.
3. Skapa en ny runbook genom att välja **+ Lägg till en runbook**, klicka på **skapa en ny runbook**.
4. Ge din runbook namnet *MyFirstRunbook-Graphical*.
5. I det här fallet ska du skapa en [grafisk runbook](automation-graphical-authoring-intro.md). Välj därför **Grafisk** för **Typ av runbook**.<br> ![Ny runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klicka på **Skapa** för att skapa den nya runbooken och öppna den grafiska redigeraren.

## <a name="add-activities"></a>Lägg till aktiviteter

Du kan lägga till aktiviteter i din runbook med hjälp av bibliotekskontrollen till vänster i redigeraren. Du ska lägga till en **Write-Output**-cmdlet som returnerar text från runbook-jobbet.

1. I bibliotekskontrollen klickar du i sökrutan och skriver **Write-Output**. Sökresultatet visas på följande bild: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Rulla längst ned i listan. Du kan antingen högerklicka på **Write-Output** och välja **Lägg till på ytan** eller klicka på ellipsen bredvid cmdleten och välja **Lägg till på ytan**.
1. Klicka på aktiviteten **Write-Output** på arbetsytan. Den här åtgärden öppnar sidan konfiguration kontroll, där du kan konfigurera aktiviteten.
1. **Etiketten** tilldelas som standard samma namn som cmdleten, men du kan ändra den till något mer beskrivande. Ändra den till *Skriv Hello World på skärmen*.
1. Klicka på **Parametrar** för att ange värden för cmdletens parametrar.

   Vissa cmdlets har flera parameteruppsättningar och du måste välja vilken du vill använda. I detta fall har **Write-Output** bara en parameteruppsättning så du behöver inte välja någon.

1. Välj **InputObject**-parametern. Det här är parametern där du anger vilken text som ska skickas till utdataströmmen.
1. I listrutan **Datakälla** väljer du **PowerShell-uttryck**. Listrutan **Datakälla** innehåller olika källor som du använder för att fylla i ett parametervärde.

   Du kan använda utdata från sådana källor som en annan aktivitet, en Automation-tillgång eller ett PowerShell-uttryck. I det här fallet är utdata bara *Hello World*. Du kan använda ett PowerShell-uttryck och ange en sträng.<br>

1. I rutan **Uttryck** skriver du *"Hello World"* och klickar på **OK** två gånger för att återgå till arbetsytan.
1. Spara runbooken genom att klicka på **Spara**.

## <a name="test-the-runbook"></a>Testa runbooken

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Välj **Test rutan** att öppna sidan Test.
1. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
1. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas i rutan.

   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en runbook-arbetsroll gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.

1. När runbook-jobbet är klart visas dess utdata. I det här fallet visas *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Stäng sidan Test om du vill återgå till arbetsytan.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbook

Det runbook-jobb som du har skapat är fortfarande i utkastläge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Välj **publicera** publicera en runbook och sedan **Ja** när du tillfrågas.
1. Om du rulla åt vänster om du vill visa runbook i den **Runbooks** sidan visas en **redigering Status** av **publicerade**.
1. Bläddra till höger för att visa sidan för **MyFirstRunbook grafiska**.

   Vi kan använda alternativen längs överkanten för att starta runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.

1. Välj **starta** och sedan **Ja** när du uppmanas att starta runbook.
1. En sida i jobbet har öppnats för runbook-jobbet har skapats. Kontrollera att **Jobbstatus** är **Slutförd**.
1. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Den **utdata** sidan öppnas och du kan se den *Hello World* i fönstret.
1. Stänga sidan utdata.
1. Klicka på **alla loggar** att öppna sidan dataströmmar för runbook-jobbet. Du bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbook-jobbet skriver till dem.
1. Stäng alla loggar sidan och sidan jobbet om du vill gå tillbaka till sidan MyFirstRunbook grafiska.
1. Visa alla jobb för runbook Stäng den **jobbet** och välja **jobb** under **resurser**. Här visas alla jobb som skapats av det här runbook-jobbet. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.
1. Du kan klicka på det här jobbet för att öppna samma jobbfönster som du visade när du startade runbook-jobbet. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="create-variable-assets"></a>Skapa variabeln tillgångar

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Innan du konfigurerar runbook-jobbet för autentisering skapar du en variabel som ska lagra prenumerations-ID:t och referera till det när du har konfigurerat aktiviteten för autentisering i steg 6 nedan. Genom att ta med en referens till prenumerationskontexten kan du enkelt arbeta mellan flera prenumerationer. Kopiera prenumerations-ID:t från alternativet Prenumerationer vid navigeringsfönstret innan du fortsätter.

1. Automation-konton på sidan Välj **variabler** under **delade resurser**.
1. Välj **lägga till en variabel**.
1. I den nya variabeln sidan, i den **namn** ange **AzureSubscriptionId** och i den **värdet** ange ditt prenumerations-ID. Behåll *sträng* som **Typ** och standardvärdet för **Kryptering**.
1. Skapa variabeln genom att klicka på **Skapa**.

## <a name="add-authentication"></a>Lägg till autentisering

Nu när du har en variabel som ska innehålla prenumerations-ID:t kan du konfigurera runbook-jobbet för att autentisera med ”kör som”-autentiseringsuppgifterna som avses i [kravavsnittet](#prerequisites). Det gör du genom att lägga till Azure kör som-anslutningen **tillgången** och **Connect-AzureRmAccount** cmdlet till arbetsytan.

1. Gå tillbaka till din runbook och väljer **redigera** på sidan MyFirstRunbook grafiska.
1. Du behöver den **skriva Hello World till utdata** längre, så klickar du på ellipserna (...) och väljer **ta bort**.
1. Expandera **TILLGÅNGAR**, **Anslutningar** i bibliotekskontrollen och lägg till **AzureRunAsConnection** på arbetsytan genom att välja **Lägg till på ytan**.
1. Skriv i kontrollen biblioteket **Connect-AzureRmAccount** i textrutan Sök.

   > [!IMPORTANT]
   > **Lägg till AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När sökningen bibliotekets objekt, om du inte ser **Connect-AzureRMAccount**, kan du använda **Add-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

1. Lägg till **Connect-AzureRmAccount** till arbetsytan.
1. Hovra över **Hämta ”kör som”-anslutning** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra på pilen till **Connect-AzureRmAccount**. Pilen som du skapade är en *länk*. Runbook startat med **hämta kör som-anslutning** och kör sedan **Connect-AzureRmAccount**.<br> ![Skapa länk mellan aktiviteter](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. På arbetsytan, Välj **Connect-AzureRmAccount** och i fönstret kontrolltypen Configuration **inloggning till Azure** i den **etikett** textruta.
1. Klicka på **parametrar** och konfigurationssidan för aktiviteten Parameter visas.
1. **Ansluta AzureRmAccount** har flera parameteruppsättningar, så du måste välja en innan du kan ange parametervärden. Klicka på **Parameteruppsättning** och välj parameteruppsättningen **ServicePrincipalCertificate**.
1. När du har valt parameteruppsättningen visas parametrarna i aktiviteten parametern konfigurationssidan. Klicka på **APPLICATIONID**.<br> ![Lägg till Azure RM-kontoparametrar](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Parametervärdet på sidan Välj **aktivitetsutdata** för den **datakällan** och välj **hämta kör som-anslutning** i listan i den **fältet sökväg** textruta typen **ApplicationId**, och klicka sedan på **OK**. Du anger namnet på egenskapen för Fältsökväg eftersom aktiviteten matar ut ett objekt med flera egenskaper.
1. Klicka på **CERTIFICATETHUMBPRINT**, och värdet för parametern på sidan Välj **utdata för aktiviteten** för den **datakällan**. Välj **Hämta ”kör som”-anslutning** i listan. I textrutan **Fältsökväg** skriver du **CertificateThumbrprint** och klickar på **OK**.
1. Klicka på **SERVICEPRINCIPAL**, och värdet för parametern på sidan Välj **ConstantValue** för den **datakällan**, klickar du på alternativet **SANT**, och klicka sedan på **OK**.
1. Klicka på **TENANTID**, och värdet för parametern på sidan Välj **utdata för aktiviteten** för den **datakällan**. Välj **Hämta ”kör som”-anslutning** i listan. I textrutan **Fältsökväg** skriver du **TenantId** och klickar på **OK** två gånger.
1. Skriv **Set-AzureRmContext** i sökrutan i bibliotekskontrollen.
1. Lägg till **Ange AzureRmContext** på arbetsytan.
1. På arbetsytan väljer du **Set-AzureRmContext** och skriver **Ange prenumerations-ID** i textrutan **Etikett** i fönstret Konfigurationskontroll.
1. Klicka på **parametrar** och konfigurationssidan för aktiviteten Parameter visas.
1. **Set-AzureRmContext** har flera parameteruppsättningar så du måste välja en innan du kan ange parametervärden. Klicka på **Parameteruppsättning** och välj parameteruppsättningen **SubscriptionId**.
1. När du har valt parameteruppsättningen visas parametrarna i aktiviteten parametern konfigurationssidan. Klicka på **SubscriptionID**
1. På sidan parametervärdet väljer **Variabeltillgång** för den **datakällan** och välj **AzureSubscriptionId** i listan och klicka sedan på **OK** två gånger.
1. Hovra över **Inloggning i Azure** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till **Ange prenumerations-ID**.

Din runbook bör se ut ungefär så här nu: <br>![Konfiguration av runbook-autentisering](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Lägg till aktivitet om du vill starta en virtuell dator

Nu lägger du till en **Start-AzureRmVM**-aktivitet för att starta en virtuell dator. Du kan välja valfri virtuell dator i Azure-prenumerationen och sedan hårdkodar du namnet i cmdleten.

1. Skriv **StartsAzureRm** i sökrutan för bibliotekskontrollen.
2. Lägg till **Start-AzureRmVM** på arbetsytan och klicka på och dra det under **Ange prenumerations-ID**.
1. Hovra över **Ange prenumerations-ID** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till **Start-AzureRmVM**.
1. Välj **Start-AzureRmVM**. Klicka på **Parametrar** och sedan på **Parameteruppsättning** för att visa uppsättningarna för **Start-AzureRmVM**. Välj parameteruppsättningen **ResourceGroupNameParameterSetName**. **ResourceGroupName** och **Name** visas med utropstecken. Det betyder att de är obligatoriska parametrar. Observera också att båda förväntar strängvärden.
1. Välj **Name**. Välj **PowerShell-uttryck** för **Datakälla** och skriv namnet på den virtuella datorn (omgivet av dubbla citattecken) som du ska starta med det här runbook-jobbet. Klicka på **OK**.
1. Välj **ResourceGroupName**. Använd **PowerShell-uttryck** för **Datakälla** och skriv namnet på resursgruppen omgivet av dubbla citattecken. Klicka på **OK**.
1. Klicka på Testfönster så att du kan testa runbook-jobbet.
1. Starta testet genom att klicka på **Starta**. När det är klart kontrollerar du att den virtuella datorn har startat.

Din runbook bör se ut ungefär så här nu: <br>![Konfiguration av runbook-autentisering](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Lägg till ytterligare indataparametrar

Runbook-jobbet startar den virtuella datorn i resursgruppen som du angav i cmdleten **Start-AzureRmVM**. Runbook-jobbet skulle vara mer användbart om du kunde ange när det startar. Nu lägger du till indataparametrar för runbook-jobbet för att implementera den funktionen.

1. Öppna Redigeraren för grafiska genom att klicka på **redigera** på den **MyFirstRunbook grafiska** fönstret.
1. Välj **ingående och utgående** och sedan **lägga till indata** att öppna fönstret Runbookinmatningsparameter.
1. Ange *VMName* för **Namn**. Behåll *sträng* för **Typ**, men ändra **Obligatorisk** till *Ja*. Klicka på **OK**.
1. Skapa en andra obligatorisk indataparameter kallad *ResourceGroupName* och klicka sedan på **OK** för att stänga fönstret **Indata och utdata**.<br> ![Indataparametrar för Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Välj aktiviteten **Start-AzureRmVM** och klicka på **Parametrar**.
1. Ändra **Datakälla** för **Namn** till **Indata för runbook** och välj sedan **VMName**.
1. Ändra **Datakälla** för **ResourceGroupName** till **Indata för runbook** och välj sedan **ResourceGroupName**.<br> ![Start-AzureVM-parametrar](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som du använder i testet.
1. Stäng Testfönster.
1. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
1. Stoppa den virtuella dator som du startade i föregående steg.
1. Starta runbooken genom att klicka på **Starta**. Skriv **VMName** och **ResourceGroupName** för den virtuella datorn som du ska starta.
1. När runbooken har slutförts kontrollerar du att den virtuella datorn har startat.

## <a name="create-a-conditional-link"></a>Skapa en villkorlig länk

Nu ändrar du runbook-jobbet så att det endast försöker starta den virtuella datorn om den inte redan startats. Du gör detta genom att lägga till en **Get-AzureRmVM**-cmdlet till runbook-jobbet som hämtar den virtuella datorns status på instansnivå. Sedan lägger du till kodmodulen **Hämta status** för PowerShell Workflow med PowerShell-kodfragmentet för att kontrollera om den virtuella datorn körs eller är stoppad. En villkorlig länk från modulen **Hämta status** kör bara **Start-AzureRmVM** om körningsstatusen är Stoppad. Slutligen matar du ut ett meddelande som meddelar om den virtuella datorn startades eller inte med hjälp av PowerShell-cmdleten Write-Output.

1. Öppna **MyFirstRunbook grafiska** i grafiska redigerare.
1. Ta bort länken mellan **Ange prenumerations-ID** och **Start-AzureRmVM** genom att klicka på den och sedan trycka på *Del*.
1. Skriv **Get-AzureRm** i sökrutan i bibliotekskontrollen.
1. Lägg till **Get-AzureRmVM** på arbetsytan.
1. Välj **Get-AzureRmVM** och sedan **Parameteruppsättning** för att visa uppsättningarna för **Get-AzureRmVM**. Välj parameteruppsättningen **GetVirtualMachineInResourceGroupNameParamSet**. **ResourceGroupName** och **Name** visas med utropstecken. Det betyder att de är obligatoriska parametrar. Observera också att båda förväntar strängvärden.
1. Under **Datakälla** för **Namn** väljer du **Indata för runbook** och väljer sedan **VMName**. Klicka på **OK**.
1. Under **Datakälla** för **ResourceGroupName** väljer du **Indata för runbook** och väljer sedan **ResourceGroupName**. Klicka på **OK**.
1. Under **Datakälla** för **Status** väljer du **Konstant värde** och klickar sedan på **Sant**. Klicka på **OK**.
1. Skapa en länk från **Ange prenumerations-ID** till **Get-AzureRmVM**.
1. Expandera **Runbook-kontroll** i bibliotekskontrollen och lägg till **Kod** på arbetsytan.  
1. Skapa en länk från **Get-AzureRmVM** till **Kod**.  
1. Klicka på **Kod** och ändra etiketten i fönstret Konfiguration till **Hämta status**.
1. Välj **kod** parameter, och **redigerare** visas.  
1. Klistra in följande kodfragment i kodredigeraren:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Skapa en länk från **Hämta status** till **Start-AzureRmVM**.<br> ![Runbook med kodmodul](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Klicka på länken och ändra **Använd villkor** till **Ja** i fönstret Konfiguration. Observera att länken förvandlas till en streckad linje vilket betyder att målaktiviteten endast körs om villkoret är sant.  
1. För **Villkorsuttryck** skriver du *$ActivityOutput['Hämta status'] -eq "Stoppad"*. Nu körs **Start-AzureRmVM** bara om den virtuella datorn har stoppats.
1. Expandera **Cmdlets** och sedan **Microsoft.PowerShell.Utility** i bibliotekskontrollen.
1. Lägg till **Write-Output** på arbetsytan två gånger.
1. I den första **Write-Output**-kontrollen klickar du på **Parametrar** och ändrar värdet för **Etikett** till *Meddela att den virtuella datorn har startat*.
1. För **InputObject** ändrar du **Datakälla** till **PowerShell-uttryck** och skriver uttrycket *"$VMName har startat."*.
1. I den andra **Write-Output**-kontrollen klickar du på **Parametrar** och ändrar värdet för **Etikett** till *Meddela att det inte gick att starta den virtuella datorn*
1. För **InputObject** ändrar du **Datakälla** till **PowerShell-uttryck** och skriver uttrycket *"Det gick inte att starta $VMName."*.
1. Skapa en länk från **Start-AzureRmVM** till **Meddela att den virtuella datorn har startat** och **Meddela att det inte gick att starta den virtuella datorn**.
1. Välj länken till **Meddela att den virtuella datorn har startat** och ändra **Använd villkor** till **Sant**.
1. För **Villkorsuttryck** skriver du *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Nu körs den här Write-Output-kontrollen bara om den virtuella datorn har startats.
1. Välj länken till **Meddela att det inte gick att starta den virtuella datorn** och ändra **Använd villkor** till **Sant**.
1. För **Villkorsuttryck** skriver du *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true*. Nu körs den här Write-Output-kontrollen bara om den virtuella datorn inte har startats. Din runbook bör se ut som följande bild: <br> ![Runbook med Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Spara runbooken och öppna Testfönster.
1. Starta runbooken när den virtuella datorn är stoppad så bör den starta.

## <a name="next-steps"></a>Nästa steg

* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)

