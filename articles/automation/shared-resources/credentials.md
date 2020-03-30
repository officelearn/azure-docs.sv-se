---
title: Autentiseringsuppgiftstillgångar i Azure Automation
description: Autentiseringsuppgifter tillgångar i Azure Automation innehåller säkerhetsreferenser som kan användas för att autentisera till resurser som nås av runbook eller DSC konfiguration. I den här artikeln beskrivs hur du skapar autentiseringsuppgifter och använder dem i en runbook- eller DSC-konfiguration.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252706"
---
# <a name="credential-assets-in-azure-automation"></a>Autentiseringsuppgiftstillgångar i Azure Automation

En resurs för automationsautentiseringsããären innehÃ/t.a innehÃ/r Runbooks- och DSC-konfigurationer kan använda cmdlets som accepterar ett PSCredential-objekt för autentisering, eller så kan de extrahera användarnamn och lösenord för PSCredential-objektet för att tillhandahålla vissa program eller tjänster som kräver autentisering. Egenskaperna för en autentiseringsuppgifter lagras säkert i Azure Automation och kan nås i runbook- eller DSC-konfigurationen med aktiviteten [Get-AutomationPSCredential.](#activities)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Säkra resurser i Azure Automation innehåller autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje automationskonto. Den här nyckeln lagras i Key Vault. Innan du lagrar en säker tillgång läses nyckeln in från Key Vault och används sedan för att kryptera tillgången.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell Az-cmdlets

För Azure PowerShell Az-modulen används cmdlets i följande tabell för att skapa och hantera resurser för automatiseringsautentiseringsuppgifter med Windows PowerShell. De levereras som en del av [Modulen AzureAz.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

| Cmdletar | Beskrivning |
|:--- |:--- |
| [Få-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hämtar information om en referenstillgång. Detta returnerar inte ett PSCredential-objekt.  |
| [Ny-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Skapar en ny automationsautentisering. |
| [Ta bort-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Tar bort en automatiseringsautentisering. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Anger egenskaperna för en befintlig Automation-autentiseringsuppgifter. |

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt autentiseringsuppgifter i en runbook- och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:--- |:--- |
| Get-AutomationPSCredential |Hämtar en autentiseringsuppgifter som ska användas i en runbook- eller DSC-konfiguration. Returnerar ett [System.Management.Automation.PSCredential-objekt.](/dotnet/api/system.management.automation.pscredential) |

> [!NOTE]
> Du bör undvika att använda variabler i parametern -Name i Get-AutomationPSCredential eftersom detta kan komplicera identifieringsberoenden mellan runbooks- eller DSC-konfigurationer och autentiseringsuppgifter tillgångar vid designtillfället.

## <a name="python2-functions"></a>Python2-funktioner

Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en Python2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_credential | Hämtar information om en referenstillgång. |

> [!NOTE]
> Du måste importera modulen "automationassets" högst upp i python-runbooken för att komma åt tillgångsfunktionerna.

## <a name="creating-a-new-credential-asset"></a>Skapa en ny referenstillgång

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Så här skapar du en ny autentiseringstillgång med Azure-portalen

1. Välj **Autentiseringsuppgifter** under **Delade resurser**i ditt automatiseringskonto .
1. Välj **Lägg till en autentiseringstillstånd**.
1. Fyll i formuläret och välj **Skapa** för att spara den nya autentiseringsformuläret.

> [!NOTE]
> Användarkonton som använder multifaktorautentisering stöds inte för användning i Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Så här skapar du en ny autentiseringstillgång med Windows PowerShell

Följande exempelkommandon visar hur du skapar en ny automatiseringsautentisering. Ett PSCredential-objekt skapas först med namnet och lösenordet och används sedan för att skapa referenstillgången. Du kan också använda cmdleten **Get-Autentiseringsuppgifter** för att ange ett namn och lösenord.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Använda en PowerShell-autentiseringstillstånd

Du hämtar en autentiseringstillgång i en runbook- eller DSC-konfiguration med aktiviteten **Get-AutomationPSCredential.** Detta returnerar ett [PSCredential-objekt](/dotnet/api/system.management.automation.pscredential) som du kan använda med en aktivitet eller cmdlet som kräver en PSCredential-parameter. Du kan också hämta egenskaperna för det autentiseringsuppgifter som ska användas individuellt. Objektet har en egenskap för användarnamnet och det säkra lösenordet, eller så kan du använda metoden **GetNetworkCredential** för att returnera ett [NetworkCredential-objekt](/dotnet/api/system.net.networkcredential) som tillhandahåller en osäker version av lösenordet.

> [!NOTE]
> **Get-AzAutomationCredential** returnerar inte en **PSCredential** som kan användas för autentisering. Den innehåller bara information om autentiseringsuppgifterna. Om du behöver använda en autentiseringsinformation i en runbook måste du använda **Get-AutomationPSCredential** för att hämta **PSCredential-objektet.**

### <a name="textual-runbook-sample"></a>Exempel på textkörningsbok

Följande exempelkommandon visar hur du använder en PowerShell-autentiseringsuppgift i en runbook. I det här exemplet hämtas autentiseringsuppgifterna och användarnamnet och lösenordet tilldelas variabler.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Du kan också använda en autentiseringsinformation för att autentisera till Azure med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Under de flesta omständigheter bör du använda ett [Run As-konto](../manage-runas-account.md) och hämta det med [Get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Exempel på grafisk runbook

Du lägger till en **Get-AutomationPSCredential-aktivitet i** en grafisk runbook genom att högerklicka på autentiseringsuppgifterna i biblioteksfönstret i den grafiska redigeraren och välja **Lägg till på arbetsyta**.

![Lägga till autentiseringsuppgifter på arbetsytan](../media/credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder en autentiseringsuppgifter i en grafisk runbook. I det här fallet används den för att tillhandahålla autentisering för en runbook till Azure-resurser enligt beskrivningen i [Authenticate Runbooks med Azure AD-användarkonto](../automation-create-aduser-account.md). Den första aktiviteten hämtar autentiseringsuppgifterna som har åtkomst till Azure-prenumerationen. **Aktiviteten Connect-AzureRmAccount** använder sedan den här autentiseringsaktiviteten för att tillhandahålla autentisering för alla aktiviteter som kommer efter den. En [pipeline länk](../automation-graphical-authoring-intro.md#links-and-workflow) är här eftersom **Get-AutomationPSCredential** förväntar sig ett enda objekt.  

![Lägga till autentiseringsuppgifter på arbetsytan](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Använda en PowerShell-autentiseringskort i DSC

Medan DSC-konfigurationer i Azure Automation kan referera till autentiseringsresurser med **Get-AutomationPSCredential,** kan autentiseringsuppgifter tillgångar också skickas in via parametrar, om så önskas. Mer information finns [i Kompilera konfigurationer i Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Använda autentiseringsuppgifter i Python2

Följande exempel visar ett exempel på åtkomst till autentiseringsuppgifter i Python2-runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om länkar i grafisk redigering finns [i Länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow)
* Information om hur du förstår de olika autentiseringsmetoderna med Automation finns i [Azure Automation Security](../automation-security-overview.md)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](../automation-first-runbook-graphical.md)
* Information om hur du kommer igång med PowerShell-arbetsflödeskörningsböcker läser du [Min första PowerShell-arbetsflödeskörningsbok](../automation-first-runbook-textual.md)
* För att komma igång med Python2 runbooks, se [Min första Python2 runbook](../automation-first-runbook-textual-python2.md) 
