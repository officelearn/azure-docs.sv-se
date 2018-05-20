---
title: Anslutningstillgångar i Azure Automation
description: Anslutningstillgångar i Azure Automation innehåller den information som krävs för att ansluta till en extern tjänst eller program från en runbook eller DSC-konfigurationen. Den här artikeln beskriver hur du anslutningar och hur du arbetar med dem i både text och grafiska redigering.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 877cdcc10e32dd915adc3579d5a08c2826d2a6e6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="connection-assets-in-azure-automation"></a>Anslutningstillgångar i Azure Automation

Ett Automation-anslutningstillgång innehåller den information som krävs för att ansluta till en extern tjänst eller program från en runbook eller DSC-konfigurationen. Detta kan inkludera information som krävs för autentisering, till exempel användarnamn och lösenord förutom anslutningsinformation, till exempel en URL eller en port. Värdet för en anslutning hindrar alla egenskaper för att ansluta till ett visst program i en tillgång i stället för att skapa flera variabler. Användaren kan redigera värdena för en anslutning i en enda plats och du kan ange namnet på en anslutning till en runbook eller DSC-konfigurationen i en parameter. Egenskaperna för en anslutning kan nås i runbook eller DSC-konfigurationen med den **Get-AutomationConnection** aktivitet. 

När du skapar en anslutning måste du ange en *anslutningstypen*. Anslutningstypen är en mall som definierar en uppsättning egenskaper. Anslutningen definierar värden för varje egenskap som definierats i dess anslutningstypen. Anslutningstyper läggs till Azure Automation i integreringsmoduler eller skapats med den [Azure Automation API](http://msdn.microsoft.com/library/azure/mt163818.aspx) om integrationsmodulen innehåller en anslutningstyp och importeras till ditt Automation-konto. I annat fall behöver du skapa en metadatafil om du vill ange ett Automation-anslutningstypen.  Mer information om detta finns [integreringsmoduler](automation-integration-modules.md).  

>[!NOTE]
>Säkra tillgångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln lagras i Nyckelvalvet. Innan de lagras en säker resurs som lästs in från Nyckelvalvet nyckeln och sedan används för att kryptera tillgången.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets

Cmdlets i följande tabell används för att skapa och hantera anslutningar för Automation med Windows PowerShell. De levereras som en del av den [Azure PowerShell-modulen](/powershell/azure/overview) som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Hämtar en anslutning. Innehåller en hash-tabell med värdena i den anslutningen.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Skapar en ny anslutning.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Ta bort en befintlig anslutning.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Anger värdet för ett visst fält för en befintlig anslutning.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt anslutningar i en runbook eller DSC-konfigurationen.

|Aktiviteter|Beskrivning|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Hämtar en anslutning för användning. Returnerar en hash-tabell med egenskaperna för anslutningen.|

>[!NOTE] 
>Du bör undvika att använda variabler med – Name-parametern i **Get - AutomationConnection** eftersom detta kan göra det svårare att hitta beroenden mellan runbooks eller DSC-konfigurationer och anslutningstillgångar i designläge.

 
## <a name="python2-functions"></a>Python2 funktioner 
Funktionen i följande tabell används för att komma åt anslutningar i en Python2 runbook. 

| Funktion | Beskrivning | 
|:---|:---| 
| automationassets.get_automation_connection | Hämtar en anslutning. Returnerar en ordlista med egenskaperna för anslutningen. | 

> [!NOTE] 
> Du måste importera modulen ”automationassets” längst upp i Python-runbook för att komma åt funktionerna tillgång.

## <a name="creating-a-new-connection"></a>Skapa en ny anslutning

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Skapa en ny anslutning med Azure-portalen

1. Från ditt automation-konto klickar du på den **tillgångar** del för att öppna den **tillgångar** bladet.
2. Klicka på den **anslutningar** del för att öppna den **anslutningar** bladet.
3. Klicka på **lägga till en anslutning** längst upp på bladet.
4. I den **typen** listrutan, Välj typ av anslutning som du vill skapa. Formuläret visas egenskaperna för den här typen.
5. Fyll i formuläret och klicka på **skapa** att spara den nya anslutningen.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Skapa en ny anslutning med Windows PowerShell

Skapa en ny anslutning med Windows PowerShell med den [ny AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) cmdlet. Denna cmdlet har en parameter med namnet **ConnectionFieldValues** som förväntar sig en [hash-tabell](http://technet.microsoft.com/library/hh847780.aspx) Definiera värden för var och en av de egenskaper som definierats av anslutningstypen.

Om du är bekant med automatisering [kör som-konto](automation-sec-configure-azure-runas-account.md) för att autentisera runbooks med tjänstens huvudnamn PowerShell-skriptet som tillhandahålls som ett alternativ till att skapa kör som-konto från portalen, skapas en ny anslutning tillgångsinformation med hjälp av följande exempelkommandon.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 
```

Du kan använda skript för att skapa anslutningen tillgången eftersom när du skapar ditt Automation-konto kan den automatiskt innehåller flera globala moduler som standard tillsammans med anslutningstypen **AzurServicePrincipal** att skapa den **AzureRunAsConnection** anslutningstillgång.  Detta är viktigt att tänka på, eftersom om du försöker skapa en ny anslutningstillgång för att ansluta till en tjänst eller program med en annan autentiseringsmetod det misslyckas eftersom typen inte redan har definierats i Automation-kontot.  Mer information om hur du skapar egna anslutningstypen för anpassad eller modul från den [PowerShell-galleriet](https://www.powershellgallery.com), se [integreringsmoduler](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Med hjälp av en anslutning i en runbook eller DSC-konfiguration

Du kan hämta en anslutning i en runbook eller DSC-konfigurationen med den **Get-AutomationConnection** cmdlet.  Du kan inte använda den [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn) aktivitet.  Den här aktiviteten hämtar värdena för olika fält i anslutningen och returnerar dem som en [hash-tabell](http://go.microsoft.com/fwlink/?LinkID=324844) som sedan kan användas med lämpliga kommandon i runbook eller DSC-konfigurationen.

### <a name="textual-runbook-sample"></a>Text-runbook-exempel

Följande exempelkommandon visar hur du använder Kör som-kontot tidigare nämnts för att autentisera med Azure Resource Manager-resurser i din runbook.  Den använder anslutningstillgång som representerar det kör som-konto som refererar till certifikatbaserad tjänstens huvudnamn, inte autentiseringsuppgifter.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint 
```

### <a name="graphical-runbook-samples"></a>Grafiska runbook-exempel

Du lägger till en **Get-AutomationConnection** aktivitet till en grafisk runbook genom att högerklicka på anslutningen i rutan bibliotek över grafiska redigerare och välja **Lägg till arbetsytan**.

![](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder en anslutning i en grafisk runbook.  Detta är det här exemplet ovan för att autentisera med hjälp av kör som-kontot med text-runbook.  Det här exemplet används den **konstantvärde** datauppsättning för den **hämta RunAs-anslutning** aktivitet som använder ett anslutningsobjekt för autentisering.  En [pipelinelänk](automation-graphical-authoring-intro.md#links-and-workflow) används här eftersom parameteruppsättning ServicePrincipalCertificate förväntar sig ett enskilt objekt.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 runbook-exempel
I följande exempel visas hur du autentiserar med hjälp av kör som-anslutningen i en Python2 runbook.

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

- Granska [länkar i grafiska redigering](automation-graphical-authoring-intro.md#links-and-workflow) att förstå hur du direkt och styr flödet av logiken i dina runbooks.  

- Mer information om hur Azure Automation använder PowerShell-moduler och bästa praxis för att skapa egna PowerShell-moduler för att fungera som integreringsmoduler i Azure Automation finns [integreringsmoduler](automation-integration-modules.md).  
