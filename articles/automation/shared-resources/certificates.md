---
title: Certifikat till gångar i Azure Automation
description: Certifikaten är säkert i Azure Automation så att de kan nås av Runbooks eller DSC-konfigurationer för att autentisera mot Azure och resurser från tredje part.  I den här artikeln förklaras information om certifikat och hur du arbetar med dem i både text-och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849487"
---
# <a name="certificate-assets-in-azure-automation"></a>Certifikat till gångar i Azure Automation

Certifikat lagras på ett säkert sätt i Azure Automation så att de kan nås av Runbooks eller DSC-konfigurationer med hjälp av aktiviteten **Get-AzureRmAutomationCertificate** för Azure Resource Manager-resurser. Med den här funktionen kan du skapa Runbooks och DSC-konfigurationer som använder certifikat för autentisering eller lägga till dem i Azure eller från resurser från tredje part.

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i ett systemhanterat Key Vault. Innan du lagrar en säker till gång läses nyckeln in från Key Vault och används sedan för att kryptera till gången. Den här processen hanteras av Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdletar

I AzureRM används cmdletarna i följande tabell för att skapa och hantera inloggnings resurser för Automation med Windows PowerShell. De levereras som en del av [modulen AzureRM. Automation](/powershell/azure/overview), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

|Cmdlet: ar|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Hämtar information om ett certifikat som ska användas i en Runbook-eller DSC-konfiguration. Du kan bara hämta själva certifikatet från get-AutomationCertificate-aktiviteten.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Skapar ett nytt certifikat i Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Tar bort ett certifikat från Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Anger egenskaperna för ett befintligt certifikat, inklusive överföring av certifikat filen och lösen ordet för en PFX-fil.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Överför ett tjänst certifikat för den angivna moln tjänsten.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt certifikat i en Runbook-och DSC-konfiguration.

| Aktiviteter | Beskrivning |
|:---|:---|
|Get-AutomationCertificate|Hämtar ett certifikat som ska användas i en Runbook-eller DSC-konfiguration. Returnerar ett [system. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -objekt.|

> [!NOTE] 
> Du bör undvika att använda variabler i parametern – name för **Get-AutomationCertificate** i en Runbook-eller DSC-konfiguration eftersom den försvårar identifieringen av beroenden mellan RUNBOOKS eller DSC-konfiguration, samt automatiserade variabler vid design tillfället.

## <a name="python2-functions"></a>Python2-funktioner

Funktionen i följande tabell används för att komma åt certifikat i en Python2-Runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_certificate | Hämtar information om en certifikat till gång. |

> [!NOTE]
> Du måste importera **automationassets** -modulen i början av din python-Runbook för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat laddar du upp en CER-eller PFX-fil för att Azure Automation. Om du markerar att certifikatet kan exporteras kan du överföra det från Azure Automation certifikat arkiv. Om den inte kan exporteras kan du bara använda den för signering i Runbook-eller DSC-konfigurationen. Azure Automation kräver att certifikatet har följande provider: **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Så här skapar du ett nytt certifikat med Azure Portal

1. Från ditt Automation-konto klickar du på panelen **till gångar** för att öppna sidan **till gångar** .
2. Klicka på panelen **certifikat** för att öppna sidan **certifikat** .
3. Klicka på **Lägg till ett certifikat** överst på sidan.
4. Skriv ett namn för certifikatet i den **namn** box.
5. Om du vill bläddra efter en CER-eller PFX-fil klickar du på **Välj en fil** under **överför en certifikat fil**. Om du väljer en. pfx-fil anger du ett lösen ord och om det kan exporteras.
6. Klicka på **skapa** för att spara den nya certifikat till gången.

### <a name="to-create-a-new-certificate-with-powershell"></a>Så här skapar du ett nytt certifikat med PowerShell

Följande exempel visar hur du skapar ett nytt Automation-certifikat och markerar det som kan exporteras. Detta importerar en befintlig. pfx-fil.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Skapa ett nytt certifikat med Resource Manager-mall

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Använda ett certifikat

Använd aktiviteten **Get-AutomationCertificate** om du vill använda ett certifikat. Du kan inte använda cmdleten [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) eftersom den returnerar information om certifikat till gången men inte själva certifikatet.

### <a name="textual-runbook-sample"></a>Text Runbook-exempel

Följande exempel kod visar hur du lägger till ett certifikat i en moln tjänst i en Runbook. I det här exemplet hämtas lösen ordet från en krypterad Automation-variabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Grafiskt Runbook-exempel

Du lägger till en **Get-AutomationCertificate** i en grafisk Runbook genom att högerklicka på certifikatet i fönstret Bibliotek och välja **Lägg till på arbets ytan**.

![Lägg till certifikat på arbets ytan](../media/certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk Runbook. Detta är samma som föregående exempel som visar hur du lägger till ett certifikat i en moln tjänst från en text-Runbook.

![Exempel på grafisk redigering](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-exempel

Följande exempel visar hur du kommer åt certifikat i Python2-Runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om hur du arbetar med länkar för att kontrol lera det logiska flödet av aktiviteter som din Runbook har utformats för att utföra, se [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow). 
