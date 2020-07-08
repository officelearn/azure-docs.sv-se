---
title: Hantera autentiseringsuppgifter i Azure Automation
description: Den här artikeln beskriver hur du skapar inloggnings till gångar och använder dem i en Runbook-eller DSC-konfiguration.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 540ae25b22b2c134a47f91ad5b8b19089c7f2acb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83745000"
---
# <a name="manage-credentials-in-azure-automation"></a>Hantera autentiseringsuppgifter i Azure Automation

En inloggnings till gång innehåller ett objekt som innehåller autentiseringsuppgifter, till exempel ett användar namn och ett lösen ord. Runbooks och DSC-konfigurationer använder cmdletar som accepterar ett [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) -objekt för autentisering. De kan också extrahera användar namn och lösen ord för `PSCredential` objektet för att tillhandahålla ett program eller en tjänst som kräver autentisering. 

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Azure Automation lagrar nyckeln i systemhanterade Key Vault. Innan du lagrar en säker till gång läser Automation in nyckeln från Key Vault och använder den för att kryptera till gången. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>PowerShell-cmdletar som används för att få åtkomst till autentiseringsuppgifter

Cmdletarna i följande tabell skapar och hanterar autentiseringsuppgifter för Automation med PowerShell. De levereras som en del av [AZ-modulerna](modules.md#az-modules).

| Cmdlet | Beskrivning |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hämtar ett [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) -objekt som innehåller metadata om autentiseringsuppgifterna. Cmdlet: en hämtar inte `PSCredential` själva objektet.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Skapar en ny Automation-autentiseringsuppgift. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Tar bort en Automation-autentiseringsuppgift. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Anger egenskaperna för en befintlig Automation-autentiseringsuppgift. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Andra cmdletar som används för att få åtkomst till autentiseringsuppgifter

Cmdletarna i följande tabell används för att komma åt autentiseringsuppgifter i dina runbooks och DSC-konfigurationer. 

| Cmdlet | Beskrivning |
|:--- |:--- |
| `Get-AutomationPSCredential` |Hämtar ett `PSCredential` objekt som ska användas i en Runbook-eller DSC-konfiguration. Oftast bör du använda den här [interna cmdleten](modules.md#internal-cmdlets) i stället för `Get-AzAutomationCredential` cmdleten, eftersom den senare bara hämtar autentiseringsinformation. Den här informationen är normalt inte användbar för att skicka till en annan cmdlet. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Hämtar autentiseringsuppgifter med en prompt för användar namn och lösen ord. Denna cmdlet är en del av standard modulen Microsoft. PowerShell. Security. Se [standardmoduler](modules.md#default-modules).|
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Skapar en inloggnings till gång. Denna cmdlet är en del av Azures standard-modul. Se [standardmoduler](modules.md#default-modules).|

Om du vill hämta `PSCredential` objekt i din kod måste du importera `Orchestrator.AssetManagement.Cmdlets` modulen. Mer information finns i [Hantera moduler i Azure Automation](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Du bör undvika att använda variabler i- `Name` parametern i `Get-AutomationPSCredential` . Användningen kan komplicera identifieringen av beroenden mellan Runbooks och DSC-konfigurationer och inloggnings till gångar i design läge.

## <a name="python-2-functions-that-access-credentials"></a>Python 2-funktioner som har åtkomst till autentiseringsuppgifter

Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en python 2-Runbook.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_credential` | Hämtar information om en inloggnings till gång. |

> [!NOTE]
> Importera `automationassets` modulen överst i python-runbooken för att få åtkomst till till gångs funktionerna.

## <a name="create-a-new-credential-asset"></a>Skapa en ny inloggnings till gång

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

## <a name="get-a-credential-asset"></a>Hämta en inloggnings till gång

En Runbook-eller DSC-konfiguration hämtar en inloggnings till gång med den interna `Get-AutomationPSCredential` cmdleten. Denna cmdlet hämtar ett `PSCredential` objekt som du kan använda med en cmdlet som kräver en autentiseringsuppgift. Du kan också hämta egenskaperna för objektet Credential som ska användas individuellt. Objektet har egenskaper för användar namnet och det säkra lösen ordet. 

> [!NOTE]
> `Get-AzAutomationCredential`Cmdleten hämtar inte ett `PSCredential` objekt som kan användas för autentisering. Den ger bara information om autentiseringsuppgifterna. Om du behöver använda autentiseringsuppgifter i en Runbook måste du hämta det som ett `PSCredential` objekt med hjälp av `Get-AutomationPSCredential` .

Du kan också använda metoden [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) för att hämta ett [NetworkCredential](/dotnet/api/system.net.networkcredential) -objekt som representerar en oskyddad version av lösen ordet.

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

Du kan lägga till en aktivitet för den interna `Get-AutomationPSCredential` cmdleten i en grafisk Runbook genom att högerklicka på autentiseringsuppgiften i fönstret Bibliotek i den grafiska redigeraren och välja **Lägg till på arbets ytan**.

![Lägg till autentiseringsuppgift till arbets ytan](../media/credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder en autentiseringsuppgift i en grafisk Runbook. I det här fallet ger autentiseringsuppgiften autentisering för en Runbook till Azure-resurser, enligt beskrivningen i [använda Azure AD i Azure Automation för att autentisera till Azure](../automation-use-azure-ad.md). Den första aktiviteten hämtar de autentiseringsuppgifter som har åtkomst till Azure-prenumerationen. Konto anslutnings aktiviteten använder sedan den här autentiseringsuppgiften för att tillhandahålla autentisering för alla aktiviteter som följer efter den. En [pipeline-länk](../automation-graphical-authoring-intro.md#use-links-for-workflow) används här eftersom `Get-AutomationPSCredential` det förväntar sig ett enskilt objekt.  

![Lägg till autentiseringsuppgift till arbets ytan](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Använda autentiseringsuppgifter i en DSC-konfiguration

Även om DSC-konfigurationer i Azure Automation kan arbeta med inloggnings till gångar med `Get-AutomationPSCredential` , kan de också skicka inloggnings resurser via parametrar. Mer information finns i [kompilera konfigurationer i Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="use-credentials-in-a-python-2-runbook"></a>Använd autentiseringsuppgifter i en python 2-Runbook

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

* Mer information om de cmdletar som används för att komma åt certifikat finns [i hantera moduler i Azure Automation](modules.md).
* Allmän information om Runbooks finns [i Runbook-körning i Azure Automation](../automation-runbook-execution.md).
* Mer information om DSC-konfigurationer finns i [Översikt över Azure Automation tillstånds konfiguration](../automation-dsc-overview.md). 
