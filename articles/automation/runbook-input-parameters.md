---
title: Indataparametrar för runbook
description: Inmatnings parametrar för Runbook ökar flexibiliteten i Runbooks genom att du kan skicka data till en runbook när den startas. I den här artikeln beskrivs olika scenarier där indataparametrar används i Runbooks.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be7d244f5aa422b2083d35fc56a52318a4379b79
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850235"
---
# <a name="runbook-input-parameters"></a>Indataparametrar för runbook

Inmatnings parametrar för Runbook ökar flexibiliteten i Runbooks genom att låta dig skicka data till den när den startas. Med parametrarna kan Runbook-åtgärder riktas mot specifika scenarier och miljöer. I den här artikeln får du gå igenom olika scenarier där indataparametrar används i Runbooks.

## <a name="configure-input-parameters"></a>Konfigurera indataparametrar

Indataparametrar kan konfigureras i PowerShell, PowerShell Workflow, python och grafiska runbooks. En Runbook kan ha flera parametrar med olika data typer eller inga parametrar alls. Indataparametrar kan vara obligatoriska eller valfria, och du kan ange ett standardvärde för valfria parametrar. Du tilldelar värden till indataparametrarna för en runbook när du startar den via någon av de tillgängliga metoderna. Dessa metoder omfattar att starta en Runbook från Azure Portal, en webb tjänst eller PowerShell. Du kan också starta en som underordnad Runbook som kallas infogad i en annan Runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurera indataparametrar i PowerShell-Runbooks

PowerShell-och PowerShell Workflow-Runbooks i Azure Automation stöder indataparametrar som definieras med följande attribut:  

| **Egenskap** | **Beskrivning** |
|:--- |:--- |
| `Type` |Krävs. Den datatyp som förväntas för parametervärdet. Alla .NET-typer är giltiga. |
| `Name` |Krävs. Parameterns namn. Detta måste vara unikt inom runbooken och får bara innehålla bokstäver, siffror eller under streck. Det måste börja med en bokstav. |
| `Mandatory` |Valfri. Anger om ett värde måste anges för parametern. Om du ställer in detta på **\$True**måste ett värde anges när runbooken startas. Om du anger värdet till **\$falskt**är ett värde valfritt. |
| `Default value` |Valfri. Anger ett värde som används för parametern om ett värde inte skickas i när runbooken startas. Du kan ange ett standardvärde för alla parametrar och göra parametern till valfri oavsett vilken inställning som är obligatorisk. |

Windows PowerShell stöder fler attribut för indataparametrar än de som anges här, t. ex. verifiering, alias och parameter uppsättningar. Azure Automation stöder för närvarande endast föregående indataparametrar.

En parameter definition i PowerShell Workflow-Runbooks har följande allmänna form, där flera parametrar skiljs åt av kommatecken.

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
> När du definierar parametrar, och om du inte anger det **obligatoriska** attributet, betraktas parametern som standard som valfri. Om du anger ett standardvärde för en parameter i PowerShell Workflow-Runbooks, hanteras den av PowerShell som en valfri parameter, oavsett vilket värde som är **obligatoriskt** .

Vi kan till exempel konfigurera indataparametrarna för en PowerShell Workflow-Runbook som visar information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer i en resurs grupp. Denna Runbook har två parametrar som visas på följande skärm bild: namnet på den virtuella datorn och namnet på resurs gruppen.

![Automation PowerShell-arbetsflöde](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

I den här parameter definitionen är parametrarna **\$VMName** och **\$resourceGroupName** enkla parametrar av typen sträng. PowerShell-och PowerShell Workflow-Runbooks stöder dock alla enkla typer och komplexa typer, till exempel **objekt** eller **PSCredential** för indataparametrar.

Om din Runbook har en indataparameter för objekt typen använder du en PowerShell-hash med (namn, värde) par för att skicka in ett värde. Om du till exempel har följande parameter i en Runbook:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Sedan kan du skicka följande värde till-parametern:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Om du inte skickar något värde till en valfri `[String]` typ parameter som har _standardvärdet_ `\$null`, är parameterns värde en _tom sträng_, **inte** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurera indataparametrar i grafiska runbooks

Om du vill [Konfigurera en grafisk Runbook](automation-first-runbook-graphical.md) med indataparametrar skapar vi en grafisk Runbook som visar information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer i en resurs grupp. Att konfigurera en Runbook består av två större aktiviteter, enligt beskrivningen nedan.

Autentisera [**Runbooks med Azure kör som-konto**](automation-sec-configure-azure-runas-account.md) för att autentisera med Azure.

[**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) för att hämta egenskaperna för en virtuell dator.

Du kan använda aktiviteten [**Skriv ut**](/powershell/module/microsoft.powershell.utility/write-output) för att mata ut namnen på virtuella datorer. Aktiviteten **Get-AzureRmVm** accepterar två parametrar, namnet på den **virtuella datorn** och **resurs gruppens namn**. Eftersom dessa parametrar kan kräva olika värden varje gång du startar en Runbook kan du lägga till indataparametrar i din Runbook. Här följer stegen för att lägga till indataparametrar:

1. Välj den grafiska runbooken från bladet **Runbooks** och klicka sedan på [**Redigera**](automation-graphical-authoring-intro.md) .
2. Öppna bladet **indata och utdata** genom att klicka på **indata och utdata** i Runbook-redigeraren.

   ![Grafisk Automation-Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Bladet **indata och utdata** visar en lista över indataparametrar som har definierats för runbooken. På det här bladet kan du antingen lägga till en ny indataparameter eller redigera konfigurationen för en befintlig indataparameter. Om du vill lägga till en ny parameter för Runbook klickar du på **Lägg till inmatare** för att öppna bladet **Runbook-indataparameter** . Där kan du konfigurera följande parametrar:

   | **Egenskap** | **Beskrivning** |
   |:--- |:--- |
   | `Name` |Krävs. Parameterns namn. Detta måste vara unikt inom runbooken och får bara innehålla bokstäver, siffror eller under streck. Det måste börja med en bokstav. |
   | `Description` |Valfri. Beskrivning av syftet med indataparametern. |
   | `Type` |Valfri. Den datatyp som förväntas för parametervärdet. Parameter typer som stöds är **sträng**, **Int32**, **Int64**, **decimal**, **Boolean**, **datetime**och **Object**. Om du inte väljer någon datatyp används **strängen**som standard. |
   | `Mandatory` |Valfri. Anger om ett värde måste anges för parametern. Om du väljer **Ja**måste ett värde anges när runbooken startas. Om du väljer **Nej**krävs inget värde när runbooken startas och ett standardvärde kan anges. |
   | `Default Value` |Valfri. Anger ett värde som används för parametern om ett värde inte skickas i när runbooken startas. Du kan ange ett standardvärde för en parameter som inte är obligatorisk. Om du vill ange ett standardvärde väljer du **anpassad**. Det här värdet används om inte ett annat värde anges när runbooken startas. Välj **ingen** om du inte vill ange något standardvärde. |

    ![Lägg till nya inmatade](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Skapa två parametrar med följande egenskaper som används av aktiviteten **Get-AzureRmVm** :

   * **Parameter1**
     * Namn – VMName
     * Typ sträng
     * Obligatoriskt-nej
   * **Parameter2:**
     * Namn – resourceGroupName
     * Typ sträng
     * Obligatoriskt-nej
     * Standardvärde – anpassad
     * Anpassat standardvärde – \<namnet på den resurs grupp som innehåller de virtuella datorerna\>

5. När du har lagt till parametrarna klickar du på **OK**. Nu kan du visa dem på **sidan för indata och utdata**. Klicka på **OK** igen och klicka sedan på **Spara** och **publicera** din Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurera indataparametrar i python-Runbooks

Till skillnad från PowerShell, PowerShell-arbetsflöde och grafiska runbooks tar python-Runbooks inte med namngivna parametrar.
Alla indataparametrar parsas som en matris med argument värden.
Du kommer åt matrisen genom att importera `sys`-modulen till python-skriptet och sedan använda `sys.argv` matrisen.
Det är viktigt att Observera att det första elementet i matrisen `sys.argv[0]`är namnet på skriptet, så den första faktiska Indataparametern är `sys.argv[1]`.

Ett exempel på hur du använder indataparametrar i en python-Runbook finns i [min första python-Runbook i Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Tilldela värden till indataparametrar i Runbooks

Du kan skicka värden till indataparametrar i Runbooks i följande scenarier:

### <a name="start-a-runbook-and-assign-parameters"></a>Starta en Runbook och tilldela parametrar

En Runbook kan startas på många sätt: via Azure Portal, med en webhook, med PowerShell-cmdletar, med REST API eller med SDK. Nedan diskuterar vi olika metoder för att starta en Runbook och tilldela parametrar.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Starta en publicerad Runbook med hjälp av Azure Portal och tilldela parametrar

När du [startar runbooken](start-runbooks.md#start-a-runbook-with-the-azure-portal)öppnas bladet **starta Runbook** och du kan ange värden för de parametrar som du har skapat.

![Börja använda portalen](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

I etiketten under indatatypen kan du se de attribut som har angetts för parametern. Attributen är obligatoriska eller valfria, typ och standardvärde. I prat bubblan bredvid parameter namnet kan du se all viktig information som du behöver för att fatta beslut om värden för parameter indata. Den här informationen inkluderar huruvida en parameter är obligatorisk eller valfri. Den innehåller också typen och standardvärdet (om det finns några) och andra användbara anteckningar.

> [!NOTE]
> Sträng typ parametrarna stöder **tomma** sträng värden.  Om du anger **[EmptyString]** i rutan parameter för inmatnings parameter skickas en tom sträng till parametern. Parametrar av sträng typ stöder inte heller **Null** -värden som skickas. Om du inte skickar något värde till sträng parametern tolkar PowerShell det som null.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Starta en publicerad Runbook med hjälp av PowerShell-cmdletar och tilldela parametrar

* **Azure Resource Manager-cmdlet: ar:** Du kan starta en Automation-Runbook som skapats i en resurs grupp med hjälp av [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Exempel:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Cmdlets för klassiska Azure-distributions modeller:** Du kan starta en Automation-Runbook som skapats i en standard resurs grupp med hjälp av [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Exempel:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> När du startar en Runbook med hjälp av PowerShell-cmdletar skapas en standard parameter **MicrosoftApplicationManagementStartedBy** med värdet **PowerShell**. Du kan visa den här parametern på sidan **jobb information** .  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Starta en Runbook med hjälp av en SDK och tilldela parametrar

* **Azure Resource Manager metod:** Du kan starta en Runbook med SDK för ett programmeringsspråk. Nedan visas ett C# kodfragment för att starta en Runbook i ditt Automation-konto. Du kan visa all kod på vår [GitHub-lagringsplats](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Metod för klassisk distributions modell för Azure:** Du kan starta en Runbook med SDK för ett programmeringsspråk. Nedan visas ett C# kodfragment för att starta en Runbook i ditt Automation-konto. Du kan visa all kod på vår [GitHub-lagringsplats](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Starta den här metoden genom att skapa en ord lista för att lagra Runbook-parametrarna, **VMName** och **resourceGroupName**och deras värden. Starta sedan runbooken. Nedan visas C# kodfragmentet för att anropa metoden som definieras ovan.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Starta en Runbook med hjälp av REST API och tilldela parametrar

Ett Runbook-jobb kan skapas och startas med Azure Automation REST API med hjälp av metoden för att **Skicka** med följande begär ande-URI: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


Ersätt följande parametrar i begärande-URI: n:

* **subscriptionId:** Ditt Azure-prenumerations-ID.  
* **resourceGroupName:** Namnet på resurs gruppen för Automation-kontot.
* **automationAccountName:** Namnet på det Automation-konto som finns i den angivna moln tjänsten.  
* **jobName:** GUID för jobbet. GUID i PowerShell kan skapas med hjälp av **[GUID]:: NewGuid (). ToString ()-** kommando.

För att kunna skicka parametrar till Runbook-jobbet använder du begär ande texten. Det tar följande två egenskaper i JSON-format:

* **Runbook-namn:** Kunna. Namnet på runbooken som jobbet ska starta.  
* **Runbook-parametrar:** Valfritt. En ord lista för parameter listan i (namn, värde) format där namnet ska vara av sträng typ och värdet kan vara ett giltigt JSON-värde.

Om du vill starta Runbook **Get-AzureVMTextual** som skapades tidigare med parametrarna **VMName** och **resourceGroupName** som parametrar använder du följande JSON-format för begär ande texten.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
   ```

En HTTP-statuskod 201 returneras om jobbet har skapats. Mer information om svarshuvuden och svars texten finns i artikeln om hur du [skapar ett Runbook-jobb med hjälp av REST API.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Testa en Runbook och tilldela parametrar

När du [testar utkast versionen av din Runbook](automation-testing-runbook.md) med hjälp av alternativet test öppnas **test** sidan och du kan konfigurera värden för de parametrar som du har skapat.

![Testa och tilldela parametrar](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Länka ett schema till en Runbook och tilldela parametrar

Du kan [Länka ett schema](automation-schedules.md) till din Runbook så att runbooken startar vid en angiven tidpunkt. Du tilldelar indataparametrar när du skapar schemat, och runbooken använder dessa värden när det startas av schemat. Du kan inte spara schemat förrän alla obligatoriska parameter värden har angetts.

![Schemalägga och tilldela parametrar](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Skapa en webhook för en Runbook och tilldela parametrar

Du kan skapa en [webhook](automation-webhooks.md) för din Runbook och konfigurera indataparametrar för Runbook. Det går inte att spara webhooken förrän alla obligatoriska parameter värden har angetts.

![Skapa webhook och tilldela parametrar](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

När du kör en Runbook med en webhook skickas den fördefinierade Indataparametern **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** , tillsammans med de indataparametrar som du har definierat. Du kan klicka för att expandera parametern **WebhookData** för mer information.

![WebhookData-parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Skicka ett JSON-objekt till en Runbook

Det kan vara användbart att lagra data som du vill skicka till en Runbook i en JSON-fil.
Du kan till exempel skapa en JSON-fil som innehåller alla parametrar som du vill skicka till en Runbook. Om du vill göra detta måste du konvertera JSON till en sträng och sedan konvertera strängen till ett PowerShell-objekt innan den skickas till runbooken.

I det här exemplet har du ett PowerShell-skript som anropar [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) för att starta en PowerShell-Runbook och skicka innehållet i JSON till Runbook.
PowerShell-runbooken startar en virtuell Azure-dator och hämtar parametrarna för den virtuella datorn från JSON som skickades.

### <a name="create-the-json-file"></a>Skapa JSON-filen

Skriv följande test i en textfil och spara det som `test.json` någonstans på den lokala datorn.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Skapa Runbook

Skapa en ny PowerShell-Runbook med namnet "test-JSON" i Azure Automation.
Information om hur du skapar en ny PowerShell-Runbook finns i [min första PowerShell-Runbook](automation-first-runbook-textual-powershell.md).

För att acceptera JSON-data måste runbooken ta ett objekt som en indataparameter.

Runbooken kan sedan använda de egenskaper som definierats i JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Spara och publicera denna Runbook i ditt Automation-konto.

### <a name="call-the-runbook-from-powershell"></a>Anropa runbooken från PowerShell

Nu kan du anropa runbooken från den lokala datorn med hjälp av Azure PowerShell.
Kör följande PowerShell-kommandon:

1. Logga in på Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

   Du uppmanas att ange dina autentiseringsuppgifter för Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRmAccount**. Om du inte ser **Connect-AzureRMAccount**när du söker i biblioteks objekt kan du använda **Add-AzureRMAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

1. Hämta innehållet i JSON-filen och konvertera den till en sträng:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` är sökvägen dit du sparade JSON-filen.

1. Konvertera sträng innehållet i `$json` till ett PowerShell-objekt:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Skapa en hash-tabellen för parametrarna för `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observera att du anger värdet för `Parameters` till PowerShell-objektet som innehåller värdena från JSON-filen.
1. Starta runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Nästa steg

* Information om olika sätt att starta en Runbook finns i [starta en Runbook](automation-starting-a-runbook.md).
* Information om hur du redigerar en text-Runbook finns i [Redigera text Runbooks](automation-edit-textual-runbook.md).
* Om du vill redigera en grafisk Runbook, se [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
