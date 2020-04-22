---
title: Skapa en grafisk runbook i Azure Automation
description: Självstudiekurs som visar hur du skapar, testar och publicerar en enkel grafisk runbook i Azure Automation.
keywords: runbook, runbook-mall, runbook-automation, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: f87f389667043e26f066886eddcdb8061df0319f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726188"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Självstudiekurs: Skapa en grafisk runbook

Den här självstudien beskriver steg för steg hur du skapar en [grafisk runbook](../automation-runbook-types.md#graphical-runbooks) i Azure Automation. Du kan skapa och redigera grafiska och grafiska PowerShell-arbetsflödeskörningsböcker med hjälp av den grafiska redigeraren i Azure-portalen. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en enkel grafisk runbook
> * Testa och publicera runbooken
> * Kör och spåra status för körningsjobbet
> * Uppdatera runbooken för att starta en virtuell Azure-dator med runbook-parametrar och villkorliga länkar

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](../automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör det inte vara en virtuell produktionsdator.

## <a name="step-1---create-runbook"></a>Steg 1 – Skapa en runbook

Börja med att skapa en enkel `Hello World`runbook som matar ut texten .

1. Öppna ditt Automation-konto på Azure Portal. 

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som automatiskt ingår i ett nytt Automation-konto. Du bör också ha den autentiseringstillgång som är kopplad till din prenumeration.

2. Välj **Runbooks** under **Process Automation** om du vill öppna listan över runbooks.

3. Skapa en ny runbook genom att välja **Skapa en runbook**.

4. Ge din runbook namnet **MyFirstRunbook-Graphical**.

5. I det här fallet ska du skapa en [grafisk runbook](../automation-graphical-authoring-intro.md). Välj **Grafisk för** **Runbook-typ**.<br> ![Ny runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)<br>

6. Klicka på **Skapa** för att skapa den nya runbooken och öppna den grafiska redigeraren.

## <a name="step-2---add-activities"></a>Steg 2 - Lägga till aktiviteter

Du kan lägga till aktiviteter i din runbook med hjälp av bibliotekskontrollen till vänster i redigeraren. Du ska lägga till `Write-Output` en cmdlet i utdatatexten från runbooken.

1. Klicka i sökfältet i bibliotekskontrollen `write-output`och skriv . Sökresultaten visas i följande bild. <br> ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Rulla längst ned i listan. Högerklicka på **Skrivutdata** och välj **Lägg till på arbetsyta .** Du kan också klicka på ellipsen (...) bredvid cmdletnamnet och sedan välja **Lägg till på arbetsytan**.

3. Klicka på aktiviteten **Write-Output** på arbetsytan. Den här åtgärden öppnar kontrollsidan för konfiguration, vilket gör att du kan konfigurera aktiviteten.

4. Fältet **Etikett** är som standard namnet på cmdleten, men du kan ändra det till något mer användarvänligt. Ändra den `Write Hello World to output`till .

5. Klicka på **Parametrar** för att ange värden för cmdletens parametrar.

   Vissa cmdlets har flera parameteruppsättningar och du måste välja vilken som ska användas. I det `Write-Output` här fallet har endast en parameteruppsättning.

6. Välj `InputObject` parametern. Det här är den parameter som du använder för att ange den text som ska skickas till utdataströmmen.

7. Den nedrullningsna menyn **Datakälla** innehåller källor som du kan använda för att fylla i ett parametervärde. Välj **PowerShell-uttryck**på den här menyn . 

   Du kan använda utdata från källor som en annan aktivitet, en Automation-tillgång eller ett PowerShell-uttryck. I det här fallet `Hello World`är utdata bara . Du kan använda ett PowerShell-uttryck och ange en sträng.<br>

8. Skriv **Expression** och `Hello World` klicka sedan på OK två gånger i fältet Uttryck och klicka sedan på **OK** två gånger för att återgå till arbetsytan.

9. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktionen bör du testa den för att se till att den fungerar som den ska. När du testar en runbook körs dess utkastversion och du kan visa dess utdata interaktivt.

1. Välj **Testfönster** för att öppna testfönstret.

2. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.

3. Observera att ett [runbook-jobb](../automation-runbook-execution.md) skapas och att dess status visas i fönstret.

   Jobbstatusen börjar `Queued`som , vilket anger att jobbet väntar på att en runbook-arbetare i molnet ska bli tillgänglig. Statusen ändras till `Starting` när en anställd gör anspråk på jobbet. Slutligen blir `Running` statusen när runbooken faktiskt börjar köras.

4. När runbook-jobbet är klart visar testfönstret utdata. I det här `Hello World`fallet ser du .

    ![Hello World](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Runbook som du har skapat är fortfarande i utkastläge. Den behöver publiceras innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Välj **Publicera** om du vill publicera runbooken och sedan **Ja** när du uppmanas att göra det.

2. Bläddra åt vänster för att visa runbooken på sidan Runbooks och observera att värdet **för redigeringsstatus** är inställt på **Publicerad**.

3. Bläddra tillbaka till höger för att visa sidan för **MyFirstRunbook-Graphical**.

   Alternativen överst gör att du kan starta runbooken nu, schemalägga en framtida starttid eller skapa en [webhook](../automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

4. Välj **Start** och sedan **Ja** när du uppmanas att starta runbooken.

5. Ett jobbfönster öppnas för det runbook-jobb som har skapats. Kontrollera att fältet **Jobbstatus** visar **Slutförd**.

6. Klicka på **Utdata** för att öppna `Hello World` utdatasidan, där du kan se visas.

7. Stäng utdatasidan.

8. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör `Hello World` bara se i utdataströmmen. 

    Observera att fönstret Strömmar kan visa andra strömmar för ett runbook-jobb, till exempel Utförliga och Felströmmar, om runbooken skriver till dem.

9. Stäng fönstret Strömmar och jobbfönstret för att återgå till sidan MyFirstRunbook-Graphical.

10. Om du vill visa alla jobb för runbooken väljer du **Jobb** under **Resurser**. På sidan Jobb visas alla jobb som skapas av runbooken. Du bör bara se ett jobb som anges, eftersom du bara har kört jobbet en gång.

11. Klicka på jobbnamnet om du vill öppna samma jobbfönster som du visade när du startade runbooken. Använd det här fönstret om du vill visa information om alla jobb som skapats för runbooken.

## <a name="step-5---create-variable-assets"></a>Steg 5 – Skapa variabler för tillgångar

Du har testat och publicerat din runbook, men hittills har det inte gjort något användbart för att hantera Azure-resurser. Innan du konfigurerar runbooken så att den autentiserar måste du skapa en variabel för att hålla prenumerations-ID:t, ställa in en aktivitet för att autentisera och sedan referera till variabeln. Genom att inkludera en referens till prenumerationskontexten kan du enkelt arbeta med flera prenumerationer.

1. Kopiera ditt prenumerations-ID från alternativet **Prenumerationer** i navigeringsfönstret.

2. På sidan Automation-konton väljer du **Variabler** under **Delade resurser**.

3. Välj **Lägg till en variabel**.

4. På sidan Ny variabel gör du följande inställningar i de angivna fälten.

    * **Namn** -- `AzureSubscriptionId`ange .
    * **Värde** – ange ditt prenumerations-ID. 
    * **Skriv** – håll strängen markerad.
    * **Kryptering** – använd standardvärdet.

5. Skapa variabeln genom att klicka på **Skapa**.

## <a name="step-6---add-authentication"></a>Steg 6 - Lägg till autentisering

Nu när du har en variabel för att behålla prenumerations-ID:t kan du konfigurera runbooken så att den autentiseras med autentiseringsuppgifterna för Körning för din prenumeration. Gör detta genom att lägga till Azure Run As-anslutningen som en tillgång. Du måste också lägga till cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) och cmdlet [set-azcontext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) på arbetsytan.

>[!NOTE]
>För `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är `Connect-AzAccount`alias för . Observera att dessa alias inte är tillgängliga för dina grafiska runbooks. En grafisk runbook kan `Connect-AzAccount`bara använda sig själv.

1. Navigera till din runbook och välj **Redigera** på sidan MyFirstRunbook-Graphical.

2. Du behöver inte `Write Hello World to output` posten längre. Klicka bara på ellipsen och välj **Ta bort**.

3. Expandera **RESURSER**i bibliotekskontrollen och sedan **Anslutningar**. Lägg `AzureRunAsConnection` till på arbetsytan genom att välja **Lägg till på arbetsyta .**

4. Byt `AzureRunAsConnection` `Get Run As Connection`namn till .

5. Skriv `Connect-AzAccount` i sökfältet i bibliotekskontrollen.

6. Lägg `Connect-AzAccount` till på arbetsytan.

7. Hovra `Get Run As Connection` över tills en cirkel visas längst ned i formen. Klicka på cirkeln och `Connect-AzAccount` dra pilen till för att bilda en länk. Runbooken börjar `Get Run As Connection` med `Connect-AzAccount`och körs sedan .<br> ![Skapa länk mellan aktiviteter](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Välj `Connect-AzAccount`på arbetsytan . Skriv **Logga in på Azure** i fältet Etikett i kontrollfönstret **Konfiguration.**

9. Klicka på **Parametrar**och sidan Konfiguration av aktivitetsparameter visas.

10. Cmdleten `Connect-AzAccount` har flera parameteruppsättningar och du måste välja en innan du anger parametervärden. Klicka på **Parameteruppsättning** och välj sedan **ServicePrincipalCertificateWithSubscriptionId**.

11. Parametrarna för den här parameteruppsättningen visas på sidan Konfiguration av aktivitetsparameter. Klicka på **APPLICATIONID**.<br> ![Lägga till Azure-kontoparametrar](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. På sidan Parametervärde gör du följande inställningar och klickar sedan på **OK**.

   * **Datakälla** – välj **Aktivitetsutdata**.
   * Datakälllista – välj **Hämta Automation-anslutning**.
   * **Fältsökväg** -- typ `ApplicationId`. Du anger namnet på egenskapen för fältsökvägen eftersom aktiviteten matar ut ett objekt med flera egenskaper.

13. Klicka på **CERTIFICATETHUMBPRINT**och gör följande inställningar på sidan Parametervärde och klicka sedan på **OK**.

    * **Datakälla** – välj **Aktivitetsutdata**.
    * Datakälllista – välj **Hämta Automation-anslutning**.
    * **Fältsökväg** -- typ `CertificateThumbprint`.

14. Klicka på **SERVICEPRINCIPAL**och välj **Konstantvärde** för fältet **Datakälla** på sidan Parametervärde. klicka på alternativet **Sant**; och klicka sedan på **OK**.

15. Klicka på **KLIENTID**och gör följande inställningar på sidan Parametervärde. När du är klar klickar du på **OK** två gånger.

    * **Datakälla** – välj **Aktivitetsutdata**. 
    * Datakälllista – välj **Hämta Automation-anslutning**.
    * **Fältsökväg** -- typ `TenantId`. 

16. Skriv `Set-AzContext` i sökfältet i bibliotekskontrollen.

17. Lägg `Set-AzContext` till på arbetsytan.

18. Välj `Set-AzContext` på arbetsytan. Ange `Specify Subscription Id` i fältet Etikett i kontrollfönstret **Konfiguration.**

19. Klicka på **Parametrar** och sidan Konfiguration av aktivitetsparameter visas.

20. Cmdleten `Set-AzContext` har flera parameteruppsättningar och du måste välja en innan du anger parametervärden. Klicka på **Parameteruppsättning** och välj sedan **SubscriptionId**.

21. Parametrarna för den här parameteruppsättningen visas på sidan Konfiguration av aktivitetsparameter. Klicka på **SubscriptionID**.

22. På sidan Parametervärde väljer du **Variabel tillgång** för **datakällfältet** och väljer **AzureSubscriptionId** i källlistan. När du är klar klickar du på **OK** två gånger.

23. Hovra `Login to Azure` över tills en cirkel visas längst ned i formen. Klicka på cirkeln och `Specify Subscription Id`dra pilen till . Runbook ska se ut så här vid denna tidpunkt.

    ![Konfiguration av runbook-autentisering](../media/automation-tutorial-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Steg 7 – Lägga till aktivitet för att starta en virtuell dator

Nu måste du `Start-AzVM` lägga till en aktivitet för att starta en virtuell dator. Du kan välja vilken virtuell dator som helst i din Azure-prenumeration och för tillfället är du hårdkodning dess namn i [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet.

1. Skriv `Start-Az` i sökfältet i bibliotekskontrollen.

2. Lägg `Start-AzVM` till på arbetsytan och klicka `Specify Subscription Id`sedan och dra den under .

3. Hovra `Specify Subscription Id` över tills en cirkel visas längst ned i formen. Klicka på cirkeln och `Start-AzVM`dra pilen till .

4. Välj `Start-AzVM`. Klicka på **Parametrar** och sedan **parameteruppsättning** för att visa uppsättningarna för aktiviteten.

5. Välj **ResourceGroupNameParameterSetName** för parameteruppsättningen. Fälten **ResourceGroupName** och **Name** har utropstecken bredvid dem för att ange att de är obligatoriska parametrar. Observera att båda fälten förväntar sig strängvärden.

6. Välj **Name**. Välj **PowerShell-uttryck** för **fältet Datakälla.** Skriv in datornamnet som omges av dubbla citattecken för den virtuella datorn som du använder för att starta den här runbooken. Klicka på **OK**.

7. Välj **ResourceGroupName**. Använd värdet **PowerShell-uttrycket** för **datakällfältet** och skriv in namnet på resursgruppen omgiven av dubbla citattecken. Klicka på **OK**.

8. Klicka på **Testfönstret** så att du kan testa runbooken.

9. Klicka på **Start** för att påbörja testet. När den är klar kontrollerar du att den virtuella datorn har startat. Runbook ska se ut så här vid denna tidpunkt.

    ![Konfiguration av runbook-autentisering](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Steg 8 - Lägga till ytterligare indataparametrar

Runbooken startar för närvarande den virtuella datorn i `Start-AzVM` resursgruppen som du angav för cmdleten. Runbooken blir mer användbar om du anger både namn och resursgrupp när runbooken startas. Nu ska vi lägga till indataparametrar i runbooken för att tillhandahålla den funktionen.

1. Öppna den grafiska redigeraren genom att klicka på **Redigera** på sidan MyFirstRunbook-Graphical.

2. Välj **Indata och utdata** och lägg sedan **till indata** för att öppna fönstret Runbook Input Parameter.

3. Gör följande inställningar i de angivna fälten och klicka sedan på **OK**.
   * **Namn** -- `VMName`ange .
   * **Skriv** – behåll stränginställningen.
   * **Obligatoriskt** – ändra värdet till **Ja**.

4. Skapa en andra obligatorisk `ResourceGroupName` indataparameter som anropas och klicka sedan på **OK** för att stänga fönstret In- och utdata.<br> ![Indataparametrar för Runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Markera `Start-AzVM` aktiviteten och klicka sedan på **Parametrar**.

6. Ändra **fältet Datakälla** för **Namn** till **Runbook indata**. Välj sedan **VMName**.

7. Ändra **fältet Datakälla** för **ResourceGroupName** till **Runbook indata** och välj sedan **ResourceGroupName**.<br> ![Start-AzVM-parametrar](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två indatavariablerna som du använder i testet.

9. Stäng Testfönster.

10. Klicka på **Publicera** för att publicera den nya versionen av runbooken.

11. Stoppa den virtuella datorn som du startade tidigare.

12. Starta runbooken genom att klicka på **Starta**. Skriv in värdena för `VMName` och `ResourceGroupName` för den virtuella datorn som du ska starta.

13. När runbooken är klar kontrollerar du att den virtuella datorn har startats.

## <a name="step-9---create-a-conditional-link"></a>Steg 9 – Skapa en villkorlig länk

Du kan nu ändra runbook så att den bara försöker starta den virtuella datorn om den inte redan har startats. Gör detta genom att lägga till en [Get-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) som hämtar status på instansnivå för den virtuella datorn. Sedan kan du lägga till en `Get Status` PowerShell Workflow-kodmodul som anropas med ett utdrag av PowerShell-kod för att avgöra om tillståndet för den virtuella datorn körs eller stoppas. En villkorslänk `Get Status` från `Start-AzVM` modulen körs bara om det aktuella körtillståndet stoppas. I slutet av den här proceduren `Write-Output` använder runbooken cmdlet för att mata ut ett meddelande för att informera dig om den virtuella datorn har startats.

1. Öppna **MyFirstRunbook-Graphical** i den grafiska redigeraren.

2. Ta bort `Specify Subscription Id` länken `Start-AzVM` mellan och genom att klicka på den och sedan trycka på **Ta bort**.

3. Skriv `Get-Az` i sökfältet i bibliotekskontrollen.

4. Lägg `Get-AzVM` till på arbetsytan.

5. Markera `Get-AzVM` och klicka sedan på **Parameteruppsättning** om du vill visa uppsättningarna för cmdleten. 

6. Välj parameteruppsättningen **GetVirtualMachineInResourceGroupNameParamSet**. Fälten **ResourceGroupName** och **Name** har utropstecken bredvid sig, vilket anger att de anger nödvändiga parametrar. Observera att båda fälten förväntar sig strängvärden.

7. Under **Datakälla** för **Namn**väljer du **Runbook-indata**och sedan **VMName**. Klicka på **OK**.

8. Under **Datakälla** för **ResourceGroupName**väljer du **Runbook-indata**och sedan **ResourceGroupName**. Klicka på **OK**.

9. Under **Datakälla** för **Status**väljer du **Konstant värde**och sedan **Sant**. Klicka på **OK**.

10. Skapa en `Specify Subscription Id` länk `Get-AzVM`från till .

11. Expandera **Runbook Control** i bibliotekskontrollen och lägg till **kod** på arbetsytan.  

12. Skapa en `Get-AzVM` länk `Code`från till .  

13. Klicka `Code` på och ändra etiketten till **Hämta status**i konfigurationsfönstret .

14. Välj `Code` och sidan Kodredigerare visas.  

15. Klistra in följande kodavsnitt på redigeringssidan.

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

16. Skapa en `Get Status` länk `Start-AzVM`från till .

    ![Runbook med kodmodul](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Markera länken och ändra **Villkoret Använd** till **Ja**i konfigurationsfönstret . Observera att länken blir en streckad linje, vilket anger att målaktiviteten endast körs om villkoret går att lösa.  

18. För **villkorsuttryck**skriver du `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM`körs nu bara om den virtuella datorn stoppas.

19. Expandera **Cmdlets** och sedan **Microsoft.PowerShell.Utility** i bibliotekskontrollen.

20. Lägg `Write-Output` till på arbetsytan två gånger.

21. För den `Write-Output` första kontrollen klickar du på **Parametrar** och ändrar **etikettvärdet** till **Meddela vm-start**.

22. För **InputObject**ändrar du **Datakälla till** **PowerShell-uttryck**och skriver in uttrycket `$VMName successfully started.`.

23. Klicka på `Write-Output` **Parametrar** och ändra **etikettvärdet** till Meddela vm-start misslyckades i den andra kontrollen och ändrar etikettvärdet till **Meddela att vm-start misslyckades**.

24. För **InputObject**ändrar du **Datakälla till** **PowerShell-uttryck**och skriver in uttrycket `$VMName could not start`.

25. Skapa länkar `Start-AzVM` `Notify VM Started` från `Notify VM Start Failed`till och .

26. Markera länken `Notify VM Started` till och ändra **Använd villkor** till true.

27. För **villkorsuttrycket**skriver du `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Den `Write-Output` här kontrollen körs nu bara om den virtuella datorn startar.

28. Markera länken `Notify VM Start Failed` till och ändra **Använd villkor** till true.

29. Skriv för fältet **Villkorsuttryck** `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Den `Write-Output` här kontrollen körs nu bara om den virtuella datorn inte har startats. Runbooken ska se ut som följande bild.

    ![Runbook med Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Spara runbooken och öppna Testfönster.

31. Starta runbooken med den virtuella datorn stoppad och datorn ska starta.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering finns [i Grafisk redigering i Azure Automation](../automation-graphical-authoring-intro.md).
* Mer om du vill komma igång med PowerShell-runbooks finns i [Skapa en PowerShell-runbook](automation-tutorial-runbook-textual-powershell.md).
* Information om hur du kommer igång med PowerShell Workflow runbooks finns i [Skapa en PowerShell-arbetsflödeskörningsbok](automation-tutorial-runbook-textual.md).
* En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).