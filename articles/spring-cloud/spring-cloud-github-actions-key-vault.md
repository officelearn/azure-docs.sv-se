---
title: Autentisera Azure Spring Cloud med Key Vault i GitHub-åtgärder
description: Så här använder du nyckelvalv med CI/CD-arbetsflöde för Azure Spring Cloud med GitHub-åtgärder
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945475"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autentisera Azure Spring Cloud med Key Vault i GitHub-åtgärder
Nyckelvalvet är en säker plats att lagra nycklar på. Företagsanvändare måste lagra autentiseringsuppgifter för CI/CD-miljöer i omfattning som de kontrollerar. Nyckeln för att hämta autentiseringsuppgifter i nyckelvalvet bör begränsas till resursomfattning.  Den har endast åtkomst till nyckelvalvets scope, inte hela Azure-scopet. Det är som en nyckel som bara kan öppna en stark låda inte en huvudnyckel som kan öppna alla dörrar i en byggnad. Det är ett sätt att få en nyckel med en annan nyckel, vilket är användbart i ett CICD-arbetsflöde. 

## <a name="generate-credential"></a>Generera autentiseringsuppgifter
Om du vill generera en nyckel för att komma åt nyckelvalvet kör du kommandot nedan på den lokala datorn:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Det scope som `--scopes` anges av parametern begränsar nyckelåtkomsten till resursen.  Den kan bara komma åt den starka lådan.

Med resultat:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Spara sedan resultaten i **GitHub-hemligheter** enligt beskrivningen i [Konfigurera din GitHub-databas och autentisera med Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Lägga till åtkomstprinciper för autentiseringsuppgifterna
Autentiseringsuppgifterna som du skapade ovan kan bara få allmän information om Nyckelvalvet, inte innehållet som lagras.  Om du vill få hemligheter lagrade i Nyckelvalvet behöver du ange åtkomstprinciper för autentiseringsuppgifterna.

Gå till **scope**key **vault-instrumentpanelen** i Azure-portalen, klicka på **kontrollmenyn** **Type** i `This resource` Access och öppna sedan fliken **Rolltilldelningar.** **Apps**  Du bör se autentiseringsuppgifterna som du skapade i föregående steg:

 ![Ange åtkomstprincip](./media/github-actions/key-vault1.png)

Kopiera namn på autentiseringsuppgifter, `azure-cli-2020-01-19-04-39-02`till exempel . Öppna **menyn Åtkomstprinciper** och klicka på **länken Lägg till åtkomstprincip.**  Välj `Secret Management` **mall**och välj sedan **Huvudnamn**. Klistra in autentiseringsnamnet i **indatarutan**/**Huvudval:**

 ![Välj](./media/github-actions/key-vault2.png)

 Klicka på knappen **Lägg till** i dialogrutan Lägg **till åtkomstprincip** och klicka sedan på **Spara**.

## <a name="generate-full-scope-azure-credential"></a>Generera Azure-autentiseringsuppgifter för fullständigt scope
Detta är huvudnyckeln för att öppna alla dörrar i byggnaden. Proceduren liknar föregående steg, men här ändrar vi omfånget för att generera huvudnyckeln:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Återigen, resultat:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Kopiera hela JSON-strängen.  Bo tillbaka till **Key Vault** instrumentpanelen. Öppna **menyn Hemligheter** och klicka sedan på knappen **Generera/importera.** Mata in det hemliga namnet, till exempel `AZURE-CRENDENTIALS-FOR-SPRING`. Klistra in JSON-autentiseringssträngen i indatarutan **Värde.** Du kanske märker att inmatningsrutan för värde är ett textfält med en rad i stället för ett textområde med flera rader.  Du kan klistra in hela JSON strängen där.

 ![Fullständig scopeautentiseringsautentisering](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Kombinera autentiseringsuppgifter i GitHub-åtgärder
Ange de autentiseringsuppgifter som används när CICD-pipelinen körs:

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
* [GitHub-åtgärder för springmoln](./spring-cloud-howto-github-actions.md)