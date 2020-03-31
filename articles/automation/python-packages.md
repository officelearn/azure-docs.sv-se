---
title: Hantera Python 2-paket i Azure Automation
description: I den här artikeln beskrivs hur du hanterar Python 2-paket i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 05d892edf20cda228bc566b30b0b693ea7c4a184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417651"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Hantera Python 2-paket i Azure Automation

Med Azure Automation kan du köra Python 2-runbooks på Azure och på Linux Hybrid Runbook Workers. För att förenkla runbooks kan du använda Python-paket för att importera de moduler som du behöver. I den här artikeln beskrivs hur du hanterar och använder Python-paket i Azure Automation.

## <a name="import-packages"></a>Importera paket

I ditt Automation-konto väljer du **Python 2-paket** under **Delade resurser**. Klicka på **+ Lägg till ett Python 2-paket**.

![Lägg till Python-paket](media/python-packages/add-python-package.png)

På sidan **Lägg till Python 2-paket** väljer du ett lokalt paket att ladda upp. Paketet kan vara `.whl` en `.tar.gz` fil eller fil. När du väljer det här alternativet klickar du på **OK** för att ladda upp paketet.

![Lägg till Python-paket](media/python-packages/upload-package.png)

När ett paket har importerats visas det på sidan **Python 2-paket** i ditt Automation-konto. Om du behöver ta bort ett paket markerar du paketet och väljer **Ta bort** på paketsidan.

![Paketlista](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Importera paket med beroenden

Azure automation löser inte beroenden för python-paket under importprocessen. Det finns två sätt att importera ett paket med alla dess beroenden. Endast ett av följande steg behöver användas för att importera paketen till ditt Automation-konto.

### <a name="manually-download"></a>Ladda ner manuellt

På en Windows 64-bitarsdator med [python2.7](https://www.python.org/downloads/release/latest/python2) och [pip](https://pip.pypa.io/en/stable/) installerat kör du följande kommando för att hämta ett paket och alla dess beroenden:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

När paketen har hämtats kan du importera dem till ditt automationskonto.

### <a name="runbook"></a>Runbook

Importera python runbook [Import Python 2-paket från pypi till Azure Automation-konto](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) från galleriet till ditt Automation-konto. Kontrollera att körinställningarna är inställda på **Azure** och starta runbook med parametrarna. Runbook kräver ett Run As-konto för att Automation-kontot ska fungera. För varje parameter se till att du startar den med växeln som visas i följande lista och bild:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -ett \<automationskonto\>
* -m \<modulPaket\>

![Paketlista](media/python-packages/import-python-runbook.png)

Med runbooken kan du ange vilket paket `Azure` som ska hämtas ,till exempel (den fjärde parametern) ska hämta alla Azure-moduler och alla dess beroenden, vilket är ca 105.

När runbooken är klar kan du kontrollera sidan **Python 2-paket** under **Delade resurser** i ditt Automation-konto för att kontrollera att paketet importerades korrekt.

## <a name="use-a-package-in-a-runbook"></a>Använda ett paket i en runbook

När du har importerat ett paket kan du nu använda det i en runbook. I följande exempel används [Azure Automation-verktygspaketet](https://github.com/azureautomation/azure_automation_utility). Det här paketet gör det enklare att använda Python med Azure Automation. Om du vill använda paketet följer du instruktionerna i GitHub-databasen `from azure_automation_utility import get_automation_runas_credential` och lägger till det i runbooken genom att till exempel importera funktionen för att hämta RunAs-kontot.

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

Om du vill utveckla och testa dina Python 2-runbooks offline kan du använda [Azure Automation python-modulen för emulerade tillgångar](https://github.com/azureautomation/python_emulated_assets) på GitHub. Med den här modulen kan du referera till delade resurser som autentiseringsuppgifter, variabler, anslutningar och certifikat.

## <a name="next-steps"></a>Nästa steg

För att komma igång med Python 2 runbooks, se [Min första Python 2 runbook](automation-first-runbook-textual-python2.md)
