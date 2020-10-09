---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512828"
---
Nu ska vi skapa en hemlighet **som kallas hemlig**, med värdet **lyckades!**. En hemlighet kan vara ett lösen ord, en SQL-anslutningssträng eller annan information som du behöver för att hålla både säkra och tillgängliga för ditt program. 

Om du vill lägga till en hemlighet i det nya nyckel valvet använder du kommandot Azure CLI [AZ Key Vault Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```