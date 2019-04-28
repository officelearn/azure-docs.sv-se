---
title: Certifikattillgångar i Azure Automation
description: Certifikat är på ett säkert sätt i Azure Automation så att de kan användas av runbooks och DSC-konfigurationer för att autentisera mot Azure och resurser från tredje part.  Den här artikeln beskrivs detaljer om certifikat och hur du arbetar med dem i både textbaserade och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d230fa97d009f0ee2a3bc86a0b6b7c8d40687a46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216091"
---
# <a name="certificate-assets-in-azure-automation"></a>Certifikattillgångar i Azure Automation

Certifikat som lagras på ett säkert sätt i Azure Automation så att de kan användas av runbooks eller DSC-konfigurationer med hjälp av den **Get-AzureRmAutomationCertificate** aktivitet för Azure Resource Manager-resurser. Den här funktionen kan du skapa runbooks och DSC-konfigurationer som använder certifikat för autentisering eller lägger till dem i Azure eller resurser från tredje part.

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln lagras i Key Vault hanteras av en datorn. Innan du lagrar en säker resurs som lästs in från Key Vault nyckeln och sedan används för att kryptera tillgången. Den här processen hanteras av Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdletar

För AzureRM används cmdletar i följande tabell för att skapa och hantera inloggningstillgångar i automation med Windows PowerShell. De levereras som en del av den [AzureRM.Automation modulen](/powershell/azure/overview), som är tillgängligt för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet: ar|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Hämtar information om ett certifikat som ska användas i en runbook eller DSC-konfiguration. Du kan bara hämta själva certifikaten från Get-AutomationCertificate aktivitet.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Skapar ett nytt certifikat till Azure Automation.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Tar bort ett certifikat från Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Anger egenskaperna för ett befintligt certifikat, inklusive överföring av certifikatfilen och inställning av lösenordet för PFX-fil.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Överför ett tjänstcertifikat för den angivna Molntjänsten.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att få åtkomst till certifikat i en runbook och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:---|:---|
|Get-AutomationCertificate|Hämtar ett certifikat som ska användas i en runbook eller DSC-konfiguration. Returnerar en [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) objekt.|

> [!NOTE] 
> Du bör undvika att använda variabler i – Name-parametern i **Get-AutomationCertificate** i en runbook eller DSC-konfiguration som den komplicerar hitta beroenden mellan runbooks eller DSC-konfiguration och automationsvariabler vid designtillfället.

## <a name="python2-functions"></a>Python2-funktioner

Funktionen i följande tabell används för att få åtkomst till certifikat i en Python2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_certificate | Hämtar information om en certifikattillgång. |

> [!NOTE]
> Du måste importera den **automationassets** modul i början av din Python-runbook för att komma åt funktionerna för tillgången.

## <a name="creating-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat kan överföra du en CER- eller PFX-fil till Azure Automation. Om du markerar certifikatet som kan exporteras, kan du överföra den utanför Azure Automation-certifikatarkivet. Om den inte kan exporteras, kan sedan den endast användas för signering i runbook eller DSC-konfiguration. Azure Automation kräver certifikatet ha providern: **Microsoft Enhanced RSA och AES kryptografiprovider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Skapa ett nytt certifikat med Azure portal

1. Från ditt Automation-konto klickar du på den **tillgångar** panelen för att öppna den **tillgångar** sidan.
2. Klicka på den **certifikat** panelen för att öppna den **certifikat** sidan.
3. Klicka på **lägga till ett certifikat** överst på sidan.
4. Skriv ett namn för certifikatet i den **namn** box.
5. Om du vill bläddra till en CER- eller PFX-fil, klickar du på **Välj en fil** under **ladda upp en certifikatfil**. Om du väljer en .pfx-fil, ange ett lösenord och om den kan exporteras.
6. Klicka på **skapa** att spara den nya certifikattillgången.

### <a name="to-create-a-new-certificate-with-powershell"></a>Att skapa ett nytt certifikat med PowerShell

I följande exempel visar hur du skapar ett nytt Automation-certifikat och markera den kan exporteras. Det importerar en befintlig PFX-fil.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Skapa ett nytt certifikat med Resource Manager-mall

I följande exempel visar hur du distribuerar ett certifikat till ditt Automation-konto med en Resource Manager-mall med PowerShell:

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

## <a name="using-a-certificate"></a>Med hjälp av ett certifikat

Om du vill använda ett certifikat måste använda den **Get-AutomationCertificate** aktivitet. Du kan inte använda den [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) cmdlet eftersom den returnerar information om certifikattillgången men inte själva certifikaten.

### <a name="textual-runbook-sample"></a>Textbaserade runbook-exempel

Följande exempelkod visar hur du lägger till ett certifikat till en molntjänst i en runbook. I det här exemplet hämtas lösenordet från en krypterad automation-variabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Grafisk runbook-exempel

Du lägger till en **Get-AutomationCertificate** till en grafisk runbook genom att högerklicka på certifikatet i rutan bibliotek och välja **Lägg till på ytan**.

![Lägg till certifikat till arbetsytan](../media/certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk runbook. Det här är samma som i föregående exempel som visar hur du lägger till ett certifikat i en molnbaserad tjänst från en text runbook.

![Exempel grafisk redigering](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-exempel

I följande exempel visas hur du kommer åt certifikat i Python2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om att arbeta med länkar till att styra det logiska flödet för din runbook har utformats för att utföra aktiviteter i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow). 
