---
title: Certifikattillgångar i Azure Automation
description: Certifikat kan lagras på ett säkert sätt i Azure Automation så att de kan användas av runbooks och DSC-konfigurationer för att autentisera mot Azure och resurser från tredje part.  Den här artikeln beskrivs detaljer om certifikat och hur du arbetar med dem i både textbaserade och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 372df0bc6ea540b4b2c030ec71acc6889e7c7d19
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874094"
---
# <a name="certificate-assets-in-azure-automation"></a>Certifikattillgångar i Azure Automation

Certifikat kan lagras på ett säkert sätt i Azure Automation så att de kan användas av runbooks eller DSC-konfigurationer med hjälp av den **Get-AzureRmAutomationCertificate** aktivitet för Azure Resource Manager-resurser. Den här funktionen kan du skapa runbooks och DSC-konfigurationer som använder certifikat för autentisering eller lägger till dem i Azure eller resurser från tredje part.

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln lagras i Key Vault hanteras av en datorn. Innan du lagrar en säker resurs som lästs in från Key Vault nyckeln och sedan används för att kryptera tillgången. Den här processen hanteras av Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdletar
För AzureRM används cmdletar i följande tabell för att skapa och hantera inloggningstillgångar i automation med Windows PowerShell. De levereras som en del av den [AzureRM.Automation modulen](/powershell/azure/overview) som är tillgängligt för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet: ar|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Hämtar information om ett certifikat som ska användas i en runbook eller DSC-konfiguration. Du kan bara hämta själva certifikaten från Get-AutomationCertificate aktivitet.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Skapar ett nytt certifikat till Azure Automation.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Tar bort ett certifikat från Azure Automation.|Skapar ett nytt certifikat till Azure Automation.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Anger egenskaperna för ett befintligt certifikat, inklusive överföring av certifikatfilen och inställning av lösenordet för PFX-fil.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Överför ett tjänstcertifikat för den angivna Molntjänsten.|

## <a name="activities"></a>Aktiviteter
Aktiviteterna i följande tabell används för att få åtkomst till certifikat i en runbook och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:---|:---|
|Get-AutomationCertificate|Hämtar ett certifikat som ska användas i en runbook eller DSC-konfiguration. Returnerar en [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) objekt.|

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

1. Från ditt Automation-konto klickar du på den **tillgångar** panelen för att öppna den **tillgångar** bladet.
1. Klicka på den **certifikat** panelen för att öppna den **certifikat** bladet.
1. Klicka på **lägga till ett certifikat** överst på bladet.
1. Skriv ett namn för certifikatet i den **namn** box.
1. Om du vill bläddra till en CER- eller PFX-fil, klickar du på **Välj en fil** under **ladda upp en certifikatfil**. Om du väljer en .pfx-fil, ange ett lösenord och om det är tillåtet att exporteras.
1. Klicka på **skapa** att spara den nya certifikattillgången.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Att skapa ett nytt certifikat med Windows PowerShell

I följande exempel visar hur du skapar ett nytt Automation-certifikat och markera den kan exporteras. Det importerar en befintlig PFX-fil.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Med hjälp av ett certifikat

Om du vill använda ett certifikat måste använda den **Get-AutomationCertificate** aktivitet. Du kan inte använda den [Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-6.6.0) cmdlet eftersom den returnerar information om certifikattillgången men inte själva certifikaten.

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

Du lägger till en **Get-AutomationCertificate** till en grafisk runbook genom att högerklicka på certifikatet i rutan bibliotek för den grafiska redigeraren och välja **Lägg till på ytan**.

![Lägg till certifikat till arbetsytan](media/automation-certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk runbook. Det här är samma som i föregående exempel för att lägga till ett certifikat i en molnbaserad tjänst från en text runbook.

![Exempel grafisk redigering](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-exempel
I följande exempel visas hur du kommer åt certifikat i Python2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om att arbeta med länkar till att styra det logiska flödet för din runbook har utformats för att utföra aktiviteter i [länkar i grafisk redigering](automation-graphical-authoring-intro.md#links-and-workflow). 

