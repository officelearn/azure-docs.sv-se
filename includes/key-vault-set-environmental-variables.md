---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013136"
---
DefaultAzureCredential-metoden i programmet är beroende av tre miljövariabler: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` och `AZURE_TENANT_ID` . Ange dessa variabler för de clientId-, clientSecret-och tenantId-värden som returnerades i steget "skapa ett tjänst huvud namn" med hjälp av `export VARNAME=VALUE` formatet. (Den här metoden anger bara variablerna för ditt aktuella gränssnitt och processer som skapats från gränssnittet. om du vill lägga till dessa variabler permanent i miljön redigerar du `/etc/environment ` filen.) 

Du måste också spara nyckel valvets namn som en miljö variabel som kallas `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
