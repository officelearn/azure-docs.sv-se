---
title: Hantera Python 2-paket i Azure Automation
description: Den här artikeln beskriver hur du hanterar Python 2-paket i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 81ce9cb2667ce9f21d7c18a92e417e47768d7efb
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407960"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Hantera Python 2-paket i Azure Automation

Azure Automation kan du köra Python 2-runbookflöden på Azure och på Linux Hybrid Runbook Worker. Du kan använda Python-paket för att importera de moduler som du behöver för att hjälpa i förenkling av distribution av runbooks. Den här artikeln beskriver hur du hanterar och använder Python-paket i Azure Automation.

## <a name="import-packages"></a>Importera paket

I ditt Automation-konto väljer **Python 2-paket** under **delade resurser**. Klicka på **+ Lägg till ett Python 2-paket**.

![Lägg till Python-paketet](media/python-packages/add-python-package.png)

På den **lägga till Python 2-paketet** väljer du ett lokala paket att ladda upp. Paketet kan vara en `.whl` fil eller `.tar.gz` fil. När du har valt, klickar du på **OK** att ladda upp paketet.

![Lägg till Python-paketet](media/python-packages/upload-package.png)

När ett paket har importerats kan det visas på den **Python 2-paket** sida i ditt Automation-konto. Om du vill ta bort ett paket väljer du paketet och väljer **ta bort** på sidan package.

![Paketlistan](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importera paket med beroenden

Azure automation inte går att lösa beroenden för python-paket under importen. Det finns två sätt att importera ett paket med alla dess beroenden. Endast en av följande steg måste användas för att importera paket till ditt Automation-konto.

### <a name="manually-download"></a>Hämta manuellt

På Windows 64-bitars dator med [python2.7](https://www.python.org/downloads/release/latest/python2) och [pip](https://pip.pypa.io/en/stable/) installerad, kör följande kommando för att ladda ned ett paket och alla dess beroenden:

```
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

När paket som har hämtats, kan du importera dem till ditt automation-konto.

### <a name="runbook"></a>Runbook

Importera python-runbook [Import Python 2-paket från pypi i Azure Automation-konto](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) från galleriet i ditt Automation-konto. Se till att köra-inställningar är inställda på **Azure** och starta en runbook med parametrar. Runbook kräver ett kör som-konto för Automation-kontot ska fungera. För varje parameter se till att inleder du du det med växeln som visas i följande lista och avbildning:

* -s \<prenumerations-ID\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![Paketlistan](media/python-packages/import-python-runbook.png)

Runbook kan du ange vilket paket för att hämta, till exempel `Azure` (fjärde parameter) laddar ned alla Azure-moduler och alla dess beroenden, vilket är ungefär 105.

När runbooken har slutförts kan du kontrollera den **Python 2-paket** sidan **delade resurser** i ditt Automation-konto för att verifiera att de paketet har importerats korrekt.

## <a name="use-a-package-in-a-runbook"></a>Använda ett paket i en runbook

När du har importerat ett paket, kan du nu använda den i en runbook. I följande exempel används den [ Azure Automation-verktyget paketet](https://github.com/azureautomation/azure_automation_utility). Det här paketet gör det enklare att använda Python med Azure Automation. Använd paketet, följ instruktionerna i GitHub-lagringsplatsen och lägga till den i en runbook med hjälp av `from azure_automation_utility import get_automation_runas_credential` till exempel för att importera funktionen för att hämta kör som-konto.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Utveckla och testa runbooks offline

För att utveckla och testa ditt Python 2-runbookflöden offline, du kan använda den [Azure Automation python emuleras tillgångar](https://github.com/azureautomation/python_emulated_assets) modulen på GitHub. Den här modulen kan du referera till dina delade resurser, till exempel autentiseringsuppgifter, variabler, anslutningar och certifikat.

## <a name="next-steps"></a>Nästa steg

Kom igång med Python 2-runbooks, se [min första Python 2-runbook](automation-first-runbook-textual-python2.md)
