---
title: Hantera anslutningar i Azure Automation
description: Anslutnings till gångar i Azure Automation innehåller den information som krävs för att ansluta till en extern tjänst eller ett program från en Runbook eller DSC-konfiguration. I den här artikeln beskrivs information om anslutningar och hur du arbetar med dem i både text-och grafisk redigering.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097123"
---
# <a name="manage-connections-in-azure-automation"></a>Hantera anslutningar i Azure Automation

En Automation-anslutning till gång innehåller den information som krävs för att ansluta till en extern tjänst eller ett program från en Runbook eller DSC-konfiguration. Detta kan innehålla information som krävs för autentisering, till exempel användar namn och lösen ord, förutom anslutnings information, till exempel en URL eller en port. Värdet för en anslutning behåller alla egenskaper för att ansluta till ett visst program i en till gång, i stället för att skapa flera variabler. Användaren kan redigera värdena för en anslutning på en plats och du kan skicka namnet på en anslutning till en Runbook-eller DSC-konfiguration i en enda parameter. Egenskaperna för en anslutning kan nås i Runbook-eller DSC-konfigurationen med `Get-AutomationConnection` aktiviteten.

När du skapar en anslutning måste du ange en Anslutnings typ. Anslutnings typen är en mall som definierar en uppsättning egenskaper. Anslutningen definierar värden för varje egenskap som definierats i anslutnings typen. Anslutnings typer läggs till Azure Automation i integrerings moduler eller skapas med [Azure Automation-API: et](/previous-versions/azure/reference/mt163818(v=azure.100)) om integrerings modulen innehåller en Anslutnings typ och har importer ATS till ditt Automation-konto. Annars måste du skapa en metadatafil för att ange en typ av Automation-anslutning. Mer information om detta finns i [integrerings moduler](automation-integration-modules.md).

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i ett systemhanterat Key Vault. Innan du lagrar en säker till gång läses nyckeln in från Key Vault och används sedan för att kryptera till gången. Den här processen hanteras av Azure Automation.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="connection-types"></a>Anslutningstyper

Det finns tre typer av inbyggda anslutningar i Azure Automation:

* **Azure** – den här anslutningen kan användas för att hantera klassiska resurser.
* **AzureClassicCertificate** – den här anslutningen används av **AzureClassicRunAs** -kontot.
* **AzureServicePrincipal** – den här anslutningen används av **AzureRunAs** -kontot.

I de flesta fall behöver du inte skapa en anslutnings resurs eftersom den skapas när du skapar ett [Kör som-konto](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-cmdlet:ar

Cmdletarna i följande tabell används för att skapa och hantera Automation-anslutningar med Windows PowerShell. De levereras som en del av [Azure PowerShell-modulen](/powershell/azure/overview), som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

|Cmdlet|Beskrivning|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Hämtar en anslutning. Innehåller en hash med värdena för anslutnings fälten.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Skapar en ny anslutning.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Tar bort en befintlig anslutning.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Ställer in värdet för ett visst fält för en befintlig anslutning.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att få åtkomst till anslutningar i en Runbook-eller DSC-konfiguration.

|Aktiviteter|Beskrivning|
|---|---|
|`Get-AutomationConnection` | Hämtar en anslutning som ska användas. Returnerar en hash med egenskaperna för anslutningen.|

>[!NOTE]
>Undvik att använda variabler med `Name` -parametern `Get-AutomationConnection`. Användningen av den här parametern kan komplicera identifieringen av beroenden mellan Runbooks och DSC-konfigurationer och anslutnings till gångar i design läge.

## <a name="python-2-functions"></a>Python 2-funktioner

Funktionen i följande tabell används för att få åtkomst till anslutningar i en python 2-Runbook.

| Funktion | Beskrivning |
|:---|:---|
| `automationassets.get_automation_connection` | Hämtar en anslutning. Returnerar en ord lista med egenskaperna för anslutningen. |

> [!NOTE]
> Du måste importera `automationassets` modulen överst i python-runbooken för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-connection"></a>Skapa en ny anslutning

### <a name="create-a-new-connection-with-the-azure-portal"></a>Skapa en ny anslutning med Azure Portal

1. Från ditt Automation-konto klickar du på delen **till gångar** för att öppna bladet **till gångar** .
2. Klicka på delen **anslutningar** för att öppna bladet **anslutningar** .
3. Klicka på **Lägg till en anslutning** överst på bladet.
4. I list rutan **typ** väljer du den typ av anslutning som du vill skapa. Formuläret visar egenskaperna för den aktuella typen.
5. Fyll i formuläret och klicka på **skapa** för att spara den nya anslutningen.

### <a name="create-a-new-connection-with-windows-powershell"></a>Skapa en ny anslutning med Windows PowerShell

Skapa en ny anslutning med Windows PowerShell med hjälp `New-AzAutomationConnection` av cmdleten. Denna cmdlet har en parameter med `ConnectionFieldValues` namnet som förväntar sig en [hash](https://technet.microsoft.com/library/hh847780.aspx) -variabel som definierar värden för var och en av de egenskaper som definieras av anslutnings typen.

Du kan använda följande exempel kommandon som ett alternativ till att skapa ett Kör som-konto från portalen för att skapa en ny anslutning till gång.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

När du skapar ett Automation-konto innehåller det flera globala moduler som standard, tillsammans med anslutnings typen `AzureServicePrincipal` för att skapa `AzureRunAsConnection` anslutnings till gången. Om du försöker skapa en ny anslutning till gång för att ansluta till en tjänst eller ett program med en annan autentiseringsmetod, Miss lyckas åtgärden eftersom anslutnings typen inte redan har definierats i ditt Automation-konto. Mer information om hur du skapar en egen Anslutnings typ för en anpassad [PowerShell-galleriet](https://www.powershellgallery.com) -modul finns i [integrerings moduler](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Använda en anslutning i en Runbook-eller DSC-konfiguration

Hämta en anslutning i en Runbook-eller DSC-konfiguration `Get-AutomationConnection` med cmdleten. Du kan inte använda `Get-AzAutomationConnection` aktiviteten. Den här aktiviteten hämtar värdena för de olika fälten i anslutningen och returnerar dem som en [hash](https://go.microsoft.com/fwlink/?LinkID=324844)-tabellen. Den här hash-tabellen kan sedan användas med lämpliga kommandon i Runbook-eller DSC-konfigurationen.

### <a name="textual-runbook-sample"></a>Text Runbook-exempel

Följande exempel kommandon visar hur du använder det kör som-konto som nämns ovan för att autentisera med Azure Resource Manager resurser i din Runbook. Den använder anslutnings till gången som representerar kör som-kontot, som refererar till det certifikatbaserade tjänstens huvud namn, inte autentiseringsuppgifter.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> För icke-grafiska PowerShell-Runbooks `Add-AzAccount` och `Add-AzureRMAccount` är alias för [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Du kan använda dessa cmdletar, eller så kan du [Uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

### <a name="graphical-runbook-samples"></a>Grafiska Runbook-exempel

Du lägger till `Get-AutomationConnection` en aktivitet i en grafisk Runbook genom att högerklicka på anslutningen i fönstret Bibliotek i den grafiska redigeraren och välja **Lägg till på arbets ytan**.

![Lägg till på arbets ytan](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder en anslutning i en grafisk Runbook. Detta är samma exempel som ovan för autentisering med hjälp av kör som-kontot med en text-Runbook. I `Constant value` det här exemplet används data uppsättningen för `Get RunAs Connection` den aktivitet som använder ett anslutnings objekt för autentisering. En [pipeline-länk](automation-graphical-authoring-intro.md#links-and-workflow) används här eftersom `ServicePrincipalCertificate` parameter uppsättningen förväntar sig ett enskilt objekt.

![Hämta anslutningar](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Exempel på python 2-Runbook

Följande exempel visar hur du autentiserar med hjälp av kör som-anslutningen i en python 2-Runbook.

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

- Granska [länkarna i grafisk redigering](automation-graphical-authoring-intro.md#links-and-workflow) och lär dig hur du dirigerar och styr flödet av logik i dina runbooks.
* En PowerShell-cmdlet-referens finns i [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Mer information om Azure Automation hur du använder PowerShell-moduler och metod tips för att skapa egna PowerShell-moduler för att arbeta som integrations moduler i Azure Automation finns i [integrerings moduler](automation-integration-modules.md).