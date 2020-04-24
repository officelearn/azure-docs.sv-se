---
title: Hantera autentiseringsuppgifter i Azure Automation
description: Inloggnings till gångar i Azure Automation innehåller säkerhets referenser som kan användas för att autentisera till resurser som används av Runbook-eller DSC-konfigurationen. Den här artikeln beskriver hur du skapar inloggnings till gångar och använder dem i en Runbook-eller DSC-konfiguration.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 42abeba310e7a30364f93e998f12129a2d3c1f15
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114300"
---
# <a name="manage-credentials-in-azure-automation"></a>Hantera autentiseringsuppgifter i Azure Automation

En inloggnings till gång innehåller ett objekt som innehåller autentiseringsuppgifter, till exempel ett användar namn och ett lösen ord. Runbooks och DSC-konfigurationer använder cmdletar som accepterar ett [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) -objekt för autentisering. De kan också extrahera användar namn och lösen ord för `PSCredential` objektet för att tillhandahålla ett program eller en tjänst som kräver autentisering. 

> [!NOTE]
> Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i Key Vault. Innan du lagrar en säker till gång läses nyckeln in från Key Vault och används sedan för att kryptera till gången.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell AZ-cmdletar som används för inloggnings till gångar

Som en del av modulen Azure PowerShell AZ används cmdletarna i följande tabell för att skapa och hantera inloggnings resurser för Automation med Windows PowerShell. De levererar i [modulen AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer. Se [stöd för AZ-modulen i Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Beskrivning |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hämtar ett [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) -objekt som innehåller metadata om autentiseringsuppgifterna. Cmdlet: en hämtar inte `PSCredential` själva objektet.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Skapar en ny Automation-autentiseringsuppgift. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Tar bort en Automation-autentiseringsuppgift. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Anger egenskaperna för en befintlig Automation-autentiseringsuppgift. |

## <a name="activities-used-to-access-credentials"></a>Aktiviteter som används för att få åtkomst till autentiseringsuppgifter

Aktiviteterna i följande tabell används för att komma åt autentiseringsuppgifter i Runbooks och DSC-konfigurationer.

| Aktivitet | Beskrivning |
|:--- |:--- |
| `Get-AutomationPSCredential` |Hämtar ett `PSCredential` objekt som ska användas i en Runbook-eller DSC-konfiguration. Oftast bör du använda den här aktiviteten i stället för `Get-AzAutomationCredential` cmdleten, eftersom den senare bara hämtar autentiseringsinformation. Den här informationen är normalt inte användbar för att skicka till en annan cmdlet. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Hämtar autentiseringsuppgifter med en prompt för användar namn och lösen ord. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Skapar en inloggnings till gång. |

Om du `PSCredential` vill hämta objekt i din kod måste du importera `Orchestrator.AssetManagement.Cmdlets` modulen. Mer information finns i [Hantera moduler i Azure Automation](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Du bör undvika att använda variabler i `Name` -parametern `Get-AutomationPSCredential`i. Användningen kan komplicera identifieringen av beroenden mellan Runbooks och DSC-konfigurationer och inloggnings till gångar i design läge.

## <a name="python-2-functions-that-access-credentials"></a>Python 2-funktioner som har åtkomst till autentiseringsuppgifter

Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en python 2-Runbook.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_credential` | Hämtar information om en inloggnings till gång. |

> [!NOTE]
> Importera `automationassets` modulen överst i python-runbooken för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-credential-asset"></a>Skapa en ny inloggnings till gång

Du kan skapa en ny inloggnings till gång med hjälp av Azure Portal eller med hjälp av Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Skapa en ny inloggnings till gång med Azure Portal

1. Från ditt Automation-konto väljer du **autentiseringsuppgifter** under **delade resurser**.
1. Välj **Lägg till en autentiseringsuppgift**.
2. I fönstret ny autentiseringsuppgift anger du ett lämpligt namn på autentiseringsuppgiften enligt dina namngivnings standarder. 
3. Skriv ditt åtkomst-ID i fältet **användar namn** . 
4. I båda fälten för lösen ord anger du din hemliga åtkomst nyckel.

    ![Skapa ny autentiseringsuppgift](../media/credentials/credential-create.png)

5. Avmarkera kryss rutan Multi-Factor Authentication om du är markerad. 
6. Klicka på **skapa** för att spara den nya inloggnings till gången.

> [!NOTE]
> Azure Automation stöder inte användar konton som använder Multi-Factor Authentication.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Skapa en ny inloggnings till gång med Windows PowerShell

I följande exempel visas hur du skapar en ny inloggnings till gång för Automation. Ett `PSCredential` objekt skapas först med namnet och lösen ordet och används sedan för att skapa inloggnings till gången. I stället kan du använda `Get-Credential` cmdleten för att begära att användaren skriver in ett namn och ett lösen ord.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Använda en PowerShell-autentiseringsuppgift

En Runbook-eller DSC-konfiguration hämtar en inloggnings till `Get-AutomationPSCredential` gång med aktiviteten. Den här aktiviteten hämtar ett `PSCredential` objekt som du kan använda med en aktivitet eller cmdlet som kräver autentiseringsuppgifter. Du kan också hämta egenskaperna för objektet Credential som ska användas individuellt. Objektet har egenskaper för användar namnet och det säkra lösen ordet. Du kan också använda metoden [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) för att hämta ett [NetworkCredential](/dotnet/api/system.net.networkcredential) -objekt som representerar en oskyddad version av lösen ordet.

> [!NOTE]
> `Get-AzAutomationCredential`hämtar inte ett `PSCredential` objekt som kan användas för autentisering. Den ger bara information om autentiseringsuppgifterna. Om du behöver använda autentiseringsuppgifter i en Runbook måste du hämta det som ett `PSCredential` objekt med hjälp av. `Get-AutomationPSCredential`

### <a name="textual-runbook-example"></a>Exempel på text Runbook

I följande exempel visas hur du använder en PowerShell-autentiseringsuppgift i en Runbook. Den hämtar autentiseringsuppgiften och tilldelar dess användar namn och lösen ord till variabler.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Du kan också använda autentiseringsuppgifter för att autentisera till Azure med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). I de flesta fall bör du använda ett [Kör som-konto](../manage-runas-account.md) och hämta anslutningen med [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Exempel på grafisk Runbook

Du kan lägga till `Get-AutomationPSCredential` en aktivitet i en grafisk Runbook genom att högerklicka på autentiseringsuppgiften i fönstret Bibliotek i den grafiska redigeraren och välja **Lägg till på arbets ytan**.

![Lägg till autentiseringsuppgift till arbets ytan](../media/credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder en autentiseringsuppgift i en grafisk Runbook. I det här fallet ger autentiseringsuppgiften autentisering för en Runbook till Azure-resurser, enligt beskrivningen i [använda Azure AD i Azure Automation för att autentisera till Azure](../automation-use-azure-ad.md). Den första aktiviteten hämtar de autentiseringsuppgifter som har åtkomst till Azure-prenumerationen. Konto anslutnings aktiviteten använder sedan den här autentiseringsuppgiften för att tillhandahålla autentisering för alla aktiviteter som följer efter den. En [pipeline-länk](../automation-graphical-authoring-intro.md#links-and-workflow) används här eftersom `Get-AutomationPSCredential` det förväntar sig ett enskilt objekt.  

![Lägg till autentiseringsuppgift till arbets ytan](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Använda autentiseringsuppgifter i en DSC-konfiguration

Även om DSC-konfigurationer i Azure Automation kan arbeta med inloggnings till gångar med `Get-AutomationPSCredential`, kan de också skicka inloggnings resurser via parametrar. Mer information finns i [kompilera konfigurationer i Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Använda autentiseringsuppgifter i python 2

I följande exempel visas ett exempel på hur du kommer åt autentiseringsuppgifter i python 2-Runbooks.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om länkar i grafisk redigering finns i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow).
* För att förstå de olika autentiseringsmetoderna för Automation, se [Azure Automation säkerhet](../automation-security-overview.md).
* För att komma igång med grafiska runbooks, se [min första grafiska Runbook](../automation-first-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell Workflow-Runbooks finns i [min första PowerShell Workflow-Runbook](../automation-first-runbook-textual.md).
* För att komma igång med python 2-Runbooks, se [min första python 2-Runbook](../automation-first-runbook-textual-python2.md). 
