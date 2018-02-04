---
title: "Tilldela en Användartilldelad MSI-åtkomst till en Azure-resurs, med hjälp av Azure CLI"
description: "Steg för steg instruktioner för att tilldela en Användartilldelad MSI på en resurs som har åtkomst till en annan resurs, med hjälp av Azure CLI."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5bea41999f59fe8be7ae0a0bd5b726527beeddd5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Tilldela en Användartilldelad hanteras Service identitet (MSI) åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

När du har skapat en Användartilldelad MSI, kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ge Användartilldelad MSI åtkomst till ett Azure storage-konto med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Om du vill köra CLI skript exemplen i den här kursen har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) från Azure-portalen, eller via knappen ”prova den” finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen. Logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera Användartilldelad MSI och VM:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använda RBAC tilldela MSI-åtkomst till en annan resurs

För att kunna använda en MSI med en värd-resurs (till exempel en virtuell dator), för inloggning eller resurs, måste MSI-filerna först beviljas åtkomst till resurser via rolltilldelning. Du kan göra detta innan du kopplar MSI-filerna med värden, eller efter. Fullständiga anvisningar för att skapa och koppla till en virtuell dator finns [konfigurera en Användartilldelad MSI för en virtuell dator med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md).

I följande exempel visas får en Användartilldelad MSI åtkomst till ett lagringskonto.  

1. Om du vill ge MSI-åtkomst måste klient-ID (även kallat app-ID) för MSI-tjänstens huvudnamn. Klient-ID har angetts av [az identitet skapa](/cli/azure/identity#az_identity_create), vid etablering av MSI-filerna och dess tjänstens huvudnamn (visas här som referens):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Ett lyckat svar innehåller klient-ID för användaren som tilldelats MSI-tjänstens huvudnamn, i den `clientId` egenskapen:

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. När klient-ID är känd använda [az rolltilldelning skapa](/cli/azure/role/assignment#az_role_assignment_create) tilldela MSI-åtkomst till en annan resurs. Se till att använda klient-ID för den `--assignee` parametern och matchande prenumerations-ID och resurs gruppen namn som returneras när du kör `az identity create`. Här tilldelas MSI ”Reader” åtkomst till ett lagringskonto som kallas ”myStorageAcct”:

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Ett lyckat svar ser ut ungefär så här:

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>Nästa steg

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).
- För att aktivera en Användartilldelad MSI på en Azure VM, se [konfigurera en Användartilldelad hanteras Service identitet (MSI) för en virtuell dator med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.

