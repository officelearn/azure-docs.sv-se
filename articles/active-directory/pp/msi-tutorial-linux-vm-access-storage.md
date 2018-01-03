---
title: "Använda en användare som tilldelats MSI på en Linux-VM för åtkomst till Azure Storage"
description: "En självstudiekurs som vägleder dig genom processen att använda en användaren tilldelas hanterade tjänsten identitet (MSI) på en Linux-VM för att komma åt Azure Storage."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c02eb32fd12e28d79dcc0340811f4ced48ed4be
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Använd en Användartilldelad hanteras Service identitet (MSI) på en Linux-VM för att få åtkomst till Azure Storage

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här kursen visar hur du skapar och använder en Användartilldelad hanteras Service identitet (MSI) från en virtuell Linux-dator och sedan använda den för att få åtkomst till Azure Storage. Lär dig att:

> [!div class="checklist"]
> * Skapa en användare som tilldelats hanterade tjänstidentiteten (MSI)
> * Tilldela Användartilldelad MSI-filerna till en virtuell Linux-dator
> * Ge MSI-åtkomst till en Azure Storage-instans
> * Få en åtkomsttoken som använder Användartilldelad MSI identitet och använda den för att få åtkomst till Azure Storage

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Om du vill köra CLI skript exemplen i den här kursen har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) från Azure-portalen, eller via knappen ”prova den” finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Först skapar du en ny Linux VM. Du kan också aktivera MSI på en befintlig virtuell dator om du föredrar.

1. Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
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
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Om du inte redan har ett kan du nu skapa ett lagringskonto. Du kan också hoppa över detta steg och använda ett befintligt lagringskonto om du föredrar. 

1. Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Klicka på **lagring**, sedan **Lagringskonto**, och en ny panel ”skapa storage-konto” visas.
3. Ange en **namn** för storage-konto som du använder senare.  
4. **Distributionsmodell** och **konto kind** respektive ska vara inställd på ”Resource manager” och ”generella”. 
5. Se till att den **prenumeration** och **resursgruppen** matchar de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blobbbehållare i storage-konto

Eftersom filer kräver blob-lagring, måste du skapa en blob-behållare där filen ska sparas. Sedan ladda upp och hämta en fil till blob-behållaren i det nya lagringskontot.

1. Gå tillbaka till det nyligen skapade lagringskontot.
2. Klicka på den **behållare** länken till vänster under ”Blob-tjänst”.
3. Klicka på **+ behållare** överst på sidan och en ”ny behållare” panelen bilder ut.
4. Namnge behållaren, Välj en åtkomstnivå och klicka sedan på **OK**. Det namn du angav används också senare under kursen. 

    ![Skapa lagringsbehållaren](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Överför en fil till behållaren för nyskapade genom att klicka på behållarens namn, sedan **överför**, sedan välja en fil och sedan på **överför**.

    ![Överför textfil](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Ge din användare tilldelade MSI-åtkomst till en Azure Storage-behållare

Med hjälp av en MSI hämta koden åtkomsttoken att autentisera till resurser som stöder Azure AD-autentisering. I den här kursen använder du Azure Storage.

Först bevilja MSI identitet åtkomst till en Azure Storage-behållare. I det här fallet kan du använda den behållare som skapats tidigare. Uppdatera värdena för `<MSI CLIENTID>`, `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, och `<CONTAINER NAME>` som passar din miljö. Ersätt `<CLIENT ID>` med den `clientId` egenskap som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI CLIENTID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/<CONTAINER NAME>"
```

Svaret innehåller information för rolltilldelning skapas:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Få en åtkomsttoken som använder Användartilldelad MSI-identitet och använda den för att anropa Azure Storage

Du måste arbeta från den virtuella datorn som du skapade tidigare under resten av kursen.

Du behöver en SSH-klient för att slutföra de här stegen. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**, gå till din Linux virtuella dator, sedan från den **översikt** klickar **Anslut** längst upp. Kopiera strängen som ska ansluta till den virtuella datorn.
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat slag. 
3. I fönstret terminal med CURL, gör en begäran till den lokala MSI-slutpunkten för att hämta en åtkomst-token för Azure Storage.

   CURL begäran om att få en åtkomsttoken som visas i följande exempel. Se till att ersätta `<CLIENT ID>` med den `clientId` egenskap som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi):

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > I föregående begäran, värdet för den `resource` parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder Azure Storage-resurs-ID, måste du inkluderar det avslutande snedstrecket på URI.
   > I följande svaret access_token elementet som kortats planeringsaspekter.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Nu använda åtkomsttoken att få åtkomst till Azure Storage, till exempel för att läsa innehållet i exempelfilen som du tidigare har överförts till behållaren. Ersätt värdena för `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, och `<FILE NAME>` med de värden du angav tidigare, och `<ACCESS TOKEN>` med den token som returneras i föregående steg.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Svaret innehåller innehållet i filen:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Nästa steg

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).
- Information om hur du gör detta samma självstudier med lagring SAS-autentiseringsuppgifter finns [använder en Linux VM hanteras tjänstidentitet för åtkomst till Azure Storage via en SAS-autentiseringsuppgifter](msi-tutorial-linux-vm-access-storage-sas.md)
- Mer information om SAS-funktionen i Azure Storage-konto finns:
  - [Använda signaturer för delad åtkomst (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Hur du skapar en tjänst-SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.





