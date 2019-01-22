---
title: Anslutningstillgångar i Azure Automation
description: Anslutningstillgångar i Azure Automation innehåller den information som krävs för att ansluta till en extern tjänst eller program från en runbook eller DSC-konfiguration. Den här artikeln beskrivs detaljer om anslutningar och hur du arbetar med dem i både textbaserade och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e00eb5756d34c7ca8cecc741b4832c583a6ed087
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54439025"
---
# <a name="connection-assets-in-azure-automation"></a>Anslutningstillgångar i Azure Automation

En Automation-anslutningstillgång innehåller den information som krävs för att ansluta till en extern tjänst eller program från en runbook eller DSC-konfiguration. Detta kan inkludera information som krävs för autentisering, t.ex ett användarnamn och lösenord utöver anslutningsinformation, till exempel en URL eller en port. Värdet för en anslutning är att hålla alla egenskaper för att ansluta till ett visst program i en tillgång till skillnad från skapar flera variabler. Du kan redigera värdena för en anslutning på samma plats och du kan skicka namnet på en anslutning till en runbook eller DSC-konfiguration i en enda parameter. Egenskaperna för en anslutning kan nås i runbook eller DSC-konfiguration med den **Get-AutomationConnection** aktivitet. 

När du skapar en anslutning måste du ange en *anslutningstypen*. Anslutningstypen är en mall som definierar en uppsättning egenskaper. Anslutningen definierar värden för varje egenskap som definierats i dess anslutningstyp. Anslutningstyper som läggs till Azure Automation i integreringsmoduler eller skapats med den [Azure Automation API](https://msdn.microsoft.com/library/azure/mt163818.aspx) om integration-modulen innehåller en anslutningstyp och importeras till ditt Automation-konto. I annat fall behöver du skapa en metadatafil om du vill ange en Automation-anslutningstyp.  Ytterligare information om detta finns i [integreringsmoduler](automation-integration-modules.md).  

>[!NOTE]
>Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln lagras i Key Vault hanteras av en datorn. Innan du lagrar en säker resurs som lästs in från Key Vault nyckeln och sedan används för att kryptera tillgången. Den här processen hanteras av Azure Automation.

## <a name="connection-types"></a>Anslutningstyper

Det finns tre typer av inbyggda anslutningar i Azure Automation:

* **Azure** – den här anslutningen kan användas för att hantera klassiska resurser.
* **AzureClassicCertificate** -den här anslutningen används av den **AzureClassicRunAs** konto.
* **AzureServicePrincipal** -den här anslutningen används av den **AzureRunAs** konto.

I de flesta fall behöver du inte skapa en anslutningsresurs när den skapas när du skapar en [RunAs-kontot](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets

Cmdlets i följande tabell används för att skapa och hantera anslutningar för Automation med Windows PowerShell. De levereras som en del av den [Azure PowerShell-modulen](/powershell/azure/overview) som är tillgängligt för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Hämtar en anslutning. Innehåller en hash-tabell med värdena för anslutningens fält.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Skapar en ny anslutning.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Ta bort en befintlig anslutning.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Anger värdet för ett visst fält för en befintlig anslutning.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt anslutningar i en runbook eller DSC-konfiguration.

|Aktiviteter|Beskrivning|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Hämtar en anslutning som ska användas. Returnerar en hash-tabell med egenskaperna för anslutningen.|

>[!NOTE] 
>Du bör undvika att använda variabler med – Name-parametern i **Get-AutomationConnection** eftersom detta kan göra det svårare att hitta beroenden mellan runbooks eller DSC-konfigurationer och anslutningstillgångar vid designtillfället.

 
## <a name="python2-functions"></a>Python2-funktioner 
Funktionen i följande tabell används för att komma åt anslutningar i en Python2-runbook. 

| Funktion | Beskrivning | 
|:---|:---| 
| automationassets.get_automation_connection | Hämtar en anslutning. Returnerar en ordlista med egenskaper för anslutningen. | 

> [!NOTE] 
> Du måste importera modulen ”automationassets” överst i Python-runbook för att komma åt funktionerna för tillgången.

## <a name="creating-a-new-connection"></a>Skapa en ny anslutning

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Skapa en ny anslutning med Azure portal

1. Från ditt automation-konto klickar du på den **tillgångar** en del för att öppna den **tillgångar** bladet.
2. Klicka på den **anslutningar** en del för att öppna den **anslutningar** bladet.
3. Klicka på **lägga till en anslutning** överst på bladet.
4. I den **typ** listrutan, Välj typ av anslutning som du vill skapa. Formuläret visar egenskaperna för den här typen.
5. Fyll i formuläret och klicka på **skapa** att spara den nya anslutningen.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Att skapa en ny anslutning med Windows PowerShell

Skapa en ny anslutning med Windows PowerShell med den [New AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) cmdlet. Denna cmdlet har en parameter med namnet **ConnectionFieldValues** som förväntar sig en [hash-tabell](https://technet.microsoft.com/library/hh847780.aspx) Definiera värden för var och en av egenskaperna som definierats av anslutningstypen.

Om du är bekant med Automation [kör som-konto](automation-sec-configure-azure-runas-account.md) för att autentisera runbooks med tjänstens huvudnamn, PowerShell-skriptet som anges som ett alternativ till att skapa kör som-kontot från portalen, skapar en ny anslutning tillgång med följande exempelkommandon.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Du kan använda skript för att skapa anslutningstillgången eftersom när du skapar ditt Automation-konto kan den automatiskt innehåller flera globala modulerna som standard tillsammans med anslutningstypen **AzureServicePrincipal** till skapa den **AzureRunAsConnection** anslutningstillgång.  Detta är viktigt att tänka på, eftersom om du försöker skapa en ny anslutningstillgång för att ansluta till en tjänst eller program med en annan autentiseringsmetod, går det inte eftersom anslutningstypen inte redan har definierats i ditt Automation-konto.  Mer information om hur du skapar dina egna anslutningstyp för din anpassade eller modul från den [PowerShell-galleriet](https://www.powershellgallery.com), se [integreringsmoduler](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Med hjälp av en anslutning i en runbook eller DSC-konfiguration

Du hämtar en anslutning i en runbook eller DSC-konfiguration med den **Get-AutomationConnection** cmdlet.  Du kan inte använda den [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) aktivitet.  Den här aktiviteten hämtar fältvärdena i olika fält i anslutningen och returnerar dem som en [hash-tabell](https://go.microsoft.com/fwlink/?LinkID=324844) som sedan kan användas med lämpliga kommandon i runbook eller DSC-konfiguration.

### <a name="textual-runbook-sample"></a>Textbaserade runbook-exempel

Följande exempelkommandon visar hur du använder Kör som-kontot tidigare nämnda för att autentisera med Azure Resource Manager-resurser i din runbook.  Anslutningstillgången som representerar det kör som-konto som refererar till certifikatbaserad tjänstens huvudnamn, inte autentiseringsuppgifter används.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När sökningen biblioteket objekt, om du inte ser **Connect-AzureRMAccount**, du kan använda **Add-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

### <a name="graphical-runbook-samples"></a>Grafiska runbook-exempel

Du lägger till en **Get-AutomationConnection** aktivitet för att en grafisk runbook genom att högerklicka på anslutningen i rutan bibliotek över den grafiska redigeraren och välja **Lägg till på ytan**.

![Lägg till på ytan](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder en anslutning i en grafisk runbook.  Det här är det här exemplet som visas ovan för att autentisera med hjälp av kör som-konto med en textbaserade runbook.  Det här exemplet används den **konstantvärde** datauppsättning efter den **hämta RunAs-anslutning** aktivitet som använder ett anslutningsobjekt för autentisering.  En [pipelinelänk](automation-graphical-authoring-intro.md#links-and-workflow) kan användas här eftersom parameteruppsättningen ServicePrincipalCertificate förväntar ett enskilt objekt.

![Hämta-anslutningar](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 runbook-exempel
I följande exempel visas hur man autentiserar med Kör som-anslutningen i en Python2-runbook.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
  """ Returns credentials to authenticate against Azure resoruce manager """
  from OpenSSL import crypto
  from msrestazure import azure_active_directory
  import adal

  # Get the Azure Automation Run As service principal certificate
  cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
  pks12_cert = crypto.load_pkcs12(cert)
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

  # Get Run As connection information for the Azure Automation service principal
  application_id = runas_connection["ApplicationId"]
  thumbprint = runas_connection["CertificateThumbprint"]
  tenant_id = runas_connection["TenantId"]

  # Authenticate with service principal certificate
  resource = "https://management.core.windows.net/"
  authority_url = ("https://login.microsoftonline.com/" + tenant_id)
  context = adal.AuthenticationContext(authority_url)
  return azure_active_directory.AdalAuthentication(
    lambda: context.acquire_token_with_client_certificate(
      resource,
      application_id,
      pem_pkey,
      thumbprint)
  )

# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Nästa steg

- Granska [länkar i grafisk redigering](automation-graphical-authoring-intro.md#links-and-workflow) vill lära dig att dirigera och styra flödet av logiken i dina runbooks.  

- Läs mer om hur Azure Automation använder PowerShell-moduler och bästa praxis för att skapa egna PowerShell-moduler fungerar som integreringsmoduler i Azure Automation i [integreringsmoduler](automation-integration-modules.md).  

