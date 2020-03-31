---
title: Indataparametrar för Runbook
description: Runbook-inmatningsparametrar ökar flexibiliteten för runbooks genom att du kan skicka data till en runbook när den startas. I den här artikeln beskrivs olika scenarier där indataparametrar används i runbooks.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 17be351d4af3d277242af70ea96e8735a5f68bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329093"
---
# <a name="runbook-input-parameters"></a>Indataparametrar för Runbook

Runbook-inmatningsparametrar ökar flexibiliteten i en runbook genom att tillåta att data skickas till den när den startas. Dessa parametrar gör att runbook-åtgärder kan riktas för specifika scenarier och miljöer. I den här artikeln beskrivs konfigurationen och användningen av indataparametrar i runbooks.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Konfigurera indataparametrar

Du kan konfigurera indataparametrar för PowerShell-, PowerShell-arbetsflöde, grafiska och Python-runbooks. En runbook kan ha flera parametrar med olika datatyper eller inga parametrar alls. Indataparametrar kan vara obligatoriska eller valfria och du kan använda standardvärden för valfria parametrar.

Du tilldelar värden till indataparametrarna för en runbook när du startar den. Du kan starta en runbook från Azure-portalen, en webbtjänst eller PowerShell. Du kan också starta en som en underordnad runbook som kallas infogad i en annan runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurera indataparametrar i PowerShell-runbooks

PowerShell- och PowerShell-arbetsflödeskörningsböcker i Azure Automation stöder indataparametrar som definieras via följande egenskaper. 

| **Egenskap** | **Beskrivning** |
|:--- |:--- |
| Typ |Krävs. Den datatyp som förväntas för parametervärdet. Alla .NET-typer är giltiga. |
| Namn |Krävs. Namnet på parametern. Det här namnet måste vara unikt i runbooken, börja med en bokstav och bara innehålla bokstäver, siffror eller understreck. |
| Obligatorisk |Valfri. Booleskt värde som anger om parametern kräver ett värde. Om du anger detta till **true**måste ett värde anges när runbooken startas. Om du anger detta till **false**är ett värde valfritt. Om du inte anger något värde för egenskapen **Obligatorisk** betraktas indataparametern som standard. |
| Standardvärde |Valfri. Ett värde som används för parametern om inget indatavärde skickas in när runbooken startar. Runbooken kan ange ett standardvärde för alla parametrar. |

Windows PowerShell stöder fler attribut för indataparametrar än de som anges ovan, till exempel validering, alias och parameteruppsättningar. Azure Automation stöder dock för närvarande endast de angivna indataparameteregenskaperna.

Låt oss till exempel titta på en parameterdefinition i en PowerShell-arbetsflödeskörningsbok. Den här definitionen har följande allmänna formulär, där flera parametrar avgränsas med kommatecken.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Nu ska vi konfigurera indataparametrarna för en PowerShell-arbetsflödeskörningsbok som matar ut information om virtuella datorer, antingen en enda virtuell dator eller alla virtuella datorer inom en resursgrupp. Den här runbooken har två parametrar, som visas i följande skärmbild: namnet på den virtuella datorn (*VMName*) och namnet på resursgruppen (*resourceGroupName*).

![Automation PowerShell-arbetsflöde](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

I den här parameterdefinitionen är indataparametrarna enkla parametrar för typsträngen.

Observera att PowerShell- och PowerShell-arbetsflödeskörningsböcker stöder alla enkla typer och komplexa typer, till exempel **objekt** eller **PSCredential** för indataparametrar. Om runbooken har en parameter för objektindata måste du använda en PowerShell-hashtable med namnvärdespar för att skicka in ett värde. Du har till exempel följande parameter i en runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

I det här fallet kan du skicka följande värde till parametern.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> När du inte skickar ett värde till en valfri strängparameter med ett null-standardvärde är parameterns värde en tom sträng i stället för **Null**.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurera indataparametrar i grafiska runbooks

Om du vill illustrera konfigurationen av indataparametrar för en grafisk runbook ska vi skapa en runbook som matar ut information om virtuella datorer, antingen en enda virtuell dator eller alla virtuella datorer inom en resursgrupp. Mer information finns i [Min första grafiska runbook](automation-first-runbook-graphical.md).

En grafisk runbook använder dessa stora runbook aktiviteter:

* Konfiguration av Azure Run As-kontot för att autentisera med Azure. 
* Definition av en [Get-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) för att hämta VM-egenskaper.
* Användning av [skrivutdataaktiviteten](/powershell/module/microsoft.powershell.utility/write-output) för att mata ut VM-namnen. 

**Get-AzVM-aktiviteten** definierar två indata, vm-namnet och resursgruppsnamnet. Eftersom dessa namn kan vara olika varje gång runbooken startar måste du lägga till indataparametrar i runbooken för att acceptera dessa indata. Se [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

Följ dessa steg för att konfigurera indataparametrarna.

1. Markera den grafiska runbooken på sidan **Runbooks** och klicka sedan på **Redigera**.
2. I den grafiska redigeraren klickar du på knappen **In- och utdata och** sedan **lägger till indata** för att öppna fönstret Runbook Input Parameter.

   ![Grafisk körningsbok för automatisering](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Kontrollen In- och utdata visar en lista över indataparametrar som har definierats för runbooken. Här kan du antingen lägga till en ny indataparameter eller redigera konfigurationen av en befintlig indataparameter. Om du vill lägga till en ny parameter för runbooken klickar du på **Lägg till indata** för att öppna **bladet Runbook-indataparameter** där du kan konfigurera parametrar med hjälp av egenskaperna som definierats i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

    ![Lägg till ny indata](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Skapa två parametrar med följande egenskaper som ska användas av aktiviteten **Get-AzVM** och klicka sedan på **OK**.

   * Parameter 1:
        * **Namn** -- **VMName**
        * **Typ** -- Sträng
        * **Obligatoriskt** -- **nej**

   * Parameter 2:
        * **Namn** -- **resourceGroupName**
        * **Typ** -- Sträng
        * **Obligatoriskt** -- **nej**
        * **Anpassat standardvärde** -- **Custom**
        * Anpassat standardvärde – Namnet på resursgruppen som innehåller de virtuella datorerna

5. Visa parametrarna i kontrollen In- och utdata. 
6. Klicka på **OK** igen och sedan på **Spara**.
7. Klicka på **Publicera** om du vill publicera runbooken.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurera indataparametrar i Python-runbooks

Till skillnad från PowerShell, PowerShell-arbetsflöde och grafiska runbooks tar Python-runbooks inte namngivna parametrar. Runbook editor tolkar alla indataparametrar som en matris med argumentvärden. Du kan komma åt matrisen genom att importera **sysmodulen** till Python-skriptet och sedan använda **sys.argv-matrisen.** Det är viktigt att notera att det `sys.argv[0]`första elementet i matrisen, är namnet på skriptet. Därför är den första faktiska indataparametern *sys.argv[1]*.

Ett exempel på hur du använder indataparametrar i en Python-runbook finns [i Min första Python-runbook i Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Tilldela värden till indataparametrar i runbooks

I det här avsnittet beskrivs flera sätt att skicka värden till indataparametrar i runbooks. Du kan tilldela parametervärden när du:

* [Starta en Runbook](#start-a-runbook-and-assign-parameters)
* [Testa en Runbook](#test-a-runbook-and-assign-parameters)
* [Länka ett schema för runbooken](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Skapa en webhook för runbooken](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Starta en runbook och tilldela parametrar

En runbook kan startas på många sätt: via Azure-portalen, med en webhook, med PowerShell-cmdlets, med REST API eller med SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Starta en publicerad runbook med Azure-portalen och tilldela parametrar

När du [startar runbooken](start-runbooks.md#start-a-runbook-with-the-azure-portal) i Azure-portalen öppnas **bladet Start Runbook** och du kan ange värden för de parametrar som du har skapat.

![Börja använda portalen](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

I etiketten under indatarutan kan du se de egenskaper som har ställts in för att definiera parameterattribut, till exempel obligatoriska eller valfria, typ, standardvärde. Hjälpbubblan bredvid parameternamnet definierar också den nyckelinformation som behövs för att fatta beslut om parameterindatavärden. 

> [!NOTE]
> Strängparametrar stöder tomma värden av typen Sträng. Om du anger **[EmptyString]** i parameterrutan indata skickas en tom sträng till parametern. Strängparametrar stöder inte heller Null. Om du inte skickar något värde till en strängparameter tolkar PowerShell det som Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Starta en publicerad runbook med PowerShell-cmdletar och tilldela parametrar

* **Cmdlets för Azure Resource Manager:** Du kan starta en Automation-runbook som har skapats i en resursgrupp med [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure klassiska distributionsmodell cmdlets:** Du kan starta en automatiseringskörning som har skapats i en standardresursgrupp med [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> När du startar en runbook med PowerShell-cmdlets skapas en standardparameter, *MicrosoftApplicationManagementStartedBy,* med värdet **PowerShell**. Du kan visa den här parametern i fönstret Jobbinformation.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Starta en runbook med hjälp av en SDK och tilldela parametrar

* **Azure Resource Manager-metod:** Du kan starta en runbook med hjälp av SDK för ett programmeringsspråk. Nedan visas ett C#-kodavsnitt för att starta en runbook i ditt Automation-konto. Du kan visa all kod i vårt [GitHub-arkiv](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Azure klassisk distributionsmodell metod:** Du kan starta en runbook med hjälp av SDK för ett programmeringsspråk. Nedan visas ett C#-kodavsnitt för att starta en runbook i ditt Automation-konto. Du kan visa all kod i vårt [GitHub-arkiv](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

   Starta den här metoden genom att skapa en ordlista för att lagra runbookparametrarna *VMName* och *resourceGroupName* och deras värden. Starta sedan runbooken. Nedan visas C#-kodavsnittet för att anropa metoden som definieras ovan.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Starta en runbook med REST API och tilldela parametrar

Du kan skapa och starta ett runbook-jobb med Azure Automation REST API med hjälp av **PUT-metoden** med följande begäran URI:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

I URI-begäran ersätter du följande parametrar:

* *subscriptionId*: Ditt Azure-prenumerations-ID.  
* *resourceGroupName*: Namnet på resursgruppen för Automation-kontot.
* *automationAccountName*: Namnet på automationskontot som finns inom den angivna molntjänsten.  
* *jobName*: GUID för jobbet. GUIDs i PowerShell kan `[GUID]::NewGuid().ToString()*`skapas med hjälp av .

Om du vill skicka parametrar till runbook-jobbet använder du begärandetexten. Det tar följande information, som tillhandahålls i JSON-format:

* Runbook namn: Krävs. Namnet på runbook för jobbet att starta.  
* Runbook parametrar: Valfritt. En ordlista med parameterlistan i (namn, värde) format, där namnet är av typen Sträng och värde kan vara valfritt giltigt JSON-värde.

Om du vill starta **get-AzureVMTextual runbook** som skapats tidigare med *VMName* och *resourceGroupName* som parametrar använder du följande JSON-format för begäran.

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

En HTTP-statuskod 201 returneras om jobbet har skapats. Mer information om svarshuvuden och svarstexten finns i [skapa ett runbook-jobb med hjälp av REST API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testa en runbook och tilldela parametrar

När du [testar utkastversionen av runbooken](automation-testing-runbook.md) med hjälp av testalternativet öppnas sidan **Testa.** Använd den här sidan om du vill konfigurera värden för de parametrar som du har skapat.

![Testa och tilldela parametrar](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Länka ett schema till en runbook och tilldela parametrar

Du kan [länka ett schema](automation-schedules.md) till runbooken så att runbooken startar vid en viss tidpunkt. Du tilldelar indataparametrar när du skapar schemat och runbooken använder dessa värden när det startas av schemat. Du kan inte spara schemat förrän alla obligatoriska parametervärden har angetts.

![Schemalägga och tilldela parametrar](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Skapa en webhook för en runbook och tilldela parametrar

Du kan skapa en [webhook](automation-webhooks.md) för runbook och konfigurera runbook inmatningsparametrar. Du kan inte spara webhooken förrän alla obligatoriska parametervärden anges.

![Skapa webhook och tilldela parametrar](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

När du kör en runbook med hjälp av en webhook skickas den fördefinierade indataparametern *[WebhookData](automation-webhooks.md)* tillsammans med de indataparametrar som du definierar. 

![Parametern WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Skicka ett JSON-objekt till en runbook

Det kan vara användbart att lagra data som du vill skicka till en runbook i en JSON-fil. Du kan till exempel skapa en JSON-fil som innehåller alla parametrar som du vill skicka till en runbook. För att göra detta måste du konvertera JSON-koden till en sträng och sedan konvertera strängen till ett PowerShell-objekt innan du skickar den till runbooken.

I det här avsnittet används ett exempel där ett PowerShell-skript anropar [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) för att starta en PowerShell-runbook och skickar innehållet i JSON-filen till runbooken. PowerShell-runbooken startar en Virtuell Azure-dator genom att hämta parametrarna för den virtuella datorn från JSON-objektet.

### <a name="create-the-json-file"></a>Skapa JSON-filen

Skriv följande kod i en textfil `test.json` och spara den som någonstans på den lokala datorn.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Skapa runbooken

Skapa en ny PowerShell-runbook med namnet **Test-Json** i Azure Automation. Se [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).

Om du vill acceptera JSON-data måste runbooken ta ett objekt som en indataparameter. Runbook kan sedan använda de egenskaper som definierats i JSON-filen.

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

Spara och publicera den här runbooken i ditt Automation-konto.

### <a name="call-the-runbook-from-powershell"></a>Anropa runbooken från PowerShell

Nu kan du anropa runbooken från din lokala dator med hjälp av Azure PowerShell. 

1. Logga in på Azure enligt bilden. Efteråt uppmanas du att ange dina Azure-autentiseringsuppgifter.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >För PowerShell-runbooks är **Add-AzAccount** och **Add-AzureRMAccount** alias för **Connect-AzAccount**. Observera att dessa alias inte är tillgängliga för grafiska runbooks. En grafisk runbook kan bara använda **Connect-AzAccount** själv.

1. Hämta innehållet i den sparade JSON-filen och konvertera den till en sträng. `JsonPath`är sökvägen där du sparade JSON-filen.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Konvertera stränginnehållet `$json` i till ett PowerShell-objekt.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Skapa en hashtable för parametrarna för **Start-AzAutomationRunbook**. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observera att du anger värdet *för parametrar* till PowerShell-objektet som innehåller värdena från JSON-filen.
1. Starta runbooken.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Nästa steg

* Mer information om olika sätt att starta en runbook finns i [Starta en runbook](automation-starting-a-runbook.md).
* Om du vill redigera en textkörningsbok läser du [Redigera textkörningar](automation-edit-textual-runbook.md).
* Information om hur du redigerar en grafisk runbook finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
