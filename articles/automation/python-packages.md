---
title: Hantera Python 2-paket i Azure Automation
description: Den här artikeln beskriver hur du hanterar Python 2-paket i Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5974a8e622ca0969b2a7b5ee9500766ac95398c9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986296"
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