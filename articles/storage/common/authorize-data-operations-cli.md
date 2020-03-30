---
title: Auktorisera åtkomst till blob- eller ködata med Azure CLI
titleSuffix: Azure Storage
description: Ange hur dataåtgärder ska auktoriseras mot blob- eller ködata med Azure CLI. Du kan auktorisera dataåtgärder med Azure AD-autentiseringsuppgifter, med kontoåtkomstnyckeln eller med en SAS-token (Shared Access Signature).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207698"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Auktorisera åtkomst till blob- eller ködata med Azure CLI

Azure Storage innehåller tillägg för Azure CLI som gör att du kan ange hur du vill auktorisera åtgärder på blob- eller ködata. Du kan auktorisera dataåtgärder på följande sätt:

- Med ett Azure Active Directory -säkerhetsobjekt (Azure AD). Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter för överlägsen säkerhet och användarvänlighet.
- Med kontoåtkomstnyckeln eller en SAS-token (Shared Access Signature).

## <a name="specify-how-data-operations-are-authorized"></a>Ange hur dataåtgärder ska auktoriseras

Azure CLI-kommandon för att läsa och skriva `--auth-mode` blob- och ködata innehåller den valfria parametern. Ange den här parametern om du vill ange hur en dataåtgärd ska auktoriseras:

- Ange `--auth-mode` parametern `login` för att logga in med ett Azure AD-säkerhetsobjekt (rekommenderas).
- Ange `--auth-mode` parametern till `key` det äldre värdet för att försöka hämta kontoåtkomstnyckeln som ska användas för auktorisering. Om du utelämnar parametern `--auth-mode` försöker Azure CLI också hämta åtkomstnyckeln.

Om du `--auth-mode` vill använda parametern kontrollerar du att du har installerat Azure CLI version 2.0.46 eller senare. Kör `az --version` för att kontrollera den installerade versionen.

> [!IMPORTANT]
> Om du utelämnar parametern `--auth-mode` `key`eller ställer in den på försöker Azure CLI använda kontoåtkomstnyckeln för auktorisering. I det här fallet rekommenderar Microsoft att du anger åtkomstnyckeln antingen i kommandot eller i **variabeln AZURE_STORAGE_KEY** miljö. Mer information om miljövariabler finns i avsnittet [Ange miljövariabler för auktoriseringsparametrar](#set-environment-variables-for-authorization-parameters).
>
> Om du inte anger åtkomstnyckeln försöker Azure CLI ringa Azure Storage-resursprovidern för att hämta den för varje åtgärd. Om du utför många dataåtgärder som kräver ett anrop till resursprovidern kan det leda till begränsning. Mer information om resursprovidergränser finns i [Skalbarhets- och prestandamål för Azure Storage-resursleverantören](scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Auktorisera med Azure AD-autentiseringsuppgifter

När du loggar in på Azure CLI med Azure AD-autentiseringsuppgifter returneras en OAuth 2.0-åtkomsttoken. Den token används automatiskt av Azure CLI för att auktorisera efterföljande dataåtgärder mot Blob- eller Queue storage. För åtgärder som stöds behöver du inte längre skicka en kontonyckel eller SAS-token med kommandot.

Du kan tilldela behörigheter till blob- och ködata till ett Azure AD-säkerhetsobjekt via rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC-roller i Azure Storage finns i [Hantera åtkomsträttigheter till Azure Storage-data med RBAC](storage-auth-aad-rbac.md).

### <a name="permissions-for-calling-data-operations"></a>Behörigheter för att anropa dataåtgärder

Azure Storage-tilläggen stöds för åtgärder på blob- och ködata. Vilka åtgärder du kan anropa beror på vilka behörigheter som beviljas Azure AD-säkerhetsobjektet som du loggar in på Azure CLI. Behörigheter till Azure Storage-behållare eller köer tilldelas via RBAC. Om du till exempel har tilldelats rollen **Blob Data Reader** kan du köra skriptkommandon som läser data från en behållare eller kö. Om du har tilldelats rollen **Blob Data Contributor** kan du köra skriptkommandon som läser, skriver eller tar bort en behållare eller kö eller de data de innehåller.

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd i en behållare eller kö finns i [Anropa lagringsåtgärder med OAuth-token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Exempel: Auktorisera en åtgärd för att skapa en behållare med Azure AD-autentiseringsuppgifter

I följande exempel visas hur du skapar en behållare från Azure CLI med dina Azure AD-autentiseringsuppgifter. För att skapa behållaren måste du logga in på Azure CLI och du behöver en resursgrupp och ett lagringskonto. Mer information om hur du skapar dessa resurser finns i [Snabbstart: Skapa, hämta och lista blobbar med Azure CLI](../blobs/storage-quickstart-blobs-cli.md).

1. Innan du skapar behållaren tilldelar du rollen [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. Även om du är kontoägare behöver du explicit behörighet för att utföra dataåtgärder mot lagringskontot. Mer information om hur du tilldelar RBAC-roller finns i [Bevilja åtkomst till Azure-blob och ködata med RBAC i Azure-portalen](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC-rolltilldelningar kan ta några minuter att sprida.

1. Anropa kommandot [az storage container create](/cli/azure/storage/container#az-storage-container-create) med `--auth-mode` parametern inställd på `login` att skapa behållaren med dina Azure AD-autentiseringsuppgifter. Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Auktorisera med kontoåtkomstnyckeln

Om du har kontonyckeln kan du anropa alla Azure Storage-dataåtgärder. I allmänhet är det mindre säkert att använda kontonyckeln. Om kontonyckeln komprometteras kan alla data i ditt konto komprometteras.

I följande exempel visas hur du skapar en behållare med hjälp av kontoåtkomstnyckeln. Ange kontonyckeln och `--auth-mode` ange parametern `key` med värdet:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Auktorisera med en SAS-token

Om du har en SAS-token kan du anropa dataåtgärder som är tillåtna av SAS. I följande exempel visas hur du skapar en behållare med en SAS-token:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ange miljövariabler för auktoriseringsparametrar

Du kan ange auktoriseringsparametrar i miljövariabler för att undvika att inkludera dem i varje anrop till en Azure Storage-dataåtgärd. I följande tabell beskrivs tillgängliga miljövariabler.

| Miljövariabel                  | Beskrivning                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Namnet på lagringskontot. Den här variabeln ska användas tillsammans med antingen lagringskontonyckeln eller en SAS-token. Om det inte finns någon av dem försöker Azure CLI hämta åtkomstnyckeln för lagringskontot med hjälp av det autentiserade Azure AD-kontot. Om ett stort antal kommandon körs samtidigt kan begränsningsgränsen för Azure Storage-resursprovidern nås. Mer information om resursprovidergränser finns i [Skalbarhets- och prestandamål för Azure Storage-resursleverantören](scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Nyckeln till lagringskontot. Den här variabeln måste användas tillsammans med lagringskontonamnet.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    En anslutningssträng som innehåller lagringskontonyckeln eller en SAS-token. Den här variabeln måste användas tillsammans med lagringskontonamnet.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    En SAS-token (Shared Access Signature). Den här variabeln måste användas tillsammans med lagringskontonamnet.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Auktoriseringsläget som kommandot ska köras med. Tillåtna `login` värden är `key`(rekommenderas) eller . Om du `login`anger använder Azure CLI dina Azure AD-autentiseringsuppgifter för att auktorisera dataåtgärden. Om du anger `key` det äldre läget försöker Azure CLI att fråga efter kontoåtkomstnyckeln och att auktorisera kommandot med nyckeln.    |

## <a name="next-steps"></a>Nästa steg

- [Använd Azure CLI för att tilldela en RBAC-roll för åtkomst till blob- och ködata](storage-auth-aad-rbac-cli.md)
- [Auktorisera åtkomst till blob- och ködata med hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)
