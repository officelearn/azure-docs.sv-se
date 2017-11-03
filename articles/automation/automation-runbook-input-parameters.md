---
title: "Indataparametrar för Runbook | Microsoft Docs"
description: "Indataparametrarna för Runbook ökas flexibiliteten för runbooks genom att du kan skicka data till en runbook när den startas. Den här artikeln beskrivs olika scenarier där indataparametrar används i runbooks."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 4d3dff2c-1f55-498d-9a0e-eee497e5bedb
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sngun
ms.openlocfilehash: e5a2afdc0dbe6171b27c11400f460eac46147f37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="runbook-input-parameters"></a>Indataparametrar för Runbook

Indataparametrarna för Runbook ökas flexibiliteten för runbooks genom att du kan skicka data till den när den startas. Parametrarna gör runbook-åtgärder kan vara mål för specifika scenarier och miljöer. I den här artikeln går du igenom olika scenarier där indataparametrar används i runbooks.

## <a name="configure-input-parameters"></a>Konfigurera parametrar för indata

Indataparametrar kan konfigureras i PowerShell, PowerShell-arbetsflöde, Python och grafiska runbook-flöden. En runbook kan ha flera parametrar med olika datatyper eller inga parametrar alls. Ange parametrarna kan vara obligatoriska eller valfria, och du kan tilldela ett standardvärde för valfria parametrar. Du kan tilldela värden indataparametrar för en runbook när du startar det genom en av de tillgängliga metoderna. De här metoderna omfattar att starta en runbook från portalen eller en webbtjänst. Du kan också starta som en underordnad runbook som anropas infogad i en annan runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Konfigurera indataparametrar i runbooks med PowerShell och PowerShell-arbetsflöde

PowerShell och [PowerShell-arbetsflöde runbooks](automation-first-runbook-textual.md) stöder indataparametrar som definieras med följande attribut i Azure Automation.  

| **Egenskap** | **Beskrivning** |
|:--- |:--- |
| Typ |Krävs. Datatypen för värdet för parametern. .NET-typ är giltig. |
| Namn |Krävs. Parameterns namn. Detta måste vara unika inom en runbook och kan bara innehålla bokstäver, siffror eller understreck. Det måste börja med en bokstav. |
| Obligatorisk |Valfri. Anger om ett värde måste anges för parametern. Om du väljer **$true**, och sedan ett värde måste anges när runbooken startar. Om du väljer **$false**, och sedan ett värde är valfritt. |
| Standardvärde |Valfri.  Anger ett värde som ska användas för parametern som ett värde inte skickas när runbook startas. Ett standardvärde kan anges för alla parametrar och görs automatiskt parametern valfria oavsett inställningen obligatoriskt. |

Windows PowerShell stöder flera attribut för indataparametrarna än de som anges här, t.ex validering, alias, och parametern anger. Azure Automation stöder för närvarande endast indataparametrar som anges ovan.

En parameterdefinition i PowerShell-arbetsflöde runbooks har följande allmänna formuläret, där flera parametrar avgränsas med kommatecken.

   ```powershell
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
   ```

> [!NOTE]
> När du definierar parametrar, om du inte anger den **obligatoriska** attribut, och sedan som standard parametern anses vara valfri. Även om du anger ett standardvärde för en parameter i PowerShell-arbetsflöde runbooks behandlas av PowerShell som en valfri parameter, oberoende av den **obligatoriska** attributvärdet.
> 
> 

Exempelvis ska vi konfigurera indataparametrar för en PowerShell-arbetsflödesrunbook som matar ut information om virtuella datorer, en enda virtuell dator eller alla virtuella datorer inom en resursgrupp. Denna runbook innehåller två parametrar som visas i följande skärmbild: namnet på den virtuella datorn och namnet på resursgruppen.

![Automation PowerShell-arbetsflöde](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

I den här parameterdefinition, parametrarna **$VMName** och **$resourceGroupName** är enkla parametrar av typen string. PowerShell och PowerShell-arbetsflöde runbooks stöder dock alla enkla typer och komplexa typer som **objekt** eller **PSCredential** för indataparametrarna.

Om din runbook har ett objekt Indataparametern, använder du en PowerShell hash-tabell med (namn, värde) par att skicka in ett värde. Till exempel om du har följande parameter i en runbook:

     [Parameter (Mandatory = $true)]
     [object] $FullName

Sedan kan du skicka följande värde i parametern:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurera indataparametrar i grafiska runbook-flöden

Att [konfigurerar en grafisk runbook](automation-first-runbook-graphical.md) med indataparametrar, skapar vi en grafisk runbook som matar ut information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer inom en resursgrupp. Konfigurera en runbook består av två viktiga aktiviteter som beskrivs nedan.

[**Autentisera Runbooks med Kör som-kontot Azure** ](automation-sec-configure-azure-runas-account.md) att autentisera med Azure.

[**Get-AzureRmVm** ](https://msdn.microsoft.com/library/mt603718.aspx) att hämta egenskaperna för en virtuella datorer.

Du kan använda den [ **Write-Output** ](https://technet.microsoft.com/library/hh849921.aspx) aktiviteten till utdata namnen på virtuella datorer. Aktiviteten **Get-AzureRmVm** två parametrar i **virtuellt datornamn** och **resursgruppens namn**. Eftersom de här parametrarna kan kräver olika värden varje gång du startar en runbook kan du lägga till indataparametrar till din runbook. Här följer stegen för att lägga till indataparametrar:

1. Välj den grafiska runbooken från den **Runbooks** bladet och klicka sedan på [ **redigera** ](automation-graphical-authoring-intro.md) den.
2. Klicka på runbook-redigeraren **ingående och utgående** att öppna den **ingående och utgående** bladet.
   
    ![Grafisk Automation-runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. Den **ingående och utgående** bladet visar en lista över indataparametrar som definierats för runbooken. På det här bladet kan du lägga till en ny Indataparametern eller redigera konfigurationen av en befintlig indataparameter. Om du vill lägga till en ny parameter för runbook, klickar du på **lägga till indata** att öppna den **runbookinmatningsparameter** bladet. Där kan konfigurera du följande parametrar:
   
   | **Egenskap** | **Beskrivning** |
   |:--- |:--- |
   | Namn |Krävs.  Parameterns namn. Detta måste vara unika inom en runbook och kan bara innehålla bokstäver, siffror eller understreck. Det måste börja med en bokstav. |
   | Beskrivning |Valfri. Beskrivning om syftet med indataparameter. |
   | Typ |Valfri. Datatypen som förväntas för parametervärdet. Parametrar som stöds är **sträng**, **Int32**, **Int64**, **Decimal**, **booleskt**,  **DateTime**, och **objektet**. Om datatypen inte är markerat används som standard **sträng**. |
   | Obligatorisk |Valfri. Anger om ett värde måste anges för parametern. Om du väljer **Ja**, och sedan ett värde måste anges när runbooken startar. Om du väljer **inga**, och sedan ett värde inte är nödvändiga när runbook startas och du kan endast ange ett standardvärde. |
   | Standardvärde |Valfri. Anger ett värde som ska användas för parametern som ett värde inte skickas när runbook startas. Ett standardvärde kan anges för en parameter som inte är obligatoriskt. Om du vill ange ett standardvärde, Välj **anpassad**. Det här värdet används om inte ett annat värde anges när runbooken startas. Välj **ingen** om du inte vill ange någon standardvärdet. |
   
    ![Lägga till nya indata](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Skapa två parametrar med följande egenskaper kommer att användas av den **Get-AzureRmVm** aktiviteten:
   
   * **Parameter1:**
     
     * Namn - VMName
     * Typ - sträng
     * Obligatoriska - Nej
   * **Parameter2:**
     
     * Namn - resourceGroupName
     * Typ - sträng
     * Obligatoriska - Nej
     * Standardvärde - anpassad
     * Anpassade standardvärde - \<namnet på resursgruppen som innehåller de virtuella datorerna >
5. När du lägger till parametrarna, klickar du på **OK**.  Nu kan du visa dem i den **indata och utdata bladet**. Klicka på **OK** igen, och klicka sedan på **spara** och **publicera** din runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurera indataparametrar i Python runbooks

Till skillnad från PowerShell PowerShell-arbetsflöde och grafiska runbook-flöden börjar Python runbooks inte namngivna parametrar.
Alla indataparametrar tolkas som en matris med argumentvärden.
Du har åtkomst till matrisen genom att importera den `sys` modulen till din Python-skriptet och sedan använda den `sys.argv` matris.
Det är viktigt att notera att det första elementet i matrisen, `sys.argv[0]`, är namnet på skriptet, så är den första parametern för faktiska inkommande `sys.argv[1]`.

Ett exempel på hur du använder indataparametrar i en Python-runbook finns [min första Python-runbook i Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Tilldela värden till indataparametrar i runbooks

Du kan skicka värden för att ange parametrar i runbooks i följande scenarier.

### <a name="start-a-runbook-and-assign-parameters"></a>Starta en runbook och tilldela parametrar

En runbook kan startas på många olika sätt: genom Azure-portalen med en webhook, med PowerShell-cmdletar, med REST API eller med SDK. Nedan diskuterar vi olika metoder för att starta en runbook och tilldela parametrar.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Starta en publicerad runbook med hjälp av Azure portal och tilldela parametrar

När du [starta runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), **starta Runbook** blad öppnas och du kan ange värden för parametrarna som du nyss skapade.

![Börja använda portalen](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Du kan se de attribut som har angetts för parametern i etiketten under rutan indata. Attribut är obligatoriska eller valfria, typ och standardvärdet. Du kan se alla viktig information som du behöver fatta beslut om parametern indatavärden i Hjälp-pratbubblor bredvid namnet på parametern. Informationen omfattar om en parameter är obligatoriska eller valfria. Även typ och standardvärdet (eventuella) och annan användbar information.

![Hjälp pratbubblor](media/automation-runbook-input-parameters/automation-05-helpbaloon.png)

> [!NOTE]
> Stöd för parametrar av typen String **tom** String värden.  Ange **[EmptyString]** i Indataparametern kommer rutan att skicka en tom sträng i parametern. Dessutom stöder inte typen strängparametrar **Null** värden som skickas. Om du inte skickar ett värde till parametern sträng, sedan tolkas PowerShell det som null.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Starta en publicerad runbook med hjälp av PowerShell-cmdlets och tilldela parametrar

* **Azure Resource Manager-cmdlets:** du kan starta en Automation-runbook som har skapats i en resursgrupp med hjälp av [Start AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Exempel:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Azure Service Management-cmdlets:** du kan starta en automation-runbook som har skapats i en standard-resursgrupp med hjälp av [Start AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Exempel:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> När du startar en runbook med hjälp av PowerShell-cmdletar, en standardparameter **MicrosoftApplicationManagementStartedBy** skapas med värdet **PowerShell**. Du kan visa den här parametern i den **Jobbdetaljer** bladet.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Starta en runbook med hjälp av ett SDK och tilldela parametrar

* **Azure Resource Manager-metod:** du kan starta en runbook med hjälp av SDK för ett programmeringsspråk. Nedan visas ett C# kodfragment för att starta en runbook i Automation-konto. Du kan visa all kod på vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```
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
* **Azure Service Management-metod:** du kan starta en runbook med hjälp av SDK för ett programmeringsspråk. Nedan visas ett C# kodfragment för att starta en runbook i Automation-konto. Du kan visa all kod på vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```      
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
  
  Skapa en ordlista för att lagra runbook-parametrar för att starta den här metoden **VMName** och **resourceGroupName**, och deras värden. Starta runbook. Nedan visas kodfragmentet C# för att anropa metoden som har definierats ovan.
  
  ```
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Starta en runbook med hjälp av REST-API och tilldela parametrar
Ett runbook-jobb kan skapas och igång med Azure Automation REST-API med hjälp av den **PLACERA** metod med följande URI-begäran.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

I URI-begäran, ersätter du följande parametrar:

* **prenumerations-id:** ditt Azure-prenumeration-ID.  
* **molntjänstnamnet-:** namnet på molnet tjänsten som begäran ska skickas.  
* **Automation-kontonamn:** namnet på ditt automation-konto som ligger inom den angivna Molntjänsten.  
* **jobb-id:** GUID för jobbet. GUID i PowerShell kan skapas med hjälp av den **[GUID]::NewGuid(). ToString()** kommando.

Använd begärandetexten för att skicka parametrar till runbook-jobbet. Det tar följande två egenskaper i JSON-format:

* **Runbook-namn:** krävs. Namnet på runbook för att jobbet ska starta.  
* **Runbook-parametrar:** valfritt. En ordlista med parameterlistan i (namn, värde) format där namnet ska vara av typen sträng och värdet kan vara ett giltigt JSON-värde.

Om du vill starta den **Get-AzureVMTextual** runbook som har skapats tidigare med **VMName** och **resourceGroupName** som parametrar, använda följande JSON-format för begärandetexten.

   ```
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

En HTTP-statuskod 201 returneras om jobbet har skapats. Mer information om svarshuvuden och svarstexten finns i artikel om hur du [skapa ett runbook-jobb med hjälp av REST API.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Testa en runbook och tilldela parametrar
När du [testa utkastversionen för din runbook](automation-testing-runbook.md) med hjälp av alternativet testa den **testa** blad öppnas och du kan konfigurera värden för parametrarna som du nyss skapade.

![Testa och tilldela parametrar](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Länka ett schema till en runbook och tilldela parametrar
Du kan [länka ett schema](automation-schedules.md) till din runbook så att runbook startar vid en viss tid. Du kan tilldela indataparametrar när du skapar schemat och runbook kommer att använda dessa värden när den har startats med schemat. Du kan inte spara schemat förrän alla obligatoriska parametervärden har angetts.

![Schemalägga och tilldela parametrar](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Skapa en webhook för en runbook och tilldela parametrar
Du kan skapa en [webhook](automation-webhooks.md) för din runbook och konfigurera indataparametrarna för runbook. Du kan inte spara webhooken förrän alla obligatoriska parametervärden har angetts.

![Skapa webhook och tilldela parametrar](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

När du kör en runbook med hjälp av en webhook fördefinierade Indataparametern  **[Webhookdata](automation-webhooks.md#details-of-a-webhook)**  skickas tillsammans med indataparametrar som du har definierat. Du kan klicka på för att expandera den **WebhookData** parameter för mer information.

![WebhookData-parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Nästa steg
* Mer information om runbook ingående och utgående finns [Azure Automation: runbook indata, utdata och kapslade runbooks](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
* Mer information om olika sätt att starta en runbook finns [starta en runbook](automation-starting-a-runbook.md).
* Om du vill redigera en textrepresentation runbook, referera till [redigera textrepresentation runbooks](automation-edit-textual-runbook.md).
* Om du vill redigera en grafisk runbook avser [grafiska redigering i Azure Automation](automation-graphical-authoring-intro.md).

