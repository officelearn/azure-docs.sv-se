---
title: Hantera certifikat i Azure Automation
description: Certifikat lagras säkert i Azure Automation så att Runbooks och DSC-konfigurationer kan komma åt dem för att autentisera mot Azure och resurser från tredje part. I den här artikeln förklaras information om certifikat och hur du arbetar med dem i både text-och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732828"
---
# <a name="manage-certificates-in-azure-automation"></a>Hantera certifikat i Azure Automation

Certifikat lagras på ett säkert sätt i Azure Automation så att de kan nås av Runbooks eller DSC-konfigurationer med hjälp av aktiviteten [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) för Azure Resource Manager-resurser. Med säker certifikat lagring kan du skapa Runbooks och DSC-konfigurationer som använder certifikat för autentisering eller lägga till dem i Azure eller från resurser från tredje part.

Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i systemhanterade Key Vault. Innan du lagrar en säker till gång läses nyckeln in från Key Vault och används sedan för att kryptera till gången. Den här processen hanteras av Azure Automation.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>AZ PowerShell-cmdletar

I AZ används cmdletarna i följande tabell för att skapa och hantera inloggnings resurser för Automation med Windows PowerShell. De levereras som en del av [modulen AZ. Automation](/powershell/azure/overview), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

|Cmdlet |Beskrivning|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Överför ett tjänst certifikat för den angivna moln tjänsten.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Hämtar information om ett certifikat som ska användas i en Runbook-eller DSC-konfiguration. Du kan bara hämta själva certifikatet med `Get-AutomationCertificate` aktiviteten.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Skapar ett nytt certifikat i Azure Automation.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Tar bort ett certifikat från Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Anger egenskaperna för ett befintligt certifikat, inklusive överföring av certifikat filen och inställning av lösen ordet för en **PFX** -fil.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt certifikat i en Runbook-och DSC-konfiguration.

| Aktiviteter | Beskrivning |
|:---|:---|
|`Get-AutomationCertificate`|Hämtar ett certifikat som ska användas i en Runbook-eller DSC-konfiguration. Returnerar ett [system. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -objekt.|

> [!NOTE] 
> Du bör undvika att använda variabler i `Name` -parametern `Get-AutomationCertificate` i en Runbook-eller DSC-konfiguration. Användning av variabler i den här parametern förlänger identifieringen av beroenden mellan Runbooks och DSC-konfigurationer och automation-variabler i design läge.

## <a name="python-2-functions"></a>Python 2-funktioner

Funktionen i följande tabell används för att komma åt certifikat i en python 2-Runbook.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_certificate` | Hämtar information om en certifikat till gång. |

> [!NOTE]
> Du måste importera `automationassets` modulen i början av din python-Runbook för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat laddar du upp en CER-eller PFX-fil för att Azure Automation. Om du markerar att certifikatet kan exporteras kan du överföra det från Azure Automation certifikat arkiv. Om den inte kan exporteras kan du bara använda den för signering i Runbook-eller DSC-konfigurationen. Azure Automation kräver att certifikatet har providern **Microsoft Enhanced RSA och AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Skapa ett nytt certifikat med Azure Portal

1. Från ditt Automation-konto klickar du på **till gångar** för att öppna sidan till gångar.
2. Välj **certifikat** för att öppna sidan certifikat.
3. Klicka på **Lägg till ett certifikat** överst på sidan.
4. Ange ett namn för certifikatet i fältet **namn** .
5. Om du vill bläddra efter en **CER** -eller **PFX** -fil klickar du på **Välj en fil** under **överför en certifikat fil**. Om du väljer en **. pfx**-fil anger du ett lösen ord och anger om det kan exporteras.
6. Klicka på **skapa** för att spara den nya certifikat till gången.

### <a name="create-a-new-certificate-with-powershell"></a>Skapa ett nytt certifikat med PowerShell

Följande exempel visar hur du skapar ett nytt Automation-certifikat och markerar det som kan exporteras. I det här exemplet importeras en befintlig **. pfx** -fil.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Skapa ett nytt certifikat med en Resource Manager-mall

Följande exempel visar hur du distribuerar ett certifikat till ditt Automation-konto med hjälp av en Resource Manager-mall via PowerShell:

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

Använd `Get-AutomationCertificate` aktiviteten om du vill använda ett certifikat. Du kan inte använda cmdleten [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) eftersom den returnerar information om certifikat till gången men inte själva certifikatet.

### <a name="textual-runbook-example"></a>Exempel på text Runbook

I följande exempel visas hur du lägger till ett certifikat i en moln tjänst i en Runbook. I det här exemplet hämtas lösen ordet från en krypterad Automation-variabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Exempel på grafisk Runbook

Lägg till `Get-AutomationCertificate` en aktivitet i en grafisk Runbook genom att högerklicka på certifikatet i fönstret Bibliotek och välja **Lägg till på arbets ytan**.

![Lägg till certifikat på arbets ytan](../media/certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk Runbook. Detta är samma som föregående exempel som visar hur du lägger till ett certifikat i en moln tjänst från en text-Runbook.

![Exempel på grafisk redigering](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Exempel på python 2

I följande exempel visas hur du får åtkomst till certifikat i Python2-Runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du arbetar med länkar för att kontrol lera det logiska flödet av aktiviteter som utförs av din Runbook finns i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow). 
