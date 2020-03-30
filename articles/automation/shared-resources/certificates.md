---
title: Certifikattillgångar i Azure Automation
description: Certifikat finns säkert i Azure Automation så att de kan nås av runbooks eller DSC-konfigurationer för att autentisera mot Azure och tredjepartsresurser.  I den här artikeln beskrivs information om certifikat och hur du arbetar med dem i både text- och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849487"
---
# <a name="certificate-assets-in-azure-automation"></a>Certifikattillgångar i Azure Automation

Certifikat lagras säkert i Azure Automation så att de kan nås av runbooks eller DSC-konfigurationer med hjälp av aktiviteten **Get-AzureRmAutomationCertificate** för Azure Resource Manager-resurser. Med den här funktionen kan du skapa runbooks- och DSC-konfigurationer som använder certifikat för autentisering eller lägger till dem i Azure eller resurser från tredje part.

>[!NOTE]
>Säkra resurser i Azure Automation innehåller autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje automationskonto. Den här nyckeln lagras i ett systemhanterade Key Vault. Innan du lagrar en säker tillgång läses nyckeln in från Key Vault och används sedan för att kryptera tillgången. Den här processen hanteras av Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdlets

För AzureRM används cmdlets i följande tabell för att skapa och hantera resurser för automatiseringsautentiseringsuppgifter med Windows PowerShell. De levereras som en del av [Modulen AzureRM.Automation](/powershell/azure/overview), som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdletar|Beskrivning|
|:---|:---|
|[Hämta-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Hämtar information om ett certifikat som ska användas i en runbook- eller DSC-konfiguration. Du kan bara hämta själva certifikatet från Get-AutomationCertificate-aktivitet.|
|[Ny-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Skapar ett nytt certifikat i Azure Automation.|
[Ta bort AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Tar bort ett certifikat från Azure Automation.|
|[Ange-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Anger egenskaperna för ett befintligt certifikat, inklusive att överföra certifikatfilen och ange lösenordet för en PFX.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Laddar ett tjänstcertifikat för den angivna molntjänsten.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt certifikat i en runbook- och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:---|:---|
|Get-AutomationCertificate|Hämtar ett certifikat som ska användas i en runbook- eller DSC-konfiguration. Returnerar ett [System.Security.Cryptography.X509Certificates.X509Certificate2-objekt.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Du bör undvika att använda variabler i parametern -Name för **Get-AutomationCertificate** i en runbook- eller DSC-konfiguration eftersom det komplicerar identifieringsberoenden mellan runbooks- eller DSC-konfiguration och automatiseringsvariabler vid designtillfället.

## <a name="python2-functions"></a>Python2-funktioner

Funktionen i följande tabell används för att komma åt certifikat i en Python2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_certificate | Hämtar information om en certifikattillgång. |

> [!NOTE]
> Du måste importera **modulen automationassets** i början av python-runbooken för att kunna komma åt tillgångsfunktionerna.

## <a name="creating-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat laddar du upp en PFX- eller PFX-fil till Azure Automation. Om du markerar certifikatet som exportbart kan du överföra det från Azure Automation-certifikatarkivet. Om den inte kan exporteras kan den bara användas för signering i runbook- eller DSC-konfigurationen. Azure Automation kräver att certifikatet har leverantören: **Microsoft Enhanced RSA och AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Så här skapar du ett nytt certifikat med Azure-portalen

1. Öppna sidan **Tillgångar** på **Assets** ditt Automation-konto.
2. Klicka på panelen **Certifikat** för att öppna sidan **Certifikat.**
3. Klicka på **Lägg till ett certifikat** högst upp på sidan.
4. Ange ett namn på certifikatet i rutan **Namn.**
5. Om du vill söka efter en PF-fil eller PFX-fil klickar du på **Välj en fil** under Ladda upp en **certifikatfil**. Om du väljer en PFX-fil anger du ett lösenord och om det kan exporteras.
6. Klicka på **Skapa** om du vill spara den nya certifikattillgången.

### <a name="to-create-a-new-certificate-with-powershell"></a>Så här skapar du ett nytt certifikat med PowerShell

I följande exempel visas hur du skapar ett nytt Automation-certifikat och markerar det som kan exporteras. Detta importerar en befintlig PFX-fil.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Skapa ett nytt certifikat med Resource Manager-mall

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Använda ett certifikat

Använd aktiviteten **Get-AutomationCertificate** om du vill använda ett certifikat. Du kan inte använda cmdleten [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) eftersom den returnerar information om certifikattillgången men inte själva certifikatet.

### <a name="textual-runbook-sample"></a>Exempel på textkörningsbok

Följande exempelkod visar hur du lägger till ett certifikat i en molntjänst i en runbook. I det här exemplet hämtas lösenordet från en krypterad automatiseringsvariabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Exempel på grafisk runbook

Du lägger till ett **Get-AutomationCertificate** i en grafisk runbook genom att högerklicka på certifikatet i biblioteksfönstret och välja **Lägg till på arbetsyta .**

![Lägga till certifikat på arbetsytan](../media/certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk runbook. Det här är samma som föregående exempel som visar hur du lägger till ett certifikat i en molntjänst från en textkörningsbok.

![Exempel grafisk redigering](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-exempel

Följande exempel visar hur du kommer åt certifikat i Python2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du arbetar med länkar för att styra det logiska flödet av aktiviteter som runbooken är utformad för att utföra finns [i Länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow). 
