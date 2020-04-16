---
title: 'Snabbstart: Ange och hämta ett certifikat från Azure Key Vault'
description: Snabbstart som visar hur du anger och hämtar ett certifikat från Azure Key Vault med Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: d8107d26405423da6bb5d85ab79b83edce95d179
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423728"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Snabbstart: Ange och hämta ett certifikat från Azure Key Vault med Azure CLI

I den här snabbstarten skapar du ett nyckelvalv i Azure Key Vault med Azure CLI. Azure Key Vault är en molntjänst som fungerar som säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Om du vill ha mer information om Key Vault kan du läsa [översikten](../general/overview.md). Med Azure-CLI:t kan du skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det kommer du att lagra ett certifikat.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI:t lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggningsalternativen via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *ContosoResourceGroup* på platsen *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

Därefter skapar du ett nyckelvalv i resursgruppen du skapade i föregående steg. Du måste ange en del information:

- I den här snabbstarten använder vi **Contoso-vault2**. Du måste ange ett unikt namn när du testar funktionen.
- Resursgruppnamnet **ContosoResourceGroup**.
- Platsen är **USA, östra**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

- **Valvnamn**: I det här exemplet är namnet **Contoso-vault2**. Du kommer att använda det här namnet i andra Key Vault-kommandon.
- **Valvets URI**: I det här exemplet är det https://contoso-vault2.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-certificate-to-key-vault"></a>Lägga till ett certifikat i Key Vault

Om du vill lägga till ett certifikat i valvet behöver du bara vidta några ytterligare åtgärder. Det här certifikatet kan användas av ett program. 

Skriv kommandona nedan om du vill skapa ett självsignerat certifikat med standardprincipen **ExampleCertificate:**

```azurecli
az keyvault certificate create --vault-name "Contoso-Vault2" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Du kan nu referera till det här certifikatet som du har lagt till i Azure Key Vault med hjälp av dess URI. Används **https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate** för att hämta den aktuella versionen. 

Så här visar du tidigare lagrat certifikat:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "Contoso-Vault2"
```

Nu har du skapat ett Nyckelvalv, lagrat ett certifikat och hämtat det.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre kan du använda kommandot [az group delete](/cli/azure/group) till att ta bort resursgruppen och alla relaterade resurser. Så här tar du bort resurserna:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Nyckelvalv och sparade ett certifikat i det. Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure CLI az keyvault-kommandon](/cli/azure/keyvault?view=azure-cli-latest)
- Granska [metodtips för Azure Key Vault](../general/best-practices.md)
