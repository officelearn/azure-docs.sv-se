---
title: Använda en användartilldelad hanterad tjänstidentitet på virtuell Linux-dator för att få åtkomst till Azure Resource Manager
description: En självstudiekurs som beskriver steg för steg hur du använder en användartilldelad hanterad tjänstidentitet på en virtuell Linux-dator för att få åtkomst till Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e4d8dd7201935ef1dbdc83224f905c812f9cca
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447483"
---
# <a name="tutorial-use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Självstudier: Använda en användartilldelad identitet på en virtuell Linux-dator för att få åtkomst till Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien beskriver hur du skapar en användartilldelad identitet, hur du tilldelar den till en virtuell Linux-dator (VM) och hur du sedan använder identiteten för att få åtkomst till Azure Resource Manager-API:et. Hanterade tjänstidentiteter hanteras automatiskt av Azure. Det gör det möjligt att autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver bädda in autentiseringsuppgifter i din kod. 

Hanterade tjänstidentiteter hanteras automatiskt av Azure. Det gör det möjligt att autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver bädda in autentiseringsuppgifter i din kod.

Lär dig att:

> [!div class="checklist"]
> * Skapa en användartilldelad identitet
> * Tilldela den användartilldelade identiteten till en virtuell Linux-dator 
> * Ge den användartilldelade identiteten åtkomst till en resursgrupp i Azure Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den användartilldelade identiteten och använd den för att anropa Azure Resource Manager 

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har arbetat med hanterade tjänstidentiteter tidigare rekommenderar vi att du läser den här [översikten](overview.md). **Gå också igenom [skillnaderna mellan system- och användartilldelade identiteter](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste ha behörigheten ”Ägare” och lämpligt omfång (din prenumeration eller resursgrupp) för att du ska kunna utföra stegen i den här självstudien som beskriver hur du skapar resurser och hanterar roller. Information om rolltilldelning finns i [Använda rollbaserad åtkomstkontroll för att hantera åtkomsten till dina Azure-prenumerationsresurser](/azure/role-based-access-control/role-assignments-portal).

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

I den här självstudien börjar du med att skapa en ny virtuell Linux-dator. Du kan också välja att använda en befintlig virtuell dator.

1. Klicka på **Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **Autentiseringstyp** väljer du **Offentlig SSH-nyckel** eller **Lösenord**. Med autentiseringsuppgifterna som skapats kan du logga in på den virtuella datorn.

    ![Skapa en virtuell Linux-dator](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Välj en ny **resursgrupp** som den virtuella datorn ska skapas i genom att klicka på **Skapa ny**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller ändrar filtret för disktyper som stöds. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

1. Om du använder CLI-konsolen (i stället för en Azure Cloud Shell-session) börjar du med att logga in i Azure. Använd ett konto som är associerat med den Azure-prenumeration som du vill skapa den nya användartilldelade identiteten i:

    ```azurecli
    az login
    ```

2. Skapa en användartilldelad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create). Parametern `-g` anger resursgruppen där den hanterade tjänstidentiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<MSI NAME>` med dina egna värden:
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <MSI NAME>
```

Svaret innehåller information om den användartilldelade identiteten som skapats, liknande den i följande exempel. Notera `id`-värdet för den användartilldelade identiteten. Du behöver det i nästa steg:

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

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>Tilldela en användartilldelad identitet till din virtuella Linux-dator

En användartilldelad identitet kan användas av klienter på flera Azure-resurser. Använd följande kommandon för att tilldela den användartilldelade identiteten till en enskild virtuell dator. Använd egenskapen `Id` som returnerades i föregående steg för `-IdentityID`-parametern.

Tilldela den användartilldelade hanterade tjänstidentiteten till din virtuella Linux-dator med hjälp av [az vm assign-identity](/cli/azure/vm#az-vm-assign-identity). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Använd egenskapen `id` som returnerades i föregående steg för `--identities`-parametervärdet.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Ge den användartilldelade identiteten åtkomst till en resursgrupp i Azure Resource Manager 

Hanterade tjänstidentiteter (MSI) tillhandahåller identiteter som din kod kan använda för att begära åtkomsttoken för autentisering mot resurs-API:er som stöder Azure AD-autentisering. I den här självstudiekursen använder koden Azure Resource Manager-API:et.  

Innan koden kan komma åt API:et måste du ge identiteten åtkomst till en resurs i Azure Resource Manager. I detta fall den resursgrupp som den virtuella datorn finns i. Uppdatera värdet för `<SUBSCRIPTION ID>` och `<RESOURCE GROUP>` baserat på din miljö. Ersätt även `<MSI PRINCIPALID>` med `principalId`-egenskapen som returnerades av kommandot `az identity create` i [Skapa en användartilldelad MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Svaret innehåller information om rolltilldelningen som skapats, liknande den i följande exempel:

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

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Resource Manager 

Under resten av självstudiekursen arbetar vi från den virtuella datorn som vi skapade tidigare.

För att slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till **Virtuella datorer** på portalen, gå till den virtuella Linux-datorn och klicka sedan på **Anslut** under **Översikt**. Kopiera strängen för anslutning till din virtuella dator.
3. Anslut till den virtuella datorn med valfri SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp med att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).
4. Gör ett anrop i terminalfönstret med hjälp av CURL till IMDS-identitetsslutpunkten (Azure Instance Metadata Service) för att hämta en åtkomsttoken för Azure Resource Manager.  

   CURL-begäran om att hämta en åtkomsttoken visas i följande exempel. Ersätt `<CLIENT ID>` med `clientId`-egenskapen som returnerades av kommandot `az identity create` i [Skapa en användartilldelad identitet](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Värdet för parametern `resource` måste vara en exakt matchning av vad som förväntas av Azure AD. När du använder Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n. 
    
    Svaret innehåller den åtkomsttoken som du behöver för att komma åt Azure Resource Manager. 
    
    Svarsexempel:  

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

5. Använd din åtkomsttoken för att komma åt Azure Resource Manager, och läs egenskaperna för den resursgrupp som du tidigare gav den användartilldelade identiteten åtkomst till. Ersätt `<SUBSCRIPTION ID>` och `<RESOURCE GROUP>` med de värden som du angav tidigare, och `<ACCESS TOKEN>` med den token som returnerades i föregående steg.

    > [!NOTE]
    > Eftersom URL:en är skiftlägeskänslig måste du använda exakt samma skiftläge som du använde tidigare när du namngav resursgruppen, samt versalt ”G” i `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Svaret innehåller information om den specifika resursgruppen, liknande den i följande exempel: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en användartilldelad identitet och kopplar den till en virtuell Linux-dator för att komma åt Azure Resource Manager-API:et.  Mer information om Azure Resource Manager finns här:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

