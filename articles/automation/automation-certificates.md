---
title: "Tillgångar i Azure Automation-certifikat | Microsoft Docs"
description: "Certifikat kan lagras på ett säkert sätt i Azure Automation så att de kan nås av runbooks eller DSC-konfigurationer för att autentisera mot Azure och resurser från tredje part.  Den här artikeln beskriver hur du certifikat och hur du arbetar med dem i både text och grafiska redigering."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: b6a5ff4fa3fd0084fd910968651c6ae0fefaf2cf
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="certificate-assets-in-azure-automation"></a>Certifikattillgångar i Azure Automation

Certifikat kan lagras på ett säkert sätt i Azure Automation så att de kan nås av runbooks eller DSC-konfigurationer med hjälp av den **Get-AzureRmAutomationCertificate** aktivitet för Azure Resource Manager-resurser. Detta gör att du kan skapa runbooks och DSC-konfigurationer som använder certifikat för autentisering eller lägger till dem i Azure eller resurser från tredje part.

> [!NOTE] 
> Säkra tillgångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure-Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln är krypterad med ett certifikat för master och lagras i Azure Automation. Innan du sparar en säker resurs, nyckeln för automation-kontot dekrypteras med master certifikatet och sedan används för att kryptera tillgången.
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets

Cmdlets i följande tabell används för att skapa och hantera automatisering certifikattillgångar med Windows PowerShell. De levereras som en del av den [Azure PowerShell-modulen](../powershell-install-configure.md) som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet: ar|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|Hämtar information om ett certifikat som ska användas i en runbook eller DSC-konfigurationen. Du kan bara hämta själva certifikaten från Get-AutomationCertificate aktivitet.|
|[Ny AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|Skapar ett nytt certifikat till Azure Automation.|
[Ta bort AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|Tar bort ett certifikat från Azure Automation.|Skapar ett nytt certifikat till Azure Automation.
|[Ange AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|Anger egenskaperna för ett befintligt certifikat, inklusive överföring av certifikatfilen och inställning av lösenordet för en PFX-fil.|
|[Lägg till AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Överför ett tjänstcertifikat för den angivna Molntjänsten.|


## <a name="python2-functions"></a>Python2 funktioner

Funktionen i följande tabell används för att få åtkomst till certifikat i en Python2 runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_certificate | Hämtar information om en certifikattillgång. |

> [!NOTE]
> Du måste importera den **automationassets** modul i början av din Python runbook för att komma åt funktionerna tillgång.


## <a name="creating-a-new-certificate"></a>Skapa ett nytt certifikat

När du skapar ett nytt certifikat kan överföra du en CER- eller PFX-fil till Azure Automation. Om du markerar certifikatet som kan exporteras, kan du överföra den utanför Azure Automation-certifikatarkivet. Om du inte kan exporteras kan sedan den endast användas för signering i runbook eller DSC-konfigurationen.


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Skapa ett nytt certifikat med Azure-portalen

1. Från ditt Automation-konto klickar du på den **tillgångar** öppna den **tillgångar** bladet.
1. Klicka på den **certifikat** öppna den **certifikat** bladet.
1. Klicka på **lägga till ett certifikat** längst upp på bladet.
2. Skriv ett namn för certifikatet i den **namn** rutan.
2. Klicka på **Välj en fil** under **överför en certifikatfil** och bläddrar till en CER- eller PFX-fil.  Om du väljer en .pfx-fil, ange ett lösenord och om den ska kunna exporteras.
1. Klicka på **skapa** att spara den nya tillgången i certifikatet.


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Skapa ett nytt certifikat med Windows PowerShell

Exemplet nedan visar hur du skapar ett nytt Automation-certifikat och markera den kan exporteras. Detta importerar en befintlig .pfx-fil.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>Använder ett certifikat

Du måste använda den **Get-AutomationCertificate** aktivitet för att använda ett certifikat. Du kan inte använda den [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) cmdlet eftersom den returnerar information om certifikat tillgången men inte själva certifikaten.

### <a name="textual-runbook-sample"></a>Text-runbook-exempel

Följande exempelkod visar hur du lägger till ett certifikat till en molntjänst i en runbook. I det här exemplet hämtas lösenordet från en krypterad automation-variabel.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Grafisk runbook-exempel

Du lägger till en **Get-AutomationCertificate** till en grafisk runbook genom att högerklicka på certifikatet i rutan bibliotek av grafiska redigerare och välja **Lägg till arbetsytan**.

![Lägg till certifikat till arbetsytan](media/automation-certificates/automation-certificate-add-to-canvas.png)

Följande bild visar ett exempel på hur du använder ett certifikat i en grafisk runbook.  Detta är det här exemplet ovan för att lägga till ett certifikat till en molnbaserad tjänst från en textrepresentation runbook.

![Exempel grafiska redigering ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2-exempel
I följande exempel visas hur du använder certifikat i Python2 runbooks.

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du arbetar med länkar till logiska kontroll över din runbook är utformat för att utföra aktiviteter finns [länkar i grafiska redigering](automation-graphical-authoring-intro.md#links-and-workflow). 
