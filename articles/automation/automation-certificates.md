---
title: Certifikattillgångar i Azure Automation
description: Certifikat kan lagras på ett säkert sätt i Azure Automation så att de kan nås av runbooks eller DSC-konfigurationer för att autentisera mot Azure och resurser från tredje part.  Den här artikeln beskriver hur du certifikat och hur du arbetar med dem i både text och grafiska redigering.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d314b7e56b769cf4a6488b769edadf5f5795fefe
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="certificate-assets-in-azure-automation"></a>Certifikattillgångar i Azure Automation

Certifikat kan lagras på ett säkert sätt i Azure Automation så att de kan nås av runbooks eller DSC-konfigurationer med hjälp av den **Get-AzureRmAutomationCertificate** aktivitet för Azure Resource Manager-resurser. Den här funktionen kan du skapa runbooks och DSC-konfigurationer som använder certifikat för autentisering eller lägger till dem i Azure eller resurser från tredje part.

>[!NOTE]
>Säkra tillgångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln lagras i Nyckelvalvet. Innan de lagras en säker resurs som lästs in från Nyckelvalvet nyckeln och sedan används för att kryptera tillgången.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdlets
För AzureRM för cmdlets i följande tabell att skapa och hantera automatisering inloggningstillgångar med Windows PowerShell. De levereras som en del av den [AzureRM.Automation modulen](/powershell/azure/overview) som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet: ar|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Hämtar information om ett certifikat som ska användas i en runbook eller DSC-konfigurationen. Du kan bara hämta själva certifikaten från Get-AutomationCertificate aktivitet.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Skapar ett nytt certifikat till Azure Automation.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Tar bort ett certifikat från Azure Automation.|Skapar ett nytt certifikat till Azure Automation.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Anger egenskaperna för ett befintligt certifikat, inklusive överföring av certifikatfilen och inställning av lösenordet för en PFX-fil.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Överför ett tjänstcertifikat för den angivna Molntjänsten.|

## <a name="activities"></a>Aktiviteter
Aktiviteterna i följande tabell används för att få åtkomst till certifikat i en runbook och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:---|:---|
|Get-AutomationCertificate|Hämtar ett certifikat som ska användas i en runbook eller DSC-konfigurationen. Returnerar en [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) objekt.|

> [!NOTE] 
> Du bör undvika att använda variabler i – Name-parametern i **Get-AutomationCertificate** i en runbook eller DSC-konfigurationen eftersom den leder till hitta beroenden mellan runbooks eller DSC-konfiguration och automatisering variabler vid designtillfället.

## <a name="python2-functions"></a>Python2 funktioner

Funktionen i följande tabell används för att få åtkomst till certifikat i en Python2 runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_certificate | Hämtar information om en certifikattillgång. |

> [!NOTE]
> Du måste importera den **automationassets** modul i början av din Python runbook för att komma åt funktionerna tillgång.

## <a name="creating-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat kan överföra du en CER- eller PFX-fil till Azure Automation. Om du markerar certifikatet som kan exporteras, kan du överföra den utanför Azure Automation-certifikatarkivet. Om du inte kan exporteras kan sedan den endast användas för signering i runbook eller DSC-konfigurationen. Azure Automation måste certifikatet ha providern: **Microsoft Enhanced RSA och AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Skapa ett nytt certifikat med Azure-portalen

1. Från ditt Automation-konto klickar du på den **tillgångar** öppna den **tillgångar** bladet.
1. Klicka på den **certifikat** öppna den **certifikat** bladet.
1. Klicka på **lägga till ett certifikat** längst upp på bladet.
1. Skriv ett namn för certifikatet i den **namn** rutan.
1. Om du vill bläddra till en CER- eller PFX-fil, klickar du på **Välj en fil** under **överför en certifikatfil**. Om du väljer en .pfx-fil, ange ett lösenord och om det är tillåtet att exporteras.
1. Klicka på **skapa** att spara den nya tillgången i certifikatet.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Skapa ett nytt certifikat med Windows PowerShell

Exemplet nedan visar hur du skapar ett nytt Automation-certifikat och markera den kan exporteras. Detta importerar en befintlig .pfx-fil.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Använder ett certifikat

Om du vill använda ett certifikat i **Get-AutomationCertificate** aktivitet. Du kan inte använda den [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) cmdlet eftersom den returnerar information om certifikat tillgången men inte själva certifikaten.

### <a name="textual-runbook-sample"></a>Text-runbook-exempel

Följande exempelkod visar hur du lägger till ett certifikat till en molntjänst i en runbook. I det här exemplet hämtas lösenordet från en krypterad automation-variabel.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Grafisk runbook-exempel

Du lägger till en **Get-AutomationCertificate** till en grafisk runbook genom att högerklicka på certifikatet i rutan bibliotek av grafiska redigerare och välja **Lägg till arbetsytan**.

![Lägg till certifikat till arbetsytan](media/automation-certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk runbook. Det här är samma som i föregående exempel för att lägga till ett certifikat till en molnbaserad tjänst från en textrepresentation runbook.

![Exempel grafiska redigering ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-exempel
I följande exempel visas hur du använder certifikat i Python2 runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du arbetar med länkar till logiska kontroll över din runbook är utformat för att utföra aktiviteter finns [länkar i grafiska redigering](automation-graphical-authoring-intro.md#links-and-workflow). 
