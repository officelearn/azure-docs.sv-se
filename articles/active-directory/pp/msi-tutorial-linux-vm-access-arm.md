---
title: "Använd en Användartilldelad Linux VM-MSI för åtkomst till Azure Resource Manager"
description: "En självstudiekurs som vägleder dig genom processen att använda en User-Assigned hanteras Service identitet (MSI) på en Linux-VM för att komma åt Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2b6d70e441dc3d300f49adff1c02d7cc65788d2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Använd en Användartilldelad hanteras Service identitet (MSI) på en Linux-VM för att komma åt Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudiekursen beskrivs hur du skapar en Användartilldelad hanteras Service identitet (MSI), tilldelar den en Linux virtuella dator (VM) och sedan använda identitet för åtkomst till Azure Resource Manager API. 

Hanterade Tjänsteidentiteter hanteras automatiskt av Azure. De aktivera autentisering för tjänster som stöder Azure AD-autentisering utan att behöva bädda in autentiseringsuppgifter i din kod.

Lär dig att:

> [!div class="checklist"]
> * Skapa en Användartilldelad MSI
> * Tilldela MSI-filerna till en virtuell Linux-dator 
> * Bevilja MSI-åtkomst till en resursgrupp i Azure Resource Manager 
> * Få en åtkomsttoken med hjälp av MSI-filerna och använda den för att anropa Azure Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Om du vill köra CLI skript exemplen i den här kursen har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) från Azure-portalen, eller via knappen ”prova den” finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen kommer skapa du först en ny Linux VM. Du kan också välja för att använda en befintlig virtuell dator.

1. Klicka på **skapar du en resurs** på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![Skapa virtuell Linux-dator](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgruppen** du vill att den virtuella datorn ska skapas i, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se mer storlekar, Välj **visa alla** eller ändra typ-filter stöds disk. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="create-a-user-assigned-msi"></a>Skapa en Användartilldelad MSI

1. Om du använder CLI-konsolen (i stället för en Azure-molnet Shell-session), starta genom att logga in till Azure. Använd ett konto som är associerade med Azure-prenumeration som du vill skapa nya MSI:

    ```azurecli
    az login
    ```

2. Skapa en Användartilldelad MSI med [az identitet skapa](/cli/azure/identity#az_identity_create). Den `-g` parametern anger resursgruppen där MSI skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<MSI NAME>` parametervärden med egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Svaret innehåller information för Användartilldelad MSI-filerna skapas, liknande exemplet nedan. Observera den `id` värdet för en MSI som kommer att användas i nästa steg:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Tilldela din Användartilldelad MSI för Linux-VM

Till skillnad från en automatiskt genererad MSI kan en Användartilldelad MSI användas av klienter på Azure-resurser. Den här kursen kan tilldela du den till en enda virtuell dator. Du kan också tilldela den till mer än en virtuell dator.

Tilldela din Linux VM som använder Användartilldelad MSI [az tilldela-identitet för](/cli/azure/vm#az_vm_assign_identity). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Använd den `id` egenskapen returneras i föregående steg för den `--identities` parametervärdet:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Ge din Användartilldelad MSI-åtkomst till en resursgrupp i Azure Resource Manager 

MSI innehåller koden med en åtkomst-token för att autentisera till resurs-API: er som stöder Azure AD-autentisering. I den här självstudiekursen koden har åtkomst till Azure Resource Manager API. 

Innan din kod kan dock åtkomst till API, måste du bevilja MSI-identitet åtkomst till en resurs i Azure Resource Manager. I det här fallet resursgruppen där den virtuella datorn finns. Uppdatera värdena för `<SUBSCRIPTION ID>` och `<RESOURCE GROUP>` som passar din miljö. Dessutom ersätta `<MSI PRINCIPALID>` med den `principalId` egenskap som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Svaret innehåller information om rolltilldelning skapas, liknande exemplet nedan:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Få en åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att anropa Resource Manager 

Vi kommer att fungera från den virtuella datorn som vi skapade tidigare under resten av kursen.

Du behöver en SSH-klient för att slutföra de här stegen. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**, gå till din Linux virtuella dator, sedan från den **översikt** klickar **Anslut** längst upp. Kopiera strängen som ska ansluta till den virtuella datorn.
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat slag.  
3. I fönstret terminal med CURL, gör en begäran till den lokala MSI-slutpunkten för att hämta ett åtkomsttoken för Azure Resource Manager.  

   CURL begäran om att få en åtkomsttoken som visas i följande exempel. Se till att ersätta `<CLIENT ID>` med den `clientId` egenskap som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Värdet för den `resource` parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder Resource Manager resurs-ID, måste du inkluderar det avslutande snedstrecket på URI. 
    
    Svaret innehåller den åtkomst-token som du behöver åtkomst till Azure Resource Manager. 
    
    Svaret exempel:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. Nu använder den åtkomst-token för åtkomst till Azure Resource Manager och läsa egenskaper för resursgruppen som du tidigare har beviljats din Användartilldelad MSI-åtkomst. Se till att ersätta `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` med de värden du angav tidigare, och `<ACCESS TOKEN>` med den token som returneras i föregående steg.

    > [!NOTE]
    > URL: en är skiftlägeskänsligt, så du används på exakt samma sätt som du använde tidigare när du namnet på resursgruppen och versaler ”G” i `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Svaret innehåller den specifika resursgruppen informationen liknar följande exempel: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Nästa steg

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
