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
ms.openlocfilehash: c8b63a2676690004d23094b490fea0ef150ab9cb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546407"
---
# <a name="credential-assets-in-azure-automation"></a>Autentiseringsuppgiftstillgångar i Azure Automation

En resurs för automationsautentiseringstillstånd innehåller ett objekt som innehåller säkerhetsreferenser, till exempel ett användarnamn och ett lösenord. Runbooks- och DSC-konfigurationer använder cmdlets som accepterar ett [PSCredential-objekt](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) för autentisering. Alternativt kan de extrahera `PSCredential` objektets användarnamn och lösenord för att tillhandahålla något program eller någon tjänst som kräver autentisering. 

Azure Automation lagrar säkert egenskaperna för en autentiseringsinformation. Åtkomst till egenskaperna via en runbook- eller DSC-konfiguration använder aktiviteten [Get-AutomationPSCredential.](#activities-used-to-access-credentials)

> [!NOTE]
> Säkra resurser i Azure Automation innehåller autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i Key Vault. Innan du lagrar en säker tillgång läses nyckeln in från Key Vault och används sedan för att kryptera tillgången.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell Az-cmdlets som används för autentiseringsuppgifter

Som en del av Azure PowerShell Az-modulen används cmdlets i följande tabell för att skapa och hantera resurser för Automation-autentiseringsuppgifter med Windows PowerShell. De levereras i [Az.Automation-modulen](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer. Se [Stöd för Az-modul i Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Cmdlet | Beskrivning |
|:--- |:--- |
| [Få-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Hämtar information om en referenstillgång. Den här cmdleten `PSCredential` returnerar inte ett objekt.  |
| [Ny-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Skapar en ny automationsautentisering. |
| [Ta bort-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Tar bort en automatiseringsautentisering. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Anger egenskaperna för en befintlig Automation-autentiseringsuppgifter. |

## <a name="activities-used-to-access-credentials"></a>Aktiviteter som används för att komma åt autentiseringsuppgifter

Aktiviteterna i följande tabell används för att komma åt autentiseringsuppgifter i runbooks- och DSC-konfigurationer.

| Aktivitet | Beskrivning |
|:--- |:--- |
| `Get-AutomationPSCredential` |Hämtar en autentiseringsuppgifter som ska användas i en runbook- eller DSC-konfiguration. Autentiseringsuppgifterna är i form `PSCredential` av ett objekt. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Hämtar en autentiseringsfråga med en fråga om användarnamn och lösenord. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Skapar en referenstillgång. |

För lokal utveckling med hjälp av Azure `Get-AutomationPSCredential` Automation Authoring Toolkit är cmdlet en del av sammansättningen [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). För Azure som arbetar med automationskontexten finns cmdlet i `Orchestrator.AssetManagement.Cmdlets`. Se [Hantera moduler i Azure Automation](modules.md).

Om `PSCredential` du vill hämta objekt i koden kan du installera [Microsoft Azure Automation ISE-tillägget för PowerShell ISE](https://github.com/azureautomation/azure-automation-ise-addon).

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Skriptet kan också importera den modul som krävs där det behövs, som i följande exempel: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Du bör undvika att `Name` använda `Get-AutomationPSCredential`variabler i parametern . Deras användning kan försvåra identifiering av beroenden mellan runbooks eller DSC-konfigurationer och autentiseringsuppgifter tillgångar vid designtid.

## <a name="python2-functions-that-access-credentials"></a>Python2-funktioner som har åtkomst till autentiseringsuppgifter

Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en Python2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_credential` | Hämtar information om en referenstillgång. |

> [!NOTE]
> Importera `automationassets` modulen högst upp i Python-runbooken för att komma åt tillgångsfunktionerna.

## <a name="creating-a-new-credential-asset"></a>Skapa en ny referenstillgång

Du kan skapa en ny autentiseringstillgång med hjälp av Azure-portalen eller med Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Skapa en ny autentiseringstillgång med Azure-portalen

1. Välj **Autentiseringsuppgifter** under **Delade resurser**i ditt Automation-konto .
1. Välj **Lägg till en autentiseringstillstånd**.
2. Ange ett lämpligt autentiseringsuppgiftersnamn i fönstret Nytt autentiseringsuppgifter. 
3. Skriv ditt åtkomst-ID i fältet **Användarnamn.** 
4. För båda lösenordsfälten anger du din hemliga åtkomstnyckel.

    ![Skapa nya autentiseringsuppgifter](../media/credentials/credential-create.png)

5. Om multifaktorautentiseringsrutan är markerad avmarkerar du den. 
6. Klicka på **Skapa** om du vill spara den nya referenstillgången.

> [!NOTE]
> Azure Automation stöder inte användarkonton som använder multifaktorautentisering.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Skapa en ny autentiseringstillgång med Windows PowerShell

I följande exempel visas hur du skapar en ny automationsautentiseringstillgång. Ett `PSCredential` objekt skapas först med namnet och lösenordet och används sedan för att skapa referenstillgången. I stället kan `Get-Credential` du använda cmdleten för att uppmana användaren att skriva in ett namn och lösenord.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Använda en PowerShell-autentiseringstillstånd

En runbook- eller DSC-konfiguration hämtar en `Get-AutomationPSCredential` referenstillgång med aktiviteten. Den här aktiviteten `PSCredential` hämtar ett objekt som du kan använda med en aktivitet eller cmdlet som kräver en autentiseringsuppgifter. Du kan också hämta egenskaperna för det autentiseringsuppgifter som ska användas individuellt. Objektet har egenskaper för användarnamnet och det säkra lösenordet. Du kan också använda metoden [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) för att hämta ett [NetworkCredential-objekt](/dotnet/api/system.net.networkcredential) som representerar en osäker version av lösenordet.

> [!NOTE]
> `Get-AzAutomationCredential`hämtar inte `PSCredential` ett objekt som kan användas för autentisering. Den innehåller bara information om autentiseringsuppgifterna. Om du behöver använda en autentiseringsinformation i en runbook `PSCredential` måste `Get-AutomationPSCredential`du hämta den som ett objekt med .

### <a name="textual-runbook-example"></a>Exempel på textkörningsbok

I följande exempel visas hur du använder en PowerShell-autentiseringsuppgifter i en runbook. Den hämtar autentiseringsuppgifterna och tilldelar dess användarnamn och lösenord till variabler.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Du kan också använda en autentiseringsinformation för att autentisera till Azure med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Under de flesta omständigheter bör du använda ett [Run As-konto](../manage-runas-account.md) och hämta anslutningen till [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Exempel på grafisk runbook

Du kan `Get-AutomationPSCredential` lägga till en aktivitet i en grafisk runbook genom att högerklicka på autentiseringsaktiviteten i biblioteksfönstret i den grafiska redigeraren och välja **Lägg till på arbetsyta .**

![Lägga till autentiseringsuppgifter på arbetsytan](../media/credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder en autentiseringsuppgifter i en grafisk runbook. I det här fallet tillhandahåller autentiseringsuppgifterna autentiseringsuppgifter för en runbook till Azure-resurser, enligt beskrivningen i [Använd Azure AD i Azure Automation för att autentisera till Azure](../automation-use-azure-ad.md). Den första aktiviteten hämtar autentiseringsuppgifterna som har åtkomst till Azure-prenumerationen. Kontoanslutningsaktiviteten använder sedan den här autentiseringsaktiviteten för att tillhandahålla autentisering för alla aktiviteter som kommer efter den. En [pipeline-länk](../automation-graphical-authoring-intro.md#links-and-workflow) används `Get-AutomationPSCredential` här eftersom väntar ett enda objekt.  

![Lägga till autentiseringsuppgifter på arbetsytan](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Använda autentiseringsuppgifter i en DSC-konfiguration

Medan DSC-konfigurationer i Azure Automation kan arbeta `Get-AutomationPSCredential`med autentiseringsuppgifter tillgångar med , de kan också passera autentiseringsuppgifter tillgångar via parametrar. Mer information finns [i Kompilera konfigurationer i Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Använda autentiseringsuppgifter i Python2

I följande exempel visas ett exempel på åtkomst till autentiseringsuppgifter i Python2-runbooks.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om länkar i grafisk redigering finns [i Länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow).
* Information om hur du förstår de olika autentiseringsmetoderna för Automation finns i [Azure Automation Security](../automation-security-overview.md).
* Kom igång med grafiska runbooks finns i [Min första grafiska runbook](../automation-first-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell-arbetsflödeskörningsböcker finns i [Min första PowerShell-arbetsflödeskörningsbok](../automation-first-runbook-textual.md).
* Läs [My first Python2 runbook](../automation-first-runbook-textual-python2.md) (Min första Python2-runbook) för att komma igång med Python2-runbooks. 
