---
title: Använd en Användartilldelad Linux VM-MSI för åtkomst till Azure Resource Manager
description: En självstudiekurs som vägleder dig genom processen att använda en User-Assigned hanteras Service identitet (MSI) på en Linux-VM för att komma åt Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6d4f7378ccd24af4281793dbc93df40830a1b31a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Självstudier: Använda en tilldelad användaridentitet på en Linux-VM för att komma åt Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudiekursen beskrivs hur du skapar en användare som tilldelats identitet, tilldela den en Linux virtuella dator (VM) och sedan använda identitet för åtkomst till Azure Resource Manager API. Hanterade Tjänsteidentiteter hanteras automatiskt av Azure. De aktivera autentisering för tjänster som stöder Azure AD-autentisering utan att behöva bädda in autentiseringsuppgifter i din kod. 

Hanterade Tjänsteidentiteter hanteras automatiskt av Azure. De aktivera autentisering för tjänster som stöder Azure AD-autentisering utan att behöva bädda in autentiseringsuppgifter i din kod.

Lär dig att:

> [!div class="checklist"]
> * Skapa en användare som tilldelats identitet
> * Tilldela användaren som har tilldelats identitet till en Linux-VM 
> * Ge åtkomst för användaren som har tilldelats identitet till en resursgrupp i Azure Resource Manager 
> * Få en åtkomsttoken med hjälp av användaren som har tilldelats identitet och använda den för att anropa Azure Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

- Om din är bekant med hanterade tjänstidentiteten checka ut den [översikt](overview.md) avsnitt. **Se till att granska den [skillnaderna mellan system- och tilldelade identiteter](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Skapa en nödvändig resurs och rollen hanteringsåtgärder utföra i den här självstudiekursen, måste ditt konto ”ägare” behörighet för definitionsområdet lämpliga (arbetsgruppen prenumerationen eller resursen). Om du behöver hjälp med rolltilldelningen finns [Use Role-Based behörighet att hantera åtkomst till resurserna i Azure-prenumeration](/azure/role-based-access-control/role-assignments-portal).

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen kommer skapa du först en ny Linux VM. Du kan också välja för att använda en befintlig virtuell dator.

1. Klicka på **skapar du en resurs** på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![Skapa virtuell Linux-dator](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgruppen** du vill att den virtuella datorn ska skapas i, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se mer storlekar, Välj **visa alla** eller ändra typ-filter stöds disk. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="create-a-user-assigned-identity"></a>Skapa en användare som tilldelats identitet

1. Om du använder CLI-konsolen (i stället för en Azure-molnet Shell-session), starta genom att logga in till Azure. Använd ett konto som är associerade med Azure-prenumeration som du vill skapa den nya användaren som tilldelats identitet:

    ```azurecli
    az login
    ```

2. Skapa en Användartilldelad identitet med hjälp av [az identitet skapa](/cli/azure/identity#az_identity_create). Den `-g` parametern anger resursgruppen där MSI skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<MSI NAME>` parametervärden med egna värden:
    
    > [!IMPORTANT]
    > Skapa tilldelade användaridentiteter stöder endast alfanumeriska och bindestreck (0-9 eller a-z eller A-Z eller -) tecken. Namnet bör dessutom begränsas till 24 tecken för tilldelning till VM/VMSS ska fungera korrekt. Sök igen efter uppdateringar. Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Svaret innehåller information om användaren som har tilldelats identitet skapas, liknande exemplet nedan. Observera den `id` värdet för användaren som har tilldelats-identitet som den ska användas i nästa steg:

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

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>Tilldela en användare som tilldelats identitet för Linux-VM

En användare som tilldelats identitet kan användas av klienter på Azure-resurser. Använd följande kommandon för att tilldela användaridentitet tilldelad till en enda virtuell dator. Använd den `Id` egenskapen returneras i föregående steg för den `-IdentityID` parameter.

Tilldela din Linux VM som använder Användartilldelad MSI [az tilldela-identitet för](/cli/azure/vm#az_vm_assign_identity). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Använd den `id` egenskapen returneras i föregående steg för den `--identities` parametervärdet.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Ge din identitet åtkomst för användaren som har tilldelats till en resursgrupp i Azure Resource Manager 

Hanterad Service identitet (MSI) ger identiteter som din kod kan använda för att begära åtkomst-token för att autentisera till resurs-API: er som stöder Azure AD-autentisering. I den här självstudiekursen kommer koden åtkomst till Azure Resource Manager API.  

Innan din kod kan komma åt API: et, måste du bevilja åtkomst till en resurs i Azure Resource Manager identitet. I det här fallet resursgruppen där den virtuella datorn finns. Uppdatera värdet för `<SUBSCRIPTION ID>` och `<RESOURCE GROUP>` som passar din miljö. Dessutom ersätta `<MSI PRINCIPALID>` med den `principalId` egenskap som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi):

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

Du behöver en SSH-klient för att slutföra de här stegen. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Logga in på Azure [portal](https://portal.azure.com).
2. I portalen, går du till **virtuella datorer** och gå till den virtuella Linux-datorn och i den **översikt**, klickar du på **Anslut**. Kopiera strängen som ska ansluta till den virtuella datorn.
3. Ansluta till den virtuella datorn med SSH-klient av önskat slag. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. I fönstret terminal med CURL, gör en begäran till slutpunkten Azure instans Metadata Service (IMDS) identitet att hämta ett åtkomsttoken för Azure Resource Manager.  

   CURL begäran om att få en åtkomsttoken som visas i följande exempel. Se till att ersätta `<CLIENT ID>` med den `clientId` egenskap som returneras av den `az identity create` i [skapar du en Användartilldelad identitet](#create-a-user-assigned-msi): 
    
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

5. Använda åtkomst-token för åtkomst till Azure Resource Manager och läsa egenskaper för resursgruppen som du tidigare åtkomst din användartilldelade identitet. Se till att ersätta `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` med de värden du angav tidigare, och `<ACCESS TOKEN>` med den token som returneras i föregående steg.

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

- En översikt över hanterade tjänstidentiteten finns [översikt](overview.md).

