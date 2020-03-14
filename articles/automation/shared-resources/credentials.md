---
title: Inloggnings till gångar i Azure Automation
description: Inloggnings till gångar i Azure Automation innehåller säkerhets referenser som kan användas för att autentisera till resurser som används av Runbook-eller DSC-konfigurationen. Den här artikeln beskriver hur du skapar inloggnings till gångar och använder dem i en Runbook-eller DSC-konfiguration.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252706"
---
# <a name="credential-assets-in-azure-automation"></a>Inloggnings till gångar i Azure Automation

En Automation Credential-till gång innehåller ett-objekt som innehåller autentiseringsuppgifter som användar namn och lösen ord. Runbooks och DSC-konfigurationer kan använda cmdletar som accepterar ett PSCredential-objekt för autentisering, eller så kan de extrahera användar namn och lösen ord för PSCredential-objektet för att tillhandahålla ett program eller en tjänst som kräver autentisering. Egenskaperna för en autentiseringsuppgift lagras på ett säkert sätt i Azure Automation och kan nås i Runbook-eller DSC-konfigurationen med aktiviteten [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i Key Vault. Innan du lagrar en säker till gång läses nyckeln in från Key Vault och används sedan för att kryptera till gången.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell AZ-cmdletar

För Azure PowerShell AZ-modulen används cmdletarna i följande tabell för att skapa och hantera inloggnings resurser för Automation med Windows PowerShell. De levereras som en del av [modulen AzureAz. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

| Cmdletar | Beskrivning |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hämtar information om en inloggnings till gång. Detta returnerar inget PSCredential-objekt.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Skapar en ny Automation-autentiseringsuppgift. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Tar bort en Automation-autentiseringsuppgift. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Anger egenskaperna för en befintlig Automation-autentiseringsuppgift. |

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt autentiseringsuppgifter i en Runbook-och DSC-konfiguration.

| Aktiviteter | Beskrivning |
|:--- |:--- |
| Get-AutomationPSCredential |Hämtar autentiseringsuppgifter som ska användas i en Runbook-eller DSC-konfiguration. Returnerar ett [system. Management. Automation. PSCredential](/dotnet/api/system.management.automation.pscredential) -objekt. |

> [!NOTE]
> Du bör undvika att använda variabler i parametern – name för Get-AutomationPSCredential eftersom detta kan komplicera identifieringen av beroenden mellan Runbooks och DSC-konfigurationer, samt behörighets till gångar i design läge.

## <a name="python2-functions"></a>Python2-funktioner

Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en Python2-Runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_credential | Hämtar information om en inloggnings till gång. |

> [!NOTE]
> Du måste importera modulen "automationassets" längst upp i din python-Runbook för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-credential-asset"></a>Skapa en ny inloggnings till gång

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Så här skapar du en ny inloggnings till gång med Azure Portal

1. Från ditt Automation-konto väljer du **autentiseringsuppgifter** under **delade resurser**.
1. Välj **Lägg till en autentiseringsuppgift**.
1. Fyll i formuläret och välj **skapa** för att spara den nya autentiseringsuppgiften.

> [!NOTE]
> Användar konton som använder Multi-Factor Authentication stöds inte för användning i Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Så här skapar du en ny inloggnings till gång med Windows PowerShell

Följande exempel kommandon visar hur du skapar en ny Automation-autentiseringsuppgift. Ett PSCredential-objekt skapas först med namnet och lösen ordet och används sedan för att skapa inloggnings till gången. Alternativt kan du använda cmdleten **Get-Credential** för att uppmanas att ange ett namn och lösen ord.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Använda en PowerShell-autentiseringsuppgift

Du hämtar en inloggnings till gång i en Runbook-eller DSC-konfiguration med aktiviteten **Get-AutomationPSCredential** . Detta returnerar ett [PSCredential-objekt](/dotnet/api/system.management.automation.pscredential) som du kan använda med en aktivitet eller en cmdlet som kräver en PSCredential-parameter. Du kan också hämta egenskaperna för objektet Credential som ska användas individuellt. Objektet har en egenskap för användar namnet och det säkra lösen ordet, eller så kan du använda metoden **GetNetworkCredential** för att returnera ett [NetworkCredential](/dotnet/api/system.net.networkcredential) -objekt som ger en oskyddad version av lösen ordet.

> [!NOTE]
> **Get-AzAutomationCredential** returnerar inte en **PSCredential** som kan användas för autentisering. Den ger bara information om autentiseringsuppgifterna. Om du behöver använda en autentiseringsuppgift i en Runbook måste du använda kommandot **Get-AutomationPSCredential** för att hämta **PSCredential** -objektet.

### <a name="textual-runbook-sample"></a>Text Runbook-exempel

Följande exempelkommandon visar hur du använder en PowerShell-autentiseringsuppgift i en runbook. I det här exemplet hämtas autentiseringsuppgiften och dess användar namn och lösen ord kopplade till variabler.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Du kan också använda autentiseringsuppgifter för att autentisera till Azure med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). I de flesta fall bör du använda ett [Kör som-konto](../manage-runas-account.md) och hämta det med [Get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Grafiskt Runbook-exempel

Du lägger till en **Get-AutomationPSCredential** -aktivitet i en grafisk Runbook genom att högerklicka på autentiseringsuppgiften i fönstret Bibliotek i den grafiska redigeraren och välja **Lägg till på arbets ytan**.

![Lägg till autentiseringsuppgift till arbets ytan](../media/credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder en autentiseringsuppgift i en grafisk Runbook. I det här fallet används den för att tillhandahålla autentisering för en Runbook till Azure-resurser enligt beskrivningen i [autentisera Runbooks med Azure AD-användarkonto](../automation-create-aduser-account.md). Den första aktiviteten hämtar de autentiseringsuppgifter som har åtkomst till Azure-prenumerationen. **Connect-AzureRmAccount** -aktiviteten använder sedan den här autentiseringsuppgiften för att tillhandahålla autentisering för alla aktiviteter som följer efter den. En [pipeline-länk](../automation-graphical-authoring-intro.md#links-and-workflow) är här eftersom **Get-AutomationPSCredential** förväntar sig ett enskilt objekt.  

![Lägg till autentiseringsuppgift till arbets ytan](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Använda en PowerShell-autentiseringsuppgift i DSC

Även om DSC-konfigurationer i Azure Automation kan referera till till gångar som använder **Get-AutomationPSCredential**, kan behörighets till gångar också skickas via parametrar, om det önskas. Mer information finns i [kompilera konfigurationer i Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Använda autentiseringsuppgifter i Python2

I följande exempel visas ett exempel på hur du kommer åt autentiseringsuppgifter i Python2-Runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om länkar i grafisk redigering, se [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow)
* Information om olika autentiseringsmetoder med Automation finns i [Azure Automation säkerhet](../automation-security-overview.md)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](../automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](../automation-first-runbook-textual.md)
* För att komma igång med Python2-Runbooks, se [min första Python2-Runbook](../automation-first-runbook-textual-python2.md) 
