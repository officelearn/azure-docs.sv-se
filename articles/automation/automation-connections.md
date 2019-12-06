---
title: Anslutnings till gångar i Azure Automation
description: Anslutnings till gångar i Azure Automation innehåller den information som krävs för att ansluta till en extern tjänst eller ett program från en Runbook eller DSC-konfiguration. I den här artikeln beskrivs information om anslutningar och hur du arbetar med dem i både text-och grafisk redigering.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 621441afaa9bef08a8ebf3b0af082c6a17c77b1b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850439"
---
# <a name="connection-assets-in-azure-automation"></a>Anslutnings till gångar i Azure Automation

En Automation-anslutning till gång innehåller den information som krävs för att ansluta till en extern tjänst eller ett program från en Runbook eller DSC-konfiguration. Detta kan innehålla information som krävs för autentisering, till exempel användar namn och lösen ord, förutom anslutnings information, till exempel en URL eller en port. Värdet för en anslutning behåller alla egenskaper för att ansluta till ett visst program i en till gång, i stället för att skapa flera variabler. Användaren kan redigera värdena för en anslutning på en plats och du kan skicka namnet på en anslutning till en Runbook-eller DSC-konfiguration i en enda parameter. Egenskaperna för en anslutning kan nås i Runbook-eller DSC-konfigurationen med aktiviteten **Get-AutomationConnection** .

När du skapar en anslutning måste du ange en *Anslutnings typ*. Anslutnings typen är en mall som definierar en uppsättning egenskaper. Anslutningen definierar värden för varje egenskap som definierats i anslutnings typen. Anslutnings typer läggs till Azure Automation i integrerings moduler eller skapas med [Azure Automation-API: et](/previous-versions/azure/reference/mt163818(v=azure.100)) om integrerings modulen innehåller en Anslutnings typ och har importer ATS till ditt Automation-konto. Annars måste du skapa en metadatafil för att ange en typ av Automation-anslutning.  Mer information om detta finns i [integrerings moduler](automation-integration-modules.md).

>[!NOTE]
>Säkra till gångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa till gångar krypteras och lagras i Azure Automation att använda en unik nyckel som genereras för varje Automation-konto. Den här nyckeln lagras i ett systemhanterat Key Vault. Innan du lagrar en säker till gång läses nyckeln in från Key Vault och används sedan för att kryptera till gången. Den här processen hanteras av Azure Automation.

## <a name="connection-types"></a>Anslutningstyper

Det finns tre typer av inbyggda anslutningar som är tillgängliga i Azure Automation:

* **Azure** – den här anslutningen kan användas för att hantera klassiska resurser.
* **AzureClassicCertificate** – den här anslutningen används av **AzureClassicRunAs** -kontot.
* **AzureServicePrincipal** – den här anslutningen används av **AzureRunAs** -kontot.

I de flesta fall behöver du inte skapa en anslutnings resurs eftersom den skapas när du skapar ett Kör som- [konto](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets

Cmdletarna i följande tabell används för att skapa och hantera Automation-anslutningar med Windows PowerShell. De levereras som en del av [Azure PowerShell-modulen](/powershell/azure/overview) som är tillgänglig för användning i Automation-RUNBOOKS och DSC-konfigurationer.

|Cmdlet|Beskrivning|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Hämtar en anslutning. Innehåller en hash-tabell med värdena för anslutningens fält.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Skapar en ny anslutning.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Ta bort en befintlig anslutning.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Anger värdet för ett visst fält för en befintlig anslutning.|

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att få åtkomst till anslutningar i en Runbook-eller DSC-konfiguration.

|Aktiviteter|Beskrivning|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Hämtar en anslutning som ska användas. Returnerar en hash-tabell med egenskaperna för anslutningen.|

>[!NOTE]
>Du bör undvika att använda variabler med parametern – name för **Get-AutomationConnection** eftersom detta kan komplicera identifieringen av beroenden mellan RUNBOOKS och DSC-konfigurationer, samt anslutnings till gångar i design läge.


## <a name="python2-functions"></a>Python2-funktioner
Funktionen i följande tabell används för att få åtkomst till anslutningar i en Python2-Runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_connection | Hämtar en anslutning. Returnerar en ord lista med egenskaperna för anslutningen. |

> [!NOTE]
> Du måste importera modulen "automationassets" längst upp i din python-Runbook för att få åtkomst till till gångs funktionerna.

## <a name="creating-a-new-connection"></a>Skapa en ny anslutning

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Så här skapar du en ny anslutning med Azure Portal

1. Från ditt Automation-konto klickar du på delen **till gångar** för att öppna bladet **till gångar** .
2. Klicka på delen **anslutningar** för att öppna bladet **anslutningar** .
3. Klicka på **Lägg till en anslutning** överst på bladet.
4. I list rutan **typ** väljer du den typ av anslutning som du vill skapa. Formuläret visar egenskaperna för den aktuella typen.
5. Fyll i formuläret och klicka på **skapa** för att spara den nya anslutningen.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Så här skapar du en ny anslutning med Windows PowerShell

Skapa en ny anslutning med Windows PowerShell med cmdleten [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) . Denna cmdlet har en parameter med namnet **ConnectionFieldValues** som förväntar sig en [hash-tabell](https://technet.microsoft.com/library/hh847780.aspx) som definierar värden för var och en av de egenskaper som definieras av anslutnings typen.

Om du är bekant med Automation- [Kör som-kontot](automation-sec-configure-azure-runas-account.md) för att autentisera Runbooks med hjälp av tjänstens huvud namn, skapar PowerShell-skriptet, som ett alternativ till att skapa kör som-kontot från portalen, en ny anslutnings till gång med hjälp av följande exempel kommandon.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Du kan använda skriptet för att skapa anslutnings till gången eftersom när du skapar ett Automation-konto innehåller det automatiskt flera globala moduler som standard tillsammans med anslutnings typen **AzureServicePrincipal** för att skapa **AzureRunAsConnection** -anslutnings till gången.  Detta är viktigt att tänka på, eftersom om du försöker skapa en ny anslutning till gång för att ansluta till en tjänst eller ett program med en annan autentiseringsmetod, Miss lyckas det eftersom anslutnings typen inte redan har definierats i ditt Automation-konto.  Mer information om hur du skapar en egen Anslutnings typ för din anpassade eller modul från [PowerShell-galleriet](https://www.powershellgallery.com)finns i [integrerings moduler](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Använda en anslutning i en Runbook-eller DSC-konfiguration

Du hämtar en anslutning i en Runbook-eller DSC-konfiguration med cmdleten **Get-AutomationConnection** .  Du kan inte använda aktiviteten [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) .  Den här aktiviteten hämtar värdena för de olika fälten i anslutningen och returnerar dem som en hash- [tabell](https://go.microsoft.com/fwlink/?LinkID=324844) som sedan kan användas med lämpliga kommandon i Runbook-eller DSC-konfigurationen.

### <a name="textual-runbook-sample"></a>Text Runbook-exempel

Följande exempel kommandon visar hur du använder det kör som-konto som nämns ovan för att autentisera med Azure Resource Manager resurser i din Runbook.  Den använder anslutnings till gången som representerar kör som-kontot, som refererar till det certifikatbaserade tjänstens huvud namn, inte autentiseringsuppgifter.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRmAccount**. Om du inte ser **Connect-AzureRMAccount**när du söker i biblioteks objekt kan du använda **Add-AzureRMAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

### <a name="graphical-runbook-samples"></a>Grafiska Runbook-exempel

Du lägger till en **Get-AutomationConnection** -aktivitet i en grafisk Runbook genom att högerklicka på anslutningen i fönstret Bibliotek i den grafiska redigeraren och välja **Lägg till på arbets ytan**.

![Lägg till på arbets ytan](media/automation-connections/connection-add-canvas.png)

Följande bild visar ett exempel på hur du använder en anslutning i en grafisk Runbook.  Detta är samma exempel som visas ovan för autentisering med hjälp av kör som-kontot med en text-Runbook.  I det här exemplet används data uppsättningen **konstant** för aktiviteten **Hämta runas-anslutning** som använder ett anslutnings objekt för autentisering.  En [pipeline-länk](automation-graphical-authoring-intro.md#links-and-workflow) används här eftersom parameter uppsättningen serviceprincipalcertificate parameter uppsättning förväntar sig ett enskilt objekt.

![Hämta anslutningar](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 Runbook-exempel
Följande exempel visar hur du autentiserar med hjälp av kör som-anslutningen i en Python2 Runbook.

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

- Mer information om Azure Automation hur du använder PowerShell-moduler och metod tips för att skapa egna PowerShell-moduler för att arbeta som integrations moduler i Azure Automation finns i [integrerings moduler](automation-integration-modules.md).

