---
title: Autentisera Azure våren Cloud med Key Vault i GitHub-åtgärder
description: Så här använder du Key Vault med CI/CD-arbetsflöde för Azure våren Cloud med GitHub-åtgärder
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: efe8c1a2726054c54934926f652e338797d4efa1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776552"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autentisera Azure våren Cloud med Key Vault i GitHub-åtgärder
Key Vault är en säker plats för att lagra nycklar. Företags användare måste lagra autentiseringsuppgifter för CI/CD-miljöer i den omfattning som de styr. Nyckeln för att hämta autentiseringsuppgifter i nyckel valvet bör begränsas till resurs omfånget.  Den har enbart åtkomst till nyckel valvets omfång, inte hela Azure-omfånget. Det är som en nyckel som bara kan öppna en stark ruta, inte en huvud nyckel som kan öppna alla dörrar i en byggnad. Det är ett sätt att hämta en nyckel till en annan nyckel, vilket är användbart i ett CICD-arbetsflöde. 

## <a name="generate-credential"></a>Generera autentiseringsuppgifter
Om du vill generera en nyckel för att komma åt nyckel valvet, kör du kommandot nedan på den lokala datorn:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Det omfång som anges av parametern `--scopes` begränsar den viktiga åtkomsten till resursen.  Den kan bara komma åt den starka rutan.

Med resultat:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Spara sedan resultaten till GitHub- **hemligheter** enligt beskrivningen i [Konfigurera din GitHub-lagringsplats och autentisera med Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Lägg till åtkomst principer för autentiseringsuppgiften
De autentiseringsuppgifter som du har skapat ovan kan bara få allmän information om Key Vault, inte innehållet som den lagrar.  Om du vill hämta hemligheterna som lagras i Key Vault måste du ange åtkomst principer för autentiseringsuppgifterna.

Gå till **Key Vault** instrument panelen i Azure Portal, klicka på menyn **åtkomst kontroll** och öppna sedan fliken **roll tilldelningar** . Välj **appar** för **typ** och `This resource` för **omfång**.  Du bör se de autentiseringsuppgifter som du skapade i föregående steg:

 ![Ange åtkomst princip](./media/github-actions/key-vault1.png)

Kopiera namnet på autentiseringsuppgiften, till exempel `azure-cli-2020-01-19-04-39-02`. Öppna menyn **åtkomst principer** , klicka på länken **Lägg till åtkomst princip** .  Välj `Secret Management` för **mall**och välj sedan **huvud konto**. Klistra in namnet på autentiseringsuppgiften i **huvud**/**Välj** inmatad ruta:

 ![Välj](./media/github-actions/key-vault2.png)

 Klicka på knappen **Lägg till** i dialog rutan **Lägg till åtkomst princip** och klicka sedan på **Spara**.

## <a name="generate-full-scope-azure-credential"></a>Generera Azure-autentiseringsuppgift med fullständig omfattning
Detta är huvud nyckeln för att öppna alla dörrar i byggnaden. Proceduren liknar föregående steg, men här ändrar vi omfattningen för att generera huvud nyckeln:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Igen, resultat:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Kopiera hela JSON-strängen.  Bo tillbaka till **Key Vault** instrument panel. Öppna **hemligheter** -menyn och klicka på knappen **generera/importera** . Mata in det hemliga namnet, t. ex. `AZURE-CRENDENTIALS-FOR-SPRING`. Klistra in JSON-autentiseringsuppgiften i rutan **värde** inmatare. Du kanske märker att indata-rutan är ett textfält med en rad, i stället för ett text område med flera rader.  Du kan klistra in hela JSON-strängen där.

 ![Fullständiga autentiseringsuppgifter för omfattning](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Kombinera autentiseringsuppgifter i GitHub-åtgärder
Ange de autentiseringsuppgifter som ska användas när CICD-pipeline körs:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Nästa steg
* [GitHub åtgärder för våren Cloud](./spring-cloud-howto-github-actions.md)