---
title: Indataparametrar för Runbook
description: Inmatnings parametrar för Runbook ökar flexibiliteten i Runbooks genom att du kan skicka data till en runbook när den startas. I den här artikeln beskrivs olika scenarier där indataparametrar används i Runbooks.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 17be351d4af3d277242af70ea96e8735a5f68bc9
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329093"
---
# <a name="runbook-input-parameters"></a>Indataparametrar för Runbook

Med inmatnings parametrar för Runbook ökar flexibiliteten i en Runbook genom att data skickas till den när den startas. Med de här parametrarna kan Runbook-åtgärder riktas mot specifika scenarier och miljöer. Den här artikeln beskriver konfigurationen och användningen av indataparametrar i dina runbooks.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Konfigurera indataparametrar

Du kan konfigurera indataparametrar för PowerShell-, PowerShell Workflow-, Graphic-och python-Runbooks. En Runbook kan ha flera parametrar med olika data typer eller inga parametrar alls. Indataparametrar kan vara obligatoriska eller valfria, och du kan använda standardvärden för valfria parametrar.

Du tilldelar värden till indataparametrarna för en runbook när du startar den. Du kan starta en Runbook från Azure Portal, en webb tjänst eller PowerShell. Du kan också starta en som underordnad Runbook som kallas infogad i en annan Runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurera indataparametrar i PowerShell-Runbooks

PowerShell-och PowerShell Workflow-Runbooks i Azure Automation stöder indataparametrar som definieras genom följande egenskaper. 

| **Egenskap** | **Beskrivning** |
|:--- |:--- |
| Typ |Krävs. Den datatyp som förväntas för parametervärdet. Alla .NET-typer är giltiga. |
| Namn |Krävs. Parameterns namn. Det här namnet måste vara unikt inom runbooken, måste börja med en bokstav och får bara innehålla bokstäver, siffror eller under streck. |
| Obligatorisk |Valfri. Booleskt värde som anger om parametern kräver ett värde. Om du ställer in värdet **True**måste ett värde anges när runbooken startas. Om du anger värdet till **falskt**är ett värde valfritt. Om du inte anger något värde för den **obligatoriska** egenskapen, anser PowerShell att Indataparametern är valfri som standard. |
| Standardvärde |Valfri. Ett värde som används för parametern om inget indatavärde skickas i när runbooken startar. Runbooken kan ange ett standardvärde för alla parametrar. |

Windows PowerShell stöder fler attribut för indataparametrar än de som anges ovan, till exempel verifiering, alias och parameter uppsättningar. Azure Automation stöder för närvarande bara de angivna egenskaperna för indataparametrar.

Till exempel kan vi titta på en parameter definition i en PowerShell-Runbook för arbets flöden. Den här definitionen har följande allmänna form, där flera parametrar skiljs åt av kommatecken.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Nu ska vi konfigurera indataparametrarna för en PowerShell Workflow-Runbook som visar information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer i en resurs grupp. Denna Runbook har två parametrar, som visas i följande skärm bild: namnet på den virtuella datorn (*VMName*) och namnet på resurs gruppen (*resourceGroupName*).

![Automation PowerShell-arbetsflöde](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

I den här parameter definitionen är indataparametrarna enkla parametrar av typen sträng.

Observera att PowerShell-och PowerShell Workflow-Runbooks stöder alla enkla typer och komplexa typer, t. ex. **objekt** eller **PSCredential** för indataparametrar. Om din Runbook har en indataparameter för inobjekt måste du använda en PowerShell-hash med namn-värdepar för att skicka in ett värde. Du kan till exempel ha följande parameter i en Runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

I det här fallet kan du skicka följande värde till-parametern.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Om du inte skickar ett värde till en valfri sträng parameter med ett null-standardvärde, är värdet för parametern en tom sträng i stället för **Null**.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurera indataparametrar i grafiska runbooks

För att illustrera konfigurationen av indataparametrar för en grafisk Runbook, ska vi skapa en Runbook som visar information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer i en resurs grupp. Mer information finns i [min första grafiska Runbook](automation-first-runbook-graphical.md).

En grafisk Runbook använder dessa viktiga Runbook-aktiviteter:

* Konfiguration av kör som-kontot i Azure för autentisering med Azure. 
* Definition av en [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) -cmdlet för att hämta egenskaper för virtuella datorer.
* Använd aktiviteten [Skriv-output](/powershell/module/microsoft.powershell.utility/write-output) för att mata ut VM-namnen. 

**Get-AzVM** -aktiviteten definierar två indata, namnet på den virtuella datorn och resurs gruppens namn. Eftersom dessa namn kan vara olika varje gång som Runbook startar, måste du lägga till indataparametrar i din Runbook för att acceptera dessa indata. Se [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

Följ dessa steg om du vill konfigurera indataparametrarna.

1. Välj den grafiska runbooken på sidan **Runbooks** och klicka sedan på **Redigera**.
2. I den grafiska redigeraren klickar du på knappen **indata och utdata och** **lägger sedan till indata** för att öppna fönstret inmatnings parameter för Runbook.

   ![Grafisk Automation-Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. I indata-och utdata-kontrollen visas en lista med indataparametrar som har definierats för runbooken. Här kan du antingen lägga till en ny indataparameter eller redigera konfigurationen för en befintlig indataparameter. Om du vill lägga till en ny parameter för Runbook klickar du på **Lägg till inmatare** för att öppna bladet **Runbook-indataparameter** där du kan konfigurera parametrar med hjälp av de egenskaper som definierats i [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

    ![Lägg till nya inmatade](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Skapa två parametrar med följande egenskaper som ska användas av **Get-AzVM** -aktiviteten och klicka sedan på **OK**.

   * Parameter 1:
        * **Namn** -- **VMName**
        * **Typ** --sträng
        * **Obligatoriskt** -- **Nej**

   * Parameter 2:
        * **Namn** -- **resourceGroupName**
        * **Typ** --sträng
        * **Obligatoriskt** -- **Nej**
        * **Standardvärde** -- **anpassad**
        * Anpassat standardvärde--namn på den resurs grupp som innehåller de virtuella datorerna

5. Visa parametrarna i indata-och utdata-kontrollen. 
6. Klicka på **OK** igen och klicka sedan på **Spara**.
7. Klicka på **publicera** för att publicera din Runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurera indataparametrar i python-Runbooks

Till skillnad från PowerShell, PowerShell-arbetsflöde och grafiska runbooks tar python-Runbooks inte med namngivna parametrar. Runbook-redigeraren parsar alla indataparametrar som en matris med argument värden. Du kan komma åt matrisen genom att importera **sys** -modulen till python-skriptet och sedan använda **sys. argv** -matrisen. Det är viktigt att Observera att det första elementet i matrisen `sys.argv[0]`är namnet på skriptet. Därför är den första Actual-Indataparametern *sys. argv [1]* .

Ett exempel på hur du använder indataparametrar i en python-Runbook finns i [min första python-Runbook i Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Tilldela värden till indataparametrar i Runbooks

I det här avsnittet beskrivs olika sätt att skicka värden till indataparametrar i Runbooks. Du kan tilldela parameter värden när du:

* [Starta en Runbook](#start-a-runbook-and-assign-parameters)
* [Testa en Runbook](#test-a-runbook-and-assign-parameters)
* [Länka ett schema för Runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Skapa en webhook för Runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Starta en Runbook och tilldela parametrar

En Runbook kan startas på många sätt: via Azure Portal, med en webhook, med PowerShell-cmdletar, med REST API eller med SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Starta en publicerad Runbook med hjälp av Azure Portal och tilldela parametrar

När du [startar runbooken](start-runbooks.md#start-a-runbook-with-the-azure-portal) i Azure Portal öppnas bladet **starta Runbook** och du kan ange värden för de parametrar som du har skapat.

![Börja använda portalen](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

I etiketten under indatatypen kan du se de egenskaper som har ställts in för att definiera parameter-attribut, till exempel obligatorisk eller valfri, typ, standardvärde. Hjälp ballongen bredvid parameter namnet definierar också den viktig information som krävs för att fatta beslut om värden för parameter värden. 

> [!NOTE]
> Sträng parametrar stöder tomma värden av typen sträng. Om du anger **[EmptyString]** i rutan parameter för inmatnings parameter skickas en tom sträng till parametern. Sträng parametrar stöder inte heller null. Om du inte skickar något värde till en sträng parameter tolkar PowerShell det som null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Starta en publicerad Runbook med PowerShell-cmdletar och tilldela parametrar

* **Azure Resource Manager-cmdlet: ar:** Du kan starta en Automation-Runbook som skapats i en resurs grupp med hjälp av [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets för klassiska Azure-distributions modeller:** Du kan starta en Automation-Runbook som skapats i en standard resurs grupp med hjälp av [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> När du startar en Runbook med hjälp av PowerShell-cmdletar skapas en standard parameter, *MicrosoftApplicationManagementStartedBy*, med värdet **PowerShell**. Du kan visa den här parametern i fönstret jobb information.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Starta en Runbook med hjälp av en SDK och tilldela parametrar

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

   Starta den här metoden genom att skapa en ord lista för att lagra Runbook-parametrarna *VMName* och *resourceGroupName* och deras värden. Starta sedan runbooken. Nedan visas C# kodfragmentet för att anropa metoden som definieras ovan.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Starta en Runbook med hjälp av REST API och tilldela parametrar

Du kan skapa och starta ett Runbook-jobb med Azure Automation REST API genom att använda metoden för att **Skicka** med följande begär ande-URI: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Ersätt följande parametrar i begärande-URI: n:

* *subscriptionId*: ditt Azure-prenumerations-ID.  
* *resourceGroupName*: namnet på resurs gruppen för Automation-kontot.
* *automationAccountName*: namnet på Automation-kontot som finns i den angivna moln tjänsten.  
* *jobName*: GUID för jobbet. GUID i PowerShell kan skapas med hjälp av `[GUID]::NewGuid().ToString()*`.

Använd begär ande texten för att skicka parametrar till Runbook-jobbet. Den tar med följande information, som finns i JSON-format:

* Runbook-namn: obligatoriskt. Namnet på runbooken som jobbet ska starta.  
* Runbook-parametrar: valfritt. En ord lista med parameter listan i (namn, värde) format, där namnet är av typen sträng och värdet kan vara ett giltigt JSON-värde.

Om du vill starta en **Get-AzureVMTextual** -Runbook som skapats tidigare med parametrarna *VMName* och *resourceGroupName* som parametrar använder du följande JSON-format för begär ande texten.

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

En HTTP-statuskod 201 returneras om jobbet har skapats. Mer information om svarshuvuden och svars texten finns i [skapa ett Runbook-jobb med hjälp av REST API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testa en Runbook och tilldela parametrar

När du [testar utkast versionen av din Runbook](automation-testing-runbook.md) med hjälp av alternativet test öppnas **test** sidan. Använd den här sidan om du vill konfigurera värden för de parametrar som du har skapat.

![Testa och tilldela parametrar](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Länka ett schema till en Runbook och tilldela parametrar

Du kan [Länka ett schema](automation-schedules.md) till din Runbook så att runbooken startar vid en angiven tidpunkt. Du tilldelar indataparametrar när du skapar schemat, och runbooken använder dessa värden när det startas av schemat. Du kan inte spara schemat förrän alla obligatoriska parameter värden har angetts.

![Schemalägga och tilldela parametrar](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Skapa en webhook för en Runbook och tilldela parametrar

Du kan skapa en [webhook](automation-webhooks.md) för din Runbook och konfigurera indataparametrar för Runbook. Det går inte att spara webhooken förrän alla obligatoriska parameter värden har angetts.

![Skapa webhook och tilldela parametrar](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

När du kör en Runbook med en webhook skickas den fördefinierade Indataparametern *[WebhookData](automation-webhooks.md)* , tillsammans med de indataparametrar som du definierar. 

![WebhookData-parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Skicka ett JSON-objekt till en Runbook

Det kan vara användbart att lagra data som du vill skicka till en Runbook i en JSON-fil. Du kan till exempel skapa en JSON-fil som innehåller alla parametrar som du vill skicka till en Runbook. Om du vill göra det måste du konvertera JSON-koden till en sträng och sedan konvertera strängen till ett PowerShell-objekt innan du skickar den till Runbook.

I det här avsnittet används ett exempel där ett PowerShell-skript anropar [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) för att starta en PowerShell-Runbook som skickar innehållet i JSON-filen till runbooken. PowerShell-runbooken startar en virtuell Azure-dator genom att hämta parametrarna för den virtuella datorn från JSON-objektet.

### <a name="create-the-json-file"></a>Skapa JSON-filen

Skriv följande kod i en textfil och spara den som `test.json` någonstans på den lokala datorn.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Skapa Runbook

Skapa en ny PowerShell-Runbook med namnet **test-JSON** i Azure Automation. Se [min första PowerShell-Runbook](automation-first-runbook-textual-powershell.md).

För att acceptera JSON-data måste runbooken ta ett objekt som en indataparameter. Runbooken kan sedan använda de egenskaper som definierats i JSON-filen.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Spara och publicera denna Runbook i ditt Automation-konto.

### <a name="call-the-runbook-from-powershell"></a>Anropa runbooken från PowerShell

Nu kan du anropa runbooken från den lokala datorn med hjälp av Azure PowerShell. 

1. Logga in på Azure på det sätt som visas. Efteråt uppmanas du att ange dina autentiseringsuppgifter för Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >För PowerShell-Runbooks är **Add-AzAccount** och **Add-AzureRMAccount** alias för **Connect-AzAccount**. Observera att dessa alias inte är tillgängliga för grafiska runbooks. En grafisk Runbook kan bara använda **Connect-AzAccount** .

1. Hämta innehållet i den sparade JSON-filen och konvertera den till en sträng. `JsonPath` är sökvägen dit du sparade JSON-filen.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Konvertera sträng innehållet i `$json` till ett PowerShell-objekt.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Skapa en hash-tabellen för parametrarna för **Start-AzAutomationRunbook**. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observera att du anger värdet för *parametrar* till PowerShell-objektet som innehåller värdena från JSON-filen.
1. Starta runbooken.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Nästa steg

* Information om olika sätt att starta en Runbook finns i [starta en Runbook](automation-starting-a-runbook.md).
* Information om hur du redigerar en text-Runbook finns i [Redigera text Runbooks](automation-edit-textual-runbook.md).
* Om du vill redigera en grafisk Runbook, se [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
