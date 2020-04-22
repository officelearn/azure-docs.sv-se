---
title: Hantera anslutningar i Azure Automation
description: Anslutningsresurser i Azure Automation innehåller den information som krävs för att ansluta till en extern tjänst eller ett program från en runbook- eller DSC-konfiguration. I den här artikeln beskrivs information om anslutningar och hur du arbetar med dem i både text- och grafisk redigering.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 90d4ec1bbfd0d76ffedf1505c9147376e3947c3c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729045"
---
# <a name="manage-connections-in-azure-automation"></a>Hantera anslutningar i Azure Automation

En automationsanslutningstillgång innehåller den information som krävs för att ansluta till en extern tjänst eller ett externt program från en runbook- eller DSC-konfiguration. Detta kan omfatta information som krävs för autentisering, till exempel ett användarnamn och lösenord, utöver anslutningsinformation som en URL eller en port. Värdet för en anslutning behåller alla egenskaper för anslutning till ett visst program i en tillgång i stället för att skapa flera variabler. Användaren kan redigera värdena för en anslutning på ett ställe och du kan skicka namnet på en anslutning till en runbook- eller DSC-konfiguration i en enda parameter. Egenskaperna för en anslutning kan nås i runbook- eller `Get-AutomationConnection` DSC-konfigurationen med aktiviteten.

När du skapar en anslutning måste du ange en anslutningstyp. Anslutningstypen är en mall som definierar en uppsättning egenskaper. Anslutningen definierar värden för varje egenskap som definierats i dess anslutningstyp. Anslutningstyper läggs till i Azure Automation i integrationsmoduler eller skapas med [Azure Automation-API:et](/previous-versions/azure/reference/mt163818(v=azure.100)) om integrationsmodulen innehåller en anslutningstyp och importeras till ditt Automation-konto. Annars måste du skapa en metadatafil för att ange en Automation-anslutningstyp. Mer information om detta finns i [Integrationsmoduler](automation-integration-modules.md).

>[!NOTE]
>Säkra resurser i Azure Automation innehåller autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i ett systemhanterade Key Vault. Innan du lagrar en säker tillgång läses nyckeln in från Key Vault och används sedan för att kryptera tillgången. Den här processen hanteras av Azure Automation.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="connection-types"></a>Anslutningstyper

Det finns tre typer av inbyggda anslutningar som är tillgängliga i Azure Automation:

* **Azure** - Den här anslutningen kan användas för att hantera klassiska resurser.
* **AzureClassicCertificate** - Den här anslutningen används av **AzureClassicRunAs-kontot.**
* **AzureServicePrincipal** - Den här anslutningen används av **AzureRunAs-kontot.**

I de flesta fall behöver du inte skapa en anslutningsresurs eftersom den skapas när du skapar ett [Kör som-konto](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-cmdlet:ar

Cmdlets i följande tabell används för att skapa och hantera Automation-anslutningar med Windows PowerShell. De levereras som en del av [Azure PowerShell-modulen](/powershell/azure/overview), som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

|Cmdlet|Beskrivning|
|---|---|
|[Få-AzAutomationAnslutning](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Hämtar en anslutning. Innehåller en hashtable med värdena i anslutningsfälten.|
|[Ny-AzAutomationAnslutning](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Skapar en ny anslutning.|
|[Ta bort-AzAutomationAnslutning](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Tar bort en befintlig anslutning.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Ställer in värdet för ett visst fält för en befintlig anslutning.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt anslutningar i en runbook- eller DSC-konfiguration.

|Aktiviteter|Beskrivning|
|---|---|
|`Get-AutomationConnection` | Hämtar en anslutning att använda. Returnerar en hashtable med anslutningens egenskaper.|

>[!NOTE]
>Undvik att använda `Name` variabler `Get-AutomationConnection`med parametern . Användning av den här parametern kan försvåra identifiering av beroenden mellan runbooks- eller DSC-konfigurationer och anslutningsresurser vid designtillfället.

## <a name="python-2-functions"></a>Python 2-funktioner

Funktionen i följande tabell används för att komma åt anslutningar i en Python 2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_connection` | Hämtar en anslutning. Returnerar en ordlista med anslutningens egenskaper. |

> [!NOTE]
> Du måste `automationassets` importera modulen högst upp i Python-runbooken för att komma åt tillgångsfunktionerna.

## <a name="creating-a-new-connection"></a>Skapa en ny anslutning

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Så här skapar du en ny anslutning till Azure-portalen

1. Öppna bladet **Tillgångar** **Assets** från ditt Automation-konto.
2. Klicka på delen **Anslutningar** för att öppna bladet **Anslutningar.**
3. Klicka på **Lägg till en anslutning** högst upp på bladet.
4. Välj den typ av anslutning som du vill skapa i listrutan **Typ.** I formuläret visas egenskaperna för den aktuella typen.
5. Fyll i formuläret och klicka på **Skapa** för att spara den nya anslutningen.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Så här skapar du en ny anslutning med Windows PowerShell

Skapa en ny anslutning med `New-AzAutomationConnection` Windows PowerShell med cmdlet. Den här cmdleten `ConnectionFieldValues` har en parameter med namnet som förväntar sig en [hashtable](https://technet.microsoft.com/library/hh847780.aspx) definiera värden för var och en av de egenskaper som definieras av anslutningstypen.

Du kan använda följande exempelkommandon som ett alternativ till att skapa kontot Kör som från portalen för att skapa en ny anslutningstillgång.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Du kan använda skriptet för att skapa anslutningstillgången eftersom det automatiskt innehåller flera globala moduler som `AzureServicePrincipal` standard `AzureRunAsConnection` tillsammans med anslutningstypen för att skapa anslutningstillgången när du skapar ditt Automation-konto. Detta är viktigt att tänka på, för om du försöker skapa en ny anslutningstillgång för att ansluta till en tjänst eller ett program med en annan autentiseringsmetod, misslyckas den eftersom anslutningstypen inte redan har definierats i ditt Automation-konto. Mer information om hur du skapar en egen anslutningstyp för din anpassade eller modul från [PowerShell-galleriet](https://www.powershellgallery.com)finns i [Integrationsmoduler](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Använda en anslutning i en runbook- eller DSC-konfiguration

Hämta en anslutning i en runbook- `Get-AutomationConnection` eller DSC-konfiguration med cmdleten. Du kan inte `Get-AzAutomationConnection` använda aktiviteten. Den här aktiviteten hämtar värdena för de olika fälten i anslutningen och returnerar dem som en [hashtable](https://go.microsoft.com/fwlink/?LinkID=324844). Den här hashtable kan sedan användas med lämpliga kommandon i runbook- eller DSC-konfigurationen.

### <a name="textual-runbook-sample"></a>Exempel på textkörningsbok

Följande exempelkommandon visar hur du använder kontot Kör som nämnts tidigare, för att autentisera med Azure Resource Manager-resurser i din runbook. Den använder anslutningstillgången som representerar kontot Kör som, som refererar till certifikatbaserade tjänstens huvudnamn, inte autentiseringsuppgifter.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> För icke-grafiska `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är alias för [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Du kan använda dessa cmdlets eller så kan du [uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du just har skapat ett nytt Automation-konto.

### <a name="graphical-runbook-samples"></a>Grafiska runbook-exempel

Du lägger till en `Get-AutomationConnection` aktivitet i en grafisk runbook genom att högerklicka på anslutningen i biblioteksfönstret i den grafiska redigeraren och välja Lägg till på arbetsyta **.**

![lägga till i arbetsyta](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder en anslutning i en grafisk runbook. Det här är samma exempel som visas ovan för att autentisera med kontot Kör som med en textkörningsbok. I det `Constant value` här exemplet `Get RunAs Connection` används datauppsättningen för aktiviteten som använder ett anslutningsobjekt för autentisering. En [pipeline-länk](automation-graphical-authoring-intro.md#links-and-workflow) används `ServicePrincipalCertificate` här eftersom parameteruppsättningen förväntar sig ett enda objekt.

![hämta anslutningar](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Exempel på Python 2-runbook

Följande exempel visar hur du autentiserar med hjälp av kör som-anslutningen i en Python 2-runbook.

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
* En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Mer information om Azure Automations användning av PowerShell-moduler och metodtips för att skapa egna PowerShell-moduler för att fungera som integrationsmoduler i Azure Automation finns i [Integrationsmoduler](automation-integration-modules.md).