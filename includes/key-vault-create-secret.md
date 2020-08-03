---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512828"
---
Nu ska vi skapa en hemlighet **som kallas hemlig**, med värdet **lyckades!**. En hemlighet kan vara ett lösen ord, en SQL-anslutningssträng eller annan information som du behöver för att hålla både säkra och tillgängliga för ditt program. 

Om du vill lägga till en hemlighet i det nya nyckel valvet använder du kommandot Azure CLI [AZ Key Vault Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```