---
title: Hantera certifikat i Azure Automation
description: Certifikat lagras säkert i Azure Automation så att runbooks eller DSC-konfigurationer kan komma åt dem för att autentisera mot Azure och tredjepartsresurser. I den här artikeln beskrivs information om certifikat och hur du arbetar med dem i både text- och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732828"
---
# <a name="manage-certificates-in-azure-automation"></a>Hantera certifikat i Azure Automation

Certifikat lagras säkert i Azure Automation så att de kan nås av runbooks eller DSC-konfigurationer med hjälp av aktiviteten [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) för Azure Resource Manager-resurser. Med säker certifikatlagring kan du skapa runbooks- och DSC-konfigurationer som använder certifikat för autentisering eller lägga till dem i Azure eller resurser från tredje part.

Säkra resurser i Azure Automation innehåller autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje automationskonto. Den här nyckeln lagras i ett systemhanterade Key Vault. Innan du lagrar en säker tillgång läses nyckeln in från Key Vault och används sedan för att kryptera tillgången. Den här processen hanteras av Azure Automation.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>Az PowerShell-cmdlets

För Az används cmdlets i följande tabell för att skapa och hantera resurser för automatiseringsautentiseringsuppgifter med Windows PowerShell. De levereras som en del av [Az.Automation-modulen](/powershell/azure/overview), som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet |Beskrivning|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Laddar ett tjänstcertifikat för den angivna molntjänsten.|
|[Få-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Hämtar information om ett certifikat som ska användas i en runbook- eller DSC-konfiguration. Du kan bara hämta själva `Get-AutomationCertificate` certifikatet med hjälp av aktiviteten.|
|[Ny-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Skapar ett nytt certifikat i Azure Automation.|
|[Ta bort-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Tar bort ett certifikat från Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Anger egenskaperna för ett befintligt certifikat, inklusive att överföra certifikatfilen och ange lösenordet för en **PFX-fil.**|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt certifikat i en runbook- och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:---|:---|
|`Get-AutomationCertificate`|Hämtar ett certifikat som ska användas i en runbook- eller DSC-konfiguration. Returnerar ett [System.Security.Cryptography.X509Certificates.X509Certificate2-objekt.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Du bör undvika att `Name` använda `Get-AutomationCertificate` variabler i parametern i en runbook- eller DSC-konfiguration. Användning av variabler i den här parametern komplicerar identifieringen av beroenden mellan runbooks- eller DSC-konfigurationer och automatiseringsvariabler vid designtid.

## <a name="python-2-functions"></a>Python 2-funktioner

Funktionen i följande tabell används för att komma åt certifikat i en Python 2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_certificate` | Hämtar information om en certifikattillgång. |

> [!NOTE]
> Du måste `automationassets` importera modulen i början av python-runbooken för att komma åt tillgångsfunktionerna.

## <a name="creating-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat laddar du upp en PFX- eller PFX-fil till Azure Automation. Om du markerar certifikatet som exportbart kan du överföra det från Azure Automation-certifikatarkivet. Om den inte kan exporteras kan den bara användas för signering i runbook- eller DSC-konfigurationen. Azure Automation kräver att certifikatet har leverantören **Microsoft Enhanced RSA och AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Skapa ett nytt certifikat med Azure-portalen

1. Öppna sidan Tillgångar **från** ditt Automation-konto.
2. Välj **Certifikat för** att öppna sidan Certifikat.
3. Klicka på **Lägg till ett certifikat** högst upp på sidan.
4. Ange ett namn på certifikatet i fältet **Namn.**
5. Om du vill söka efter en **PF-fil** eller **PFX-fil** klickar du på **Välj en fil** under Ladda upp en **certifikatfil**. Om du väljer en **PFX-fil**anger du ett lösenord och anger om den kan exporteras.
6. Klicka på **Skapa** om du vill spara den nya certifikattillgången.

### <a name="create-a-new-certificate-with-powershell"></a>Skapa ett nytt certifikat med PowerShell

I följande exempel visas hur du skapar ett nytt Automation-certifikat och markerar det som kan exporteras. I det här exemplet importeras en befintlig **PFX-fil.**

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Skapa ett nytt certifikat med en Resource Manager-mall

I följande exempel visas hur du distribuerar ett certifikat till ditt Automation-konto med hjälp av en Resource Manager-mall via PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Använda ett certifikat

Använd aktiviteten om `Get-AutomationCertificate` du vill använda ett certifikat. Du kan inte använda cmdleten [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) eftersom den returnerar information om certifikattillgången men inte själva certifikatet.

### <a name="textual-runbook-example"></a>Exempel på textkörningsbok

I följande exempel visas hur du lägger till ett certifikat i en molntjänst i en runbook. I det här exemplet hämtas lösenordet från en krypterad automatiseringsvariabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Exempel på grafisk runbook

Lägg `Get-AutomationCertificate` till en aktivitet i en grafisk runbook genom att högerklicka på certifikatet i biblioteksfönstret och välja **Lägg till på arbetsyta .**

![Lägga till certifikat på arbetsytan](../media/certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk runbook. Det här är samma som föregående exempel som visar hur du lägger till ett certifikat i en molntjänst från en textkörningsbok.

![Exempel grafisk redigering](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 exempel

I följande exempel visas hur du kommer åt certifikat i Python2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du arbetar med länkar för att styra det logiska flödet av aktiviteter som utförs av runbooken finns [i Länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow). 
