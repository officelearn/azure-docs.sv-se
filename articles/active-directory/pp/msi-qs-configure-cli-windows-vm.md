---
title: "Hur du konfigurerar en Användartilldelad MSI för en virtuell Azure-dator med hjälp av Azure CLI"
description: "Steg för steg-instruktioner för hur du konfigurerar en Användartilldelad hanteras Service identitet (MSI) för en Azure-dator med hjälp av Azure CLI."
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
ms.openlocfilehash: 495ed6daf0d73d89a4bc572f6bccf294cee7decb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Konfigurera en Användartilldelad hanteras Service identitet (MSI) för en virtuell dator med hjälp av Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort en Användartilldelad MSI för en Azure-dator med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Om du vill köra CLI skript exemplen i den här kursen har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) från Azure-portalen, eller via knappen ”prova den” finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen. Logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera Användartilldelad MSI och VM:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Aktivera MSI under skapandet av en Azure VM

Det här avsnittet vägleder dig genom skapandet av den virtuella datorn och tilldelning av Användartilldelad MSI-filerna till den virtuella datorn. Om du redan har en virtuell dator som du vill använda, hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgruppen](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av din MSI med [az gruppen skapa](/cli/azure/group/#az_group_create). Se till att ersätta den `<RESOURCE GROUP>` och `<LOCATION>` parametervärden med egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en Användartilldelad MSI med [az identitet skapa](/cli/azure/identity#az_identity_create).  Den `-g` parametern anger resursgruppen där MSI skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<MSI NAME>` parametervärden med egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Svaret innehåller information för Användartilldelad MSI-filerna skapas liknar följande. Resursen `id` värdet som tilldelas MSI används i följande steg.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator som är associerade med den nya Användartilldelad MSI, som anges av den `--assign-identity` parameter. Se till att ersätta den `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, och `<`MSI-ID >` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id-egenskap som skapats i föregående steg: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Aktivera MSI på en befintlig virtuell Azure-dator

1. Skapa en Användartilldelad MSI med [az identitet skapa](/cli/azure/identity#az_identity_create).  Den `-g` parametern anger resursgruppen där MSI skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<MSI NAME>` parametervärden med egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Svaret innehåller information för Användartilldelad MSI-filerna skapas liknar följande. Resursen `id` värdet som tilldelas MSI används i följande steg.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Tilldela Användartilldelad MSI-filerna till virtuell dator med hjälp av [az tilldela-identitet för](/cli/azure/vm#az_vm_assign_identity). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Den `<MSI ID>` blir Användartilldelad MSI-resurs `id` egenskapen som du skapade i föregående steg:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Ta bort MSI från en virtuell dator i Azure

1. Ta bort Användartilldelad MSI-filerna från virtuell dator med hjälp av [az remove-identitet för](/cli/azure/vm#az_vm_remove_identity). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Den `<MSI NAME>` blir Användartilldelad MSI- `name` egenskapen som anges under den `az identity create` kommando (se exempel i föregående avsnitt):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade tjänstidentiteten](msi-overview.md)
- För fullständig Azure VM skapa Snabbstart, se: 

  - [Skapa en Windows-dator med CLI](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en virtuell Linux-dator med CLI](~/articles/virtual-machines/linux/quick-create-cli.md) 

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
















