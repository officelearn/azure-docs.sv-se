---
title: Indataparametrar för Runbook
description: Indataparametrar för Runbook öka flexibiliteten i runbooks genom att låta dig skicka data till en runbook när den startas. Den här artikeln beskrivs olika scenarier där indataparametrar som används i runbooks.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc03bbf63427061c8d9f9e96ebcd9dce84f9fccf
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "42056936"
---
# <a name="runbook-input-parameters"></a>Indataparametrar för Runbook

Indataparametrar för Runbook öka flexibiliteten i runbooks genom att låta dig skicka data till den när den startas. Parametrarna kan åtgärderna som runbook ska vara mål för specifika scenarier och miljöer. I den här artikeln får igenom du olika scenarier där indataparametrar som används i runbooks.

## <a name="configure-input-parameters"></a>Konfigurera indataparametrar

Indataparametrar kan konfigureras i PowerShell, PowerShell-arbetsflöde, Python och grafiska runbooks. En runbook kan ha flera parametrar med olika datatyper eller inga parametrar alls. Indataparametrar kan vara obligatoriskt eller valfritt och du kan tilldela ett standardvärde för valfria parametrar. Du kan tilldela värden till indataparametrar för en runbook när du startar det genom en av de tillgängliga metoderna. De här metoderna omfattar att starta en runbook från portalen eller en webbtjänst. Du kan också starta en som en underordnad runbook som ska anropas infogad i en annan runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Konfigurera indataparametrar i PowerShell och PowerShell Workflow-runbooks

PowerShell och [PowerShell Workflow-runbooks](automation-first-runbook-textual.md) stöder indataparametrar som definieras via följande attribut i Azure Automation:  

| **Egenskap** | **Beskrivning** |
|:--- |:--- |
| Typ |Krävs. Datatypen som förväntat för parametervärdet. Valfri .NET är giltig. |
| Namn |Krävs. Namnet på parametern. Detta måste vara unika inom en runbook och kan enbart innehålla bokstäver, siffror eller understreck. Det måste börja med en bokstav. |
| Obligatorisk |Valfri. Anger om ett värde måste anges för parametern. Om du väljer **$true**, och sedan ett värde måste anges när runbooken startar. Om du väljer **$false**, och sedan ett värde är valfritt. |
| Standardvärde |Valfri. Anger ett värde som används för parametern om ett värde inte skickas när runbooken startar. Ett standardvärde kan ställas in för valfri parameter och görs automatiskt parametern valfritt oavsett den obligatoriska inställningen. |

Windows PowerShell har stöd för flera attribut för indataparametrar än de som anges här, t.ex validering, alias, och parametern anger. Azure Automation stöder för närvarande endast de föregående indataparametrarna.

En parameter i PowerShell Workflow-runbooks har följande allmänna formuläret, där flera parametrar är avgränsade med kommatecken.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> När du definierar parametrar, om du inte anger den **obligatorisk** attributet och sedan som standard parametern betraktas som valfria. Även om du anger ett standardvärde för en parameter i PowerShell Workflow-runbooks behandlas av PowerShell som en valfri parameter, oavsett den **obligatorisk** attributvärde.
> 
> 

Till exempel ska vi konfigurera indataparametrar för en PowerShell Workflow-runbook som visar information om virtuella datorer, en enskild virtuell dator eller alla virtuella datorer inom en resursgrupp. Denna runbook innehåller två parametrar som visas i följande skärmbild: namnet på den virtuella datorn och namnet på resursgruppen.

![Automation PowerShell-arbetsflöde](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

I den här parameterdefinitionen av parametrarna **$VMName** och **$resourceGroupName** är enkla parametrar av typen sträng. Dock PowerShell och PowerShell Workflow-runbooks stöder alla typer av enkla och komplexa typer som **objekt** eller **PSCredential** för indataparametrar.

Om din runbook har en indataparameter av typen objekt, använder du en PowerShell hash-tabell med (namn, värde) par att skicka in ett värde. Exempel: Om du har följande parameter i en runbook:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Sedan kan du skicka följande värde för parametern:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurera indataparametrar i grafiska runbooks

Att [konfigurera en grafisk runbook](automation-first-runbook-graphical.md) med indataparametrar, nu ska vi skapa en grafisk runbook som visar information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer inom en resursgrupp. Konfigurera en runbook består av två viktiga aktiviteter, enligt beskrivningen nedan.

[**Autentisera Runbooks med Kör som-konto** ](automation-sec-configure-azure-runas-account.md) att autentisera med Azure.

[**Get-AzureRmVm** ](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) att hämta egenskaperna för en virtuell dator.

Du kan använda den [ **Write-Output** ](https://technet.microsoft.com/library/hh849921.aspx) aktivitet att mata ut namnen på virtuella datorer. Aktiviteten **Get-AzureRmVm** två parametrar de **virtuellt datornamn** och **resursgruppens namn**. Eftersom dessa parametrar kan kräva olika värden varje gång du startar runbooken, kan du lägga till indataparametrar i din runbook. Här följer stegen för att lägga till indataparametrar:

1. Välj den grafiska runbooken från den **Runbooks** bladet och klicka sedan på [ **redigera** ](automation-graphical-authoring-intro.md) den.
2. Runbook-redigeringsprogrammet klickar du på **indata och utdata** att öppna den **indata och utdata** bladet.
   
    ![Grafisk runbook för Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. Den **indata och utdata** bladet visar en lista över indataparametrar som har definierats för runbooken. På det här bladet kan du lägga till en indataparameter med nya eller redigera konfigurationen av en befintlig indataparameter. Lägg till en ny parameter för runbook, klicka på **Lägg till indata** att öppna den **runbookinmatningsparameter** bladet. Där kan konfigurera du följande parametrar:
   
   | **Egenskap** | **Beskrivning** |
   |:--- |:--- |
   | Namn |Krävs. Namnet på parametern. Detta måste vara unika inom en runbook och kan enbart innehålla bokstäver, siffror eller understreck. Det måste börja med en bokstav. |
   | Beskrivning |Valfri. Beskrivning av syftet med indataparameter. |
   | Typ |Valfri. Datatypen som kan förväntas för parametervärdet. Typer av parameterdefinitioner som stöds är **sträng**, **Int32**, **Int64**, **Decimal**, **booleskt**,  **DateTime**, och **objektet**. Om datatypen inte är markerad standard **sträng**. |
   | Obligatorisk |Valfri. Anger om ett värde måste anges för parametern. Om du väljer **Ja**, och sedan ett värde måste anges när runbooken startar. Om du väljer **inga**, och sedan ett värde inte krävs när runbooken startar och du kan endast ange ett standardvärde. |
   | Standardvärde |Valfri. Anger ett värde som används för parametern om ett värde inte skickas när runbooken startar. Ett standardvärde kan anges för en parameter som inte är obligatoriskt. Om du vill ange ett standardvärde, Välj **anpassad**. Det här värdet används om inte ett annat värde anges när runbooken startar. Välj **ingen** om du inte vill ange några standardvärdet. |
   
    ![Lägg till nya indata](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Skapa två parametrar med följande egenskaper som används av den **Get-AzureRmVm** aktivitet:
   
   * **Parameter1:**
     
     * Namn – VMName
     * Typ - sträng
     * Obligatorisk - Nej
   * **Parameter2:**
     
     * Namn - resourceGroupName
     * Typ - sträng
     * Obligatorisk - Nej
     * Standardvärde - anpassad
     * Anpassat standardvärde - \<namnet på resursgruppen som innehåller virtuella datorer >
5. När du lägger till parametrarna, klickar du på **OK**. Nu kan du visa dem i den **indata och utdatabladet**. Klicka på **OK** igen, och klicka sedan på **spara** och **publicera** din runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurera indataparametrar i Python-runbooks

Till skillnad från PowerShell PowerShell-arbetsflöde och grafiska runbooks vidtar Python runbooks inte namngivna parametrar.
Alla indataparametrar parsas som en matris med argumentvärden som.
Du har åtkomst till matrisen genom att importera den `sys` modulen till din Python-skriptet och sedan använda den `sys.argv` matris.
Det är viktigt att Observera att det första elementet i matrisen använder `sys.argv[0]`, är namnet på skriptet, så att den första faktiska Indataparametern är `sys.argv[1]`.

Ett exempel på hur du använder indataparametrar i en Python-runbook finns i [min första Python-runbook i Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Tilldela värden till indataparametrar i runbooks

Du kan skicka värden för att ange parametrar i runbooks i följande scenarier:

### <a name="start-a-runbook-and-assign-parameters"></a>Starta en runbook och tilldela parametrar

En runbook kan startas på många olika sätt: via Azure portal, med en webhook, med PowerShell-cmdletar, med REST API eller med SDK: N. Nedan diskuterar vi hur olika metoder för att starta en runbook och tilldela parametrar.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Starta en publicerad runbook med hjälp av Azure-portalen och tilldela parametrar

När du [starta runbooken](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), **starta Runbook** bladet öppnas och du kan ange värden för parametrarna som du skapade.

![Börja använda portalen](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Du kan se de attribut som har angetts för parametern i etiketten under rutan. Attribut är obligatorisk eller valfri typ och standardvärdet. I Hjälp-pratbubblor bredvid parameternamnet på ser du den viktiga information som du behöver fatta beslut om indata för parametervärden. Informationen omfattar om en parameter som är obligatoriska eller valfria. Den innehåller också typen och standardvärdet (om sådan finns) och annan användbar information.

> [!NOTE]
> Sträng stöd för parametrar av typen **tom** sträng värden.  Att ange **[EmptyString]** i Indataparametern box skickar en tom sträng för parametern. Dessutom stöder inte typen strängparametrar **Null** värden som skickas. Om du inte skickar något värde för parametern sträng är sedan tolkar PowerShell det som null.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Starta en publicerad runbook med hjälp av PowerShell-cmdlets och tilldela parametrar

* **Azure Resource Manager-cmdletar:** du kan starta en Automation-runbook som skapades i en resursgrupp med hjälp av [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Exempel:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Cmdlet: ar för klassiska Azure-modell:** du kan starta en automation-runbook som har skapats i en standardresursgrupp med hjälp av [Start AzureAutomationRunbook](https://docs.microsoft.com/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Exempel:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> När du startar en runbook med hjälp av PowerShell-cmdletar, en standardparameter **MicrosoftApplicationManagementStartedBy** har skapats med värdet **PowerShell**. Du kan visa den här parametern i den **Jobbdetaljer** bladet.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Starta en runbook med hjälp av ett SDK och tilldela parametrar

* **Azure Resource Manager-metod:** du kan starta en runbook med hjälp av SDK i ett programmeringsspråk. Nedan visas ett C# kodfragment för att starta en runbook i ditt Automation-konto. Du kan visa alla koden på vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```
* **Azure klassiska distributionsmodell-metod:** du kan starta en runbook med hjälp av SDK i ett programmeringsspråk. Nedan visas ett C# kodfragment för att starta en runbook i ditt Automation-konto. Du kan visa alla koden på vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```
  
  Skapa en ordlista för att lagra runbook-parametrar för att starta den här metoden **VMName** och **resourceGroupName**, och deras värden. Starta runbook. Nedan visas kodfragmentet C# för att anropa metoden som definieras ovan.
  
  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Starta en runbook med hjälp av REST-API och tilldela parametrar
Runbook-jobb kan skapas och startas med Azure Automation REST-API med hjälp av den **PLACERA** metoden med följande begärande-URI:

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

Ersätt följande parametrar i begärans URI:

* **prenumerations-id:** ditt Azure-prenumerations-ID.  
* **cloud-service-name:** namnet på molnet service för att som begäran ska skickas.  
* **Automation-kontonamn:** namnet på ditt automation-konto som finns inom den angivna Molntjänsten.  
* **jobb-id:** GUID för jobbet. GUID i PowerShell kan skapas med hjälp av den **[GUID]::NewGuid(). ToString ()** kommando.

Använda begärandetexten för att skicka parametrar till runbook-jobbet. Det tar följande två egenskaper i JSON-format:

* **Runbook-namn:** krävs. Namnet på runbooken för att jobbet ska starta.  
* **Runbook-parametrar:** valfritt. En ordlista med parameterlistan i (namn, värde)-format där namnet ska vara av typen sträng och värdet kan vara ett giltigt JSON-värde.

Om du vill starta den **Get-AzureVMTextual** runbook som du skapade tidigare med **VMName** och **resourceGroupName** som parametrar, använder du följande JSON-format för begärantexten.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

HTTP-statuskoden 201 returneras om jobbet har skapats. Mer information om svarshuvuden och svarstexten finns i artikeln om hur du [skapa ett runbook-jobb med hjälp av REST-API.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Testa en runbook och tilldela parametrar
När du [testa Utkastversionen av din runbook](automation-testing-runbook.md) med hjälp av alternativet test i **testa** öppnas och du kan konfigurera värden för parametrarna som du skapade.

![Testa och tilldela parametrar](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Länka ett schema till en runbook och tilldela parametrar
Du kan [länka ett schema](automation-schedules.md) i din runbook så att runbook startar vid en viss tidpunkt. Du kan tilldela indataparametrar när du skapar schemat och runbook använder dessa värden när den startas av schemat. Du kan inte spara schemat tills alla obligatorisk parametervärden anges.

![Schemalägga och tilldela parametrar](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Skapa en webhook för en runbook och tilldela parametrar
Du kan skapa en [webhook](automation-webhooks.md) för din runbook och konfigurera indataparametrar för runbook. Du kan inte spara webhooken tills alla obligatorisk parametervärden anges.

![Skapa webhook och tilldela parametrar](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

När du kör en runbook med en webhook, fördefinierade Indataparametern **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** skickas tillsammans med de indataparametrar som du har definierat. Du kan klicka på för att expandera den **WebhookData** parametern för mer information.

![WebhookData-parametern](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Nästa steg
* Mer information om runbook-indata och utdata finns i [Azure Automation: runbook indata, utdata och kapslade runbooks](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
* Mer information om olika sätt att starta en runbook finns [starta en runbook](automation-starting-a-runbook.md).
* Om du vill redigera en runbook som text, referera till [redigera runbooks med text](automation-edit-textual-runbook.md).
* Om du vill redigera en grafisk runbook, referera till [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

