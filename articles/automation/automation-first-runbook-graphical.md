---
title: Min första grafiska Runbook i Azure Automation
description: En självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel grafisk runbook.
keywords: runbook, runbook-mall, runbook-automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: b891c8a7bbb33e3a3f18adbbc723d4bc9aa99a3a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246468"
---
# <a name="my-first-graphical-runbook"></a>Min första grafiska runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Den här självstudien beskriver steg för steg hur du skapar en [grafisk runbook](automation-runbook-types.md#graphical-runbooks) i Azure Automation. Börja med en enkel Runbook som testar och publicerar, och lär dig hur du spårar statusen för Runbook-jobbet. Ändra sedan runbooken till att hantera Azure-resurser i det här fallet genom att starta en virtuell Azure-dator. Slutför självstudien för att göra runbooken mer robust genom att lägga till Runbook-parametrar och villkorliga länkar.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör den inte vara en virtuell produktions dator.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook

Börja med att skapa en enkel Runbook som visar texten "Hello World".

1. Öppna ditt Automation-konto på Azure Portal. 

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa till gångar är de moduler som automatiskt ingår i ett nytt Automation-konto. Du bör också ha den autentiseringsuppgifter som är kopplad till din prenumeration.
2. Öppna listan över runbooks genom att välja **Runbooks** under **process automatisering** .
3. Skapa en ny Runbook genom att välja **skapa en Runbook**.
4. Ge din runbook namnet **MyFirstRunbook-Graphical**.
5. I det här fallet ska du skapa en [grafisk Runbook](automation-graphical-authoring-intro.md). Välj **grafisk** för **Runbook-typ**.<br> ![Ny runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Klicka på **Skapa** för att skapa den nya runbooken och öppna den grafiska redigeraren.

## <a name="step-2---add-activities"></a>Steg 2 – lägga till aktiviteter

Du kan lägga till aktiviteter i din runbook med hjälp av bibliotekskontrollen till vänster i redigeraren. Du ska lägga till en **Write-Output**-cmdlet som returnerar text från runbook-jobbet.

1. Klicka i Sök fältet i biblioteks kontrollen och skriv **Write-output**. Sök resultatet visas i följande bild. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Rulla längst ned i listan. Högerklicka på **Skriv ut** och välj **Lägg till på arbets ytan**. Alternativt kan du klicka på ellipsen (...) bredvid namnet på cmdleten och sedan välja **Lägg till på arbets ytan**.
1. Klicka på aktiviteten **Write-Output** på arbetsytan. Den här åtgärden öppnar sidan konfigurations kontroll där du kan konfigurera aktiviteten.
1. **Etikett** fältet är som standard namnet på cmdleten, men du kan ändra det till något mer användarvänligt. Ändra den till **Skriv Hello World på skärmen**.
1. Klicka på **Parametrar** för att ange värden för cmdletens parametrar.

   En del cmdlets har flera parameter uppsättningar och du måste välja vilken som ska användas. I det här fallet har **Write-output** bara en parameter uppsättning.

1. Välj *InputObject*-parametern. Det här är den parameter som du använder för att ange den text som ska skickas till utdataströmmen.
1. List rutan **data källa** innehåller källor som du kan använda för att fylla i ett parameter värde. I den här menyn väljer du **PowerShell-uttryck**. 

   Du kan använda utdata från sådana källor som en annan aktivitet, en Automation-till gång eller ett PowerShell-uttryck. I det här fallet är utdata bara **Hello World**. Du kan använda ett PowerShell-uttryck och ange en sträng.<br>

1. I fältet **uttryck** skriver du **Hello World** och klickar sedan på **OK** två gånger för att återgå till arbets ytan.
1. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktion bör du testa den och kontrol lera att den fungerar som den ska. Om du testar en runbook körs dess utkast version och du kan visa dess utdata interaktivt.

1. Öppna test fönstret genom att välja **test fönster** .
1. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
1. Observera att ett [Runbook-jobb](automation-runbook-execution.md) skapas och att dess status visas i fönstret.

   Jobbets status börjar i **kö**, vilket anger att jobbet väntar på att en Runbook Worker i molnet ska bli tillgänglig. Statusen ändras till att **börja** när en arbets uppgift anlitar jobbet. Slutligen blir statusen **igång** när runbooken faktiskt börjar köras.

1. När Runbook-jobbet har slutförts visas utdata i test sidan. I det här fallet visas **Hello World**.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Den Runbook som du har skapat är fortfarande i utkast läge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Välj **publicera** för att publicera runbooken och sedan **Ja** när du uppmanas till det.
1. Rulla åt vänster för att Visa runbooken på sidan Runbooks och Observera att status värdet för **redigering** är inställt på **publicerat**.
1. Rulla tillbaka till höger för att visa sidan för **MyFirstRunbook-Graphic**.

   Med alternativen över överst kan du starta runbooken nu, schemalägga en framtida start tid eller skapa en [webhook](automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

1. Välj **Start** och sedan **Ja** när du uppmanas att starta runbooken.
1. Ett jobb fönster öppnas för det Runbook-jobb som har skapats. Kontrol lera att fältet **jobb status** visar **slutfört**.
1. Klicka på **utdata** för att öppna sidan utdata där du kan se **Hello World** visas.
1. Stäng sidan utdata.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se **Hello World** i utdataströmmen. 

    Observera att fönstret strömmar kan visa andra strömmar för ett Runbook-jobb, till exempel utförliga data strömmar och fel strömmar, om Runbook skriver till dem.
1. Stäng fönstret strömmar och fönstret jobb för att återgå till den **MyFirstRunbook-grafiska** sidan.
1. Om du vill visa alla jobb för runbooken väljer du **jobb** under **resurser**. På sidan jobb visas alla jobb som skapats av din Runbook. Du bör bara se ett jobb i listan eftersom du bara kör jobbet en gång.
1. Klicka på jobb namnet för att öppna samma jobb fönster som du visade när du startade runbooken. Använd det här fönstret om du vill visa information om alla jobb som har skapats för runbooken.

## <a name="step-5---create-variable-assets"></a>Steg 5 – Skapa variabler för tillgångar

Du har testat och publicerat din Runbook, men hittills gör den inte något användbart för att hantera Azure-resurser. Innan du konfigurerar runbooken att autentisera måste du skapa en variabel som ska innehålla prenumerations-ID, konfigurera en aktivitet för autentisering och sedan referera till variabeln. Genom att inkludera en referens till prenumerations kontexten kan du enkelt arbeta med flera prenumerationer.

1. Kopiera ditt prenumerations-ID från alternativet **prenumerationer** i navigerings fönstret.
1. På sidan Automation-konton väljer du **variabler** under **delade resurser**.
1. Välj **Lägg till en variabel**.
1. På sidan ny variabel gör du följande inställningar i de angivna fälten.

    * **Namn** – ange **AzureSubscriptionId**.
    * **Värde** – ange ditt PRENUMERATIONS-ID. 
    * **Skriv** --Behåll sträng vald.
    * **Kryptering** – Använd standardvärdet.
1. Skapa variabeln genom att klicka på **Skapa**.

## <a name="step-6---add-authentication"></a>Steg 6 – Lägg till autentisering

Nu när du har en variabel som ska innehålla prenumerations-ID kan du konfigurera runbooken att autentisera med kör som-autentiseringsuppgifterna för din prenumeration. Gör detta genom att lägga till Azure kör som-anslutningen som en till gång. Du måste också lägga till cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) och cmdleten [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) på arbets ytan.

>[!NOTE]
>För PowerShell-Runbooks är **Add-AzAccount** och **Add-AzureRMAccount** alias för **Connect-AzAccount**. Observera att dessa alias inte är tillgängliga för dina grafiska runbooks. En grafisk Runbook kan bara använda **Connect-AzAccount** .

1. Navigera till din Runbook och välj **Redigera** på den **MyFirstRunbook-grafiska** sidan.
1. Du behöver inte **skriva Hello World för att mata** in fler. Klicka bara på ellipsen och välj **ta bort**.
1. I biblioteks kontrollen expanderar du **till gångar**och sedan **anslutningar**. Lägg till **AzureRunAsConnection** på arbets ytan genom att välja **Lägg till på arbets ytan**.
1. Skriv **Connect-AzAccount** i Sök fältet i biblioteks kontrollen.
1. Lägg till **Connect-AzAccount** på arbets ytan.
1. Hovra över **Hämta ”kör som”-anslutning** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen för att **ansluta-AzAccount** för att skapa en länk. Runbooken börjar med **Hämta kör som-anslutning** och kör sedan **Connect-AzAccount**.<br> ![Skapa länk mellan aktiviteter](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. På arbets ytan väljer du **Connect-AzAccount**. I rutan konfigurations kontroll skriver **du logga in på Azure** i fältet **etikett** .
1. Klicka på **parametrar**så visas sidan konfiguration av aktivitets parameter.
1. Cmdleten **Connect-AzAccount** har flera parameter uppsättningar och du måste välja en innan du kan ange parameter värden. Klicka på **Parameteruppsättning** och välj parameteruppsättningen **ServicePrincipalCertificate**.
1. Parametrarna för den här parameter uppsättningen visas på sidan konfiguration av aktivitets parameter. Klicka på **APPLICATIONID**.<br> ![lägga till Azure-konto parametrar](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. På sidan parameter värde gör du följande inställningar och klickar sedan på **OK**.

   * **Data källa** – Välj **aktivitets utdata**.
   * Lista över data källor – Välj **Hämta kör som-anslutning**.
   * **Fält Sök väg** -typ **ApplicationId**. Du anger namnet på egenskapen för fält Sök vägen eftersom aktiviteten matar ut ett objekt med flera egenskaper.
1. Klicka på **CERTIFICATETHUMBPRINT**och gör följande inställningar på sidan parameter värde och klicka sedan på **OK**.

    * **Data källa** – Välj **aktivitets utdata**.
    * Lista över data källor – Välj **Hämta kör som-anslutning**.
    * **Fält Sök väg** --Skriv **CertificateThumbprint**.
1. Klicka på **SERVICEPRINCIPAL**och på sidan parameter värde väljer du **ConstantValue** för fältet **data källa** . Klicka på alternativet **Sant**; och klicka sedan på **OK**.
1. Klicka på **TENANTID**och gör följande inställningar på sidan parameter värde. När du är färdig klickar du på **OK** två gånger.

    * **Data källa** – Välj **aktivitets utdata**. 
    * Lista över data källor – Välj **Hämta kör som-anslutning**.
    * **Fält Sök väg** --typ **TenantId**. 
1. I biblioteks kontrollen skriver du **set-AzContext** i Sök fältet.
1. Lägg till **set-AzContext** på arbets ytan.
1. Välj **set-AzContext** på arbets ytan. I fönstret konfigurations kontroll anger du **ange prenumerations-ID** i fältet **etikett** .
1. Klicka på **parametrar** och sidan konfiguration av aktivitets parameter visas.
1. **Set-AzContext** -cmdleten har flera parameter uppsättningar och du måste välja en innan du tillhandahåller parameter värden. Klicka på **Parameteruppsättning** och välj parameteruppsättningen **SubscriptionId**.
1. Parametrarna för den här parameter uppsättningen visas på sidan konfiguration av aktivitets parameter. Klicka på **SubscriptionID**.
1. På sidan parameter värde väljer du **variabel till gång** för fältet **data källa** och väljer **AzureSubscriptionId** i listan källa. När du är färdig klickar du på **OK** två gånger.
1. Hovra över **Inloggning i Azure** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till **Ange prenumerations-ID**. 

Din runbook bör se ut ungefär så här nu: <br>![Konfiguration av runbook-autentisering](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Steg 7 – Lägga till aktivitet för att starta en virtuell dator

Nu måste du lägga till en **Start-AzVM** -aktivitet för att starta en virtuell dator. Du kan välja vilken virtuell dator som helst i din Azure-prenumeration och för tillfället hårdkoda du dess namn i cmdleten [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. I biblioteks kontrollen skriver du **Start-AZ** i Sök fältet.
2. Lägg till **Start-AzVM** på arbets ytan och klicka på och dra den under **ange prenumerations-ID**.
1. Hovra över **Ange prenumerations-ID** tills en cirkel visas längst ned i formen. Klicka på cirkeln och dra pilen till **Start-AzVM**.
1. Välj **Start-AzVM**. Klicka på **parametrar** och sedan på **parameter uppsättning** för att Visa uppsättningarna för aktiviteten.
1. Välj parameteruppsättningen **ResourceGroupNameParameterSetName**. Fälten **ResourceGroupName** och **Name** har utrops tecken bredvid sig för att ange att de är obligatoriska parametrar. Observera att båda fälten förväntar sig sträng värden.
1. Välj **Name**. Välj **PowerShell-uttryck** för **data källans** fält. För den virtuella dator som du använder för att starta denna Runbook skriver du in dator namnet omgiven av dubbla citat tecken. Klicka på **OK**
1. Välj **ResourceGroupName**. Använd värdet **PowerShell-uttrycket** för fältet **data källa** och skriv namnet på resurs gruppen omgiven av dubbla citat tecken. Klicka på **OK**
1. Klicka på **test fönster** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta** . När den är klar kontrollerar du att den virtuella datorn har startats. 

Din runbook bör se ut ungefär så här nu: <br>![Konfiguration av runbook-autentisering](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Steg 8 – lägga till ytterligare indataparametrar

Din Runbook startar för närvarande den virtuella datorn i resurs gruppen som du angav för cmdleten **Start-AzVM** . Runbooken blir mer användbar om du anger både namn och resurs grupp när runbooken startas. Nu ska vi lägga till indataparametrar till Runbook för att tillhandahålla den funktionen.

1. Öppna den grafiska redigeraren genom att klicka på **Redigera** i fönstret **MyFirstRunbook-Graphic** .
1. Välj **indata och utdata** och **Lägg sedan till indata** för att öppna fönstret inmatnings parameter för Runbook.
1. Gör följande inställningar i de angivna fälten och klicka sedan på **OK**.
   * **Namn** --ange **VMName**.
   * **Skriv** --Behåll sträng inställningen.
   * **Obligatoriskt** – ändra värdet till **Ja**.
1. Skapa en andra obligatorisk indataparameter med namnet *ResourceGroupName* och klicka sedan på **OK** för att stänga fönstret indata och utdata.<br> ![Indataparametrar för Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Välj aktiviteten **Start-AzVM** och klicka sedan på **parametrar**.
1. Ändra fältet **data källa** för **namn** till **indata för Runbook**. Välj sedan **VMName**.
1. Ändra fältet **data källa** för **ResourceGroupName** till **indata för Runbook** och välj sedan **ResourceGroupName**.<br> ![start-AzVM parametrar](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som du använder i testet.
1. Stäng Testfönster.
1. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
1. Stoppa den virtuella datorn som du startade tidigare.
1. Starta runbooken genom att klicka på **Starta**. Skriv värdena för **VMName** och **ResourceGroupName** för den virtuella dator som du ska starta.
1. När Runbook-flödet har slutförts kontrollerar du att den virtuella datorn har startats.

## <a name="step-9---create-a-conditional-link"></a>Steg 9 – Skapa en villkorlig länk

Nu kan du ändra runbooken så att den bara försöker starta den virtuella datorn om den inte redan har startats. Det gör du genom att lägga till en [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) -cmdlet som hämtar den virtuella datorns status på instans nivå. Sedan kan du lägga till en PowerShell Workflow-modul med namnet **Hämta status** med ett kodfragment av PowerShell-koden för att avgöra om tillståndet för den virtuella datorn körs eller är stoppad. En villkorlig länk från modulen **Hämta status** kör bara **Start-AzVM** om det aktuella körnings tillståndet har stoppats. I slutet av den här proceduren använder din Runbook cmdleten **Write-output** för att skicka ett meddelande som meddelar dig om den virtuella datorn har startats.

1. Öppna **MyFirstRunbook – grafiskt** i den grafiska redigeraren.
1. Ta bort länken mellan **ange prenumerations-ID** och **Start-AzVM** genom att klicka på den och sedan trycka på **ta bort**.
1. Skriv **Get-AZ** i Sök fältet i biblioteks kontrollen.
1. Lägg till **Get-AzVM** på arbets ytan.
1. Välj **Get-AzVM** och sedan **parameter uppsättning** för att Visa uppsättningarna för cmdleten. 
1. Välj parameteruppsättningen **GetVirtualMachineInResourceGroupNameParamSet**. Fälten **ResourceGroupName** och **namn** innehåller utrops tecken bredvid dem, vilket indikerar att de anger obligatoriska parametrar. Observera att båda fälten förväntar sig sträng värden.
1. Under **data källa** för **namn**väljer du **indata för Runbook**och sedan **VMName**. Klicka på **OK**
1. Under **data källa** för **ResourceGroupName**, väljer du **indata för Runbook**och sedan **ResourceGroupName**. Klicka på **OK**
1. Under **data källa** för **status**väljer du **konstant värde**och sedan **Sant**. Klicka på **OK**
1. Skapa en länk från **ange prenumerations-ID** till **Get-AzVM**.
1. I biblioteks kontrollen expanderar du **Runbook-kontroll** och lägger till **kod** på arbets ytan.  
1. Skapa en länk från **Get-AzVM** till **Code**.  
1. Klicka på **kod** och ändra etiketten för att **Hämta status**i rutan konfiguration.
1. Välj **kod** så visas sidan kod redigerare.  
1. Klistra in följande kodfragment på sidan redigeraren.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Skapa en länk från **Hämta status** till **Start-AzVM**.<br> ![Runbook med kodmodul](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Välj länken och ändra **tillämpa villkor** till **Ja**i rutan konfiguration. Observera att länken blir en streckad linje som anger att mål aktiviteten endast körs om villkoret matchar sant.  
1. För **villkors uttryck**skriver du `$ActivityOutput['Get Status'] -eq "Stopped"`. **Start-AzVM** körs nu bara om den virtuella datorn har stoppats.
1. Expandera **Cmdlets** och sedan **Microsoft.PowerShell.Utility** i bibliotekskontrollen.
1. Lägg till **Write-Output** på arbetsytan två gånger.
1. För den första **Skriv-och utdata-** kontrollen klickar du på **parametrar** och ändrar **etikett** svärdet för att **meddela att den virtuella datorn har startats**.
1. För **InputObject**ändrar du **data källa** till **PowerShell-uttryck**och skriver in uttrycket **$VMName har startats.** .
1. På den andra **skrivnings-och utdata-** kontrollen klickar du på **parametrar** och ändrar **etikett** svärdet till att **meddela att den virtuella datorn startades**.
1. För **InputObject**, ändra **data källa** till **PowerShell-uttryck**och skriv in uttrycket **$VMName inte kunde starta.** .
1. Skapa länkar från **Start-AzVM** till **meddela att den virtuella datorn har startats** och **meddela att den virtuella datorn startades**.
1. Välj länken för att **meddela att den virtuella datorn har startats** och ändra **tillämpa villkor** till sant.
1. Skriv `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`för **villkors uttrycket**. Nu körs den här **Write-output-** kontrollen bara om den virtuella datorn har startats.
1. Välj länken för att **meddela att den virtuella datorns start misslyckades** och ändra **tillämpa villkor** till sant.
1. I fältet **villkors uttryck** skriver du `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Nu körs den här **Write-output-** kontrollen bara om den virtuella datorn inte har startats. Din Runbook bör se ut som på följande bild: <br> ![Runbook med Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Spara runbooken och öppna Testfönster.
1. Starta runbooken med den virtuella datorn stoppad och starta sedan datorn.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering finns [i grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Information om hur du kommer igång med PowerShell Workflow-Runbooks finns i [min första PowerShell Workflow-Runbook](automation-first-runbook-textual.md).