---
title: Anslutningstillgångar i Azure Automation
description: Anslutningsresurser i Azure Automation innehåller den information som krävs för att ansluta till en extern tjänst eller ett program från en runbook- eller DSC-konfiguration. I den här artikeln beskrivs information om anslutningar och hur du arbetar med dem i både text- och grafisk redigering.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: b6276153921feb0e6f27194d36d1c32c1d0ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940819"
---
# <a name="connection-assets-in-azure-automation"></a>Anslutningstillgångar i Azure Automation

En automationsanslutningstillgång innehåller den information som krävs för att ansluta till en extern tjänst eller ett externt program från en runbook- eller DSC-konfiguration. Detta kan omfatta information som krävs för autentisering, till exempel ett användarnamn och lösenord, utöver anslutningsinformation som en URL eller en port. Värdet för en anslutning behåller alla egenskaper för anslutning till ett visst program i en tillgång i stället för att skapa flera variabler. Användaren kan redigera värdena för en anslutning på ett ställe och du kan skicka namnet på en anslutning till en runbook- eller DSC-konfiguration i en enda parameter. Egenskaperna för en anslutning kan nås i runbook- eller DSC-konfigurationen med aktiviteten **Get-AutomationConnection.**

När du skapar en anslutning måste du ange en *anslutningstyp*. Anslutningstypen är en mall som definierar en uppsättning egenskaper. Anslutningen definierar värden för varje egenskap som definierats i dess anslutningstyp. Anslutningstyper läggs till i Azure Automation i integrationsmoduler eller skapas med [Azure Automation-API:et](/previous-versions/azure/reference/mt163818(v=azure.100)) om integrationsmodulen innehåller en anslutningstyp och importeras till ditt Automation-konto. Annars måste du skapa en metadatafil för att ange en automationsanslutningstyp. Mer information om detta finns i [Integrationsmoduler](automation-integration-modules.md).

>[!NOTE]
>Säkra resurser i Azure Automation innehåller autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje automationskonto. Den här nyckeln lagras i ett systemhanterade Key Vault. Innan du lagrar en säker tillgång läses nyckeln in från Key Vault och används sedan för att kryptera tillgången. Den här processen hanteras av Azure Automation.

## <a name="connection-types"></a>Anslutningstyper

Det finns tre typer av inbyggda anslutningar som är tillgängliga i Azure Automation:

* **Azure** - Den här anslutningen kan användas för att hantera klassiska resurser.
* **AzureClassicCertificate** - Den här anslutningen används av **AzureClassicRunAs-kontot.**
* **AzureServicePrincipal** - Den här anslutningen används av **AzureRunAs-kontot.**

I de flesta fall behöver du inte skapa en anslutningsresurs eftersom den skapas när du skapar ett [RunAs-konto](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-cmdletar

Cmdlets i följande tabell används för att skapa och hantera Automation-anslutningar med Windows PowerShell. De levereras som en del av [Azure PowerShell-modulen](/powershell/azure/overview), som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet|Beskrivning|
|:---|:---|
|[Hämta-AzureRmAutomationAnslutning](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Hämtar en anslutning. Innehåller en hash-tabell med värdena i anslutningens fält.|
|[Ny-AzureRmAutomationAnslutning](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Skapar en ny anslutning.|
|[Ta bort AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Tar bort en befintlig anslutning.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Ställer in värdet för ett visst fält för en befintlig anslutning.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt anslutningar i en runbook- eller DSC-konfiguration.

|Aktiviteter|Beskrivning|
|---|---|
|Get-AutomationConnection | Hämtar en anslutning att använda. Returnerar en hash-tabell med anslutningens egenskaper.|

>[!NOTE]
>Du bör undvika att använda variabler med parametern -Name i **Get-AutomationConnection** eftersom detta kan komplicera identifieringsberoenden mellan runbooks- eller DSC-konfigurationer och anslutningsresurser vid designtillfället.


## <a name="python2-functions"></a>Python2-funktioner

Funktionen i följande tabell används för att komma åt anslutningar i en Python2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_connection | Hämtar en anslutning. Returnerar en ordlista med anslutningens egenskaper. |

> [!NOTE]
> Du måste importera modulen "automationassets" högst upp i python-runbooken för att komma åt tillgångsfunktionerna.

## <a name="creating-a-new-connection"></a>Skapa en ny anslutning

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Så här skapar du en ny anslutning till Azure-portalen

1. Öppna bladet **Tillgångar** **Assets** från ditt automatiseringskonto.
2. Klicka på delen **Anslutningar** för att öppna bladet **Anslutningar.**
3. Klicka på **Lägg till en anslutning** högst upp på bladet.
4. Välj den typ av anslutning som du vill skapa i listrutan **Typ.** I formuläret visas egenskaperna för den aktuella typen.
5. Fyll i formuläret och klicka på **Skapa** för att spara den nya anslutningen.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Så här skapar du en ny anslutning med Windows PowerShell

Skapa en ny anslutning med Windows PowerShell med cmdleten [New-AzureRmAutomationConnection.](/powershell/module/azurerm.automation/new-azurermautomationconnection) Den här cmdleten har en parameter med namnet **ConnectionFieldValues** som förväntar sig att en [hash-tabell](https://technet.microsoft.com/library/hh847780.aspx) definierar värden för var och en av de egenskaper som definieras av anslutningstypen.

Om du är bekant med kontot För körning [som](automation-sec-configure-azure-runas-account.md) automatisering för att autentisera runbooks med hjälp av tjänstens huvudnamn, skapar PowerShell-skriptet, som tillhandahålls som ett alternativ till att skapa kontot Kör som från portalen, en ny anslutningstillgång med hjälp av följande exempelkommandon.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Du kan använda skriptet för att skapa anslutningstillgången eftersom när du skapar ditt Automation-konto innehåller det automatiskt flera globala moduler som standard tillsammans med anslutningstypen **AzureServicePrincipal** för att skapa **AzureRunAsConnection-anslutningstillgången.** Detta är viktigt att tänka på, för om du försöker skapa en ny anslutningstillgång för att ansluta till en tjänst eller ett program med en annan autentiseringsmetod, misslyckas den eftersom anslutningstypen inte redan har definierats i ditt Automation-konto. Mer information om hur du skapar en egen anslutningstyp för din anpassade eller modul från [PowerShell-galleriet](https://www.powershellgallery.com)finns i [Integrationsmoduler](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Använda en anslutning i en runbook- eller DSC-konfiguration

Du hämtar en anslutning i en runbook- eller DSC-konfiguration med **Cmdlet get-automation.** Du kan inte använda aktiviteten [Get-AzureRmAutomationConnection.](/powershell/module/azurerm.automation/get-azurermautomationconnection) Den här aktiviteten hämtar värdena för de olika fälten i anslutningen och returnerar dem som en [hash-tabell](https://go.microsoft.com/fwlink/?LinkID=324844), som sedan kan användas med lämpliga kommandon i runbook- eller DSC-konfigurationen.

### <a name="textual-runbook-sample"></a>Exempel på textkörningsbok

Följande exempelkommandon visar hur du använder kontot Kör som nämnts tidigare, för att autentisera med Azure Resource Manager-resurser i din runbook. Den använder anslutningstillgången som representerar kontot Kör som, som refererar till certifikatbaserade tjänstens huvudnamn, inte autentiseringsuppgifter.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När du söker i dina biblioteksobjekt, om du inte ser **Connect-AzureRMAccount,** kan du använda **Add-AzureRmAccount**eller uppdatera dina moduler i ditt Automation-konto.

### <a name="graphical-runbook-samples"></a>Grafiska runbook-exempel

Du lägger till en **Get-AutomationConnection-aktivitet** i en grafisk runbook genom att högerklicka på anslutningen i **biblioteksfönstret** i den grafiska redigeraren och välja **Lägg till på arbetsyta .**

![lägga till i arbetsyta](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder en anslutning i en grafisk runbook. Det här är samma exempel som visas ovan för att autentisera med kontot Kör som med en textkörningsbok. I det här exemplet används datauppsättningen **Konstant värde** för aktiviteten **Hämta RunAs-anslutning** som använder ett anslutningsobjekt för autentisering. En [pipeline-länk](automation-graphical-authoring-intro.md#links-and-workflow) används här eftersom parameteruppsättningen ServicePrincipalCertificate väntar ett enda objekt.

![hämta anslutningar](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Exempel på Python2-runbook

Följande exempel visar hur du autentiserar med hjälp av kör som-anslutningen i en Python2-runbook.

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
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

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
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Nästa steg

- Granska [länkar i grafisk redigering för](automation-graphical-authoring-intro.md#links-and-workflow) att förstå hur du styr och styr flödet av logik i dina runbooks.

- Mer information om Azure Automations användning av PowerShell-moduler och metodtips för att skapa egna PowerShell-moduler för att fungera som integrationsmoduler i Azure Automation finns i [Integrationsmoduler](automation-integration-modules.md).