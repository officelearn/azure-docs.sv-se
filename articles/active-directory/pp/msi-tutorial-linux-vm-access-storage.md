---
title: Använda en användare som tilldelats MSI på en Linux VM för att få åtkomst till Azure Storage
description: En självstudiekurs som vägleder dig genom processen med att använda en användare tilldelas hanterad tjänstidentitet (MSI) på en Linux VM att få åtkomst till Azure Storage.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4a1a2d0c40012649f6cd89193fd3f704f325e38a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611052"
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Använd en Användartilldelad hanterad tjänstidentitet (MSI) på en Linux VM för att komma åt Azure Storage

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du skapar och använder en Användartilldelad hanterad tjänstidentitet (MSI) från en Linux-dator och sedan använda den för att få åtkomst till Azure Storage. Lär dig att:

> [!div class="checklist"]
> * Skapa en användare som tilldelats hanterad tjänstidentitet (MSI)
> * Tilldela MSI användartilldelade till en Linux-dator
> * Ge MSI åtkomst till en Azure Storage-instans
> * Få ett åtkomsttoken med hjälp av MSI Användartilldelad identitet och använda den för att få åtkomst till Azure Storage

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Om du vill köra CLI-exempelskript i den här självstudien har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) antingen från Azure-portalen eller via knappen ”prova” finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en Linux-dator i en ny resursgrupp.

Först skapar du en ny Linux-VM. Du kan också aktivera MSI på en befintlig virtuell dator om du föredrar.

1. Klicka på den **+/ Skapa ny tjänst** knappen hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentlig SSH-nyckel** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgrupp** som den virtuella datorn ska skapas i, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer **visa alla** eller ändra det typ av filtret som stöds disk. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="create-a-user-assigned-msi"></a>Skapa en Användartilldelad MSI-dator

1. Om du använder CLI-konsolen (i stället för en Azure Cloud Shell-session), börjar du med att logga in på Azure. Använd ett konto som är associerade med Azure-prenumerationen som du vill skapa nya MSI:

    ```azurecli
    az login
    ```

2. Skapa en Användartilldelad MSI med [az identitet skapa](/cli/azure/identity#az_identity_create). Den `-g` parametern anger resursgruppen där MSI skapas, och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<MSI NAME>` parametervärden med dina egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Svaret innehåller information för användartilldelade MSI skapade, ungefär som i följande exempel. Obs den `id` värde för din MSI, som kommer att användas i nästa steg:

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Tilldela din användartilldelade MSI för Linux-VM

Till skillnad från en automatiskt genererad MSI-dator, kan en Användartilldelad MSI-dator användas av klienter på flera Azure-resurser. För den här självstudien får tilldela du den till en enskild virtuell dator. Du kan också tilldela den till mer än en virtuell dator.

Tilldela din Linux-VM med hjälp av MSI användartilldelade [az tilldela-identitet för virtuell](/cli/azure/vm#az-vm-identity-assign). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med dina egna värden. Använd den `id` egenskapen som returneras i föregående steg för den `--identities` parametervärde:

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Om du inte redan har ett kan du nu skapa ett lagringskonto. Du kan även hoppa över detta steg och använda ett befintligt lagringskonto om du föredrar. 

1. Klicka på den **+/ Skapa ny tjänst** knappen hittades på det övre vänstra hörnet i Azure-portalen.
2. Klicka på **Storage**, sedan **Lagringskonto**, och en ny ”skapa storage-konto” panel visar.
3. Ange en **namn** för storage-konto som du använder senare.  
4. **Distributionsmodellen** och **typ av konto** respektive ska vara inställd på ”Resource manager” och ”Allmänt”. 
5. Se till att den **prenumeration** och **resursgrupp** överensstämmer med de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blobbehållare i lagringskontot

Eftersom filer kräver blob-lagring, måste du skapa en blob-behållare där du vill lagra filen. Sedan ladda upp och hämta en fil till blobbehållaren i det nya lagringskontot.

1. Gå tillbaka till din nyligen skapade lagringskontot.
2. Klicka på den **behållare** länken till vänster under ”Blob service”.
3. Klicka på **+ behållare** överst på sidan och en ”ny behållare” panelen skjuts ut.
4. Ge behållaren ett namn, Välj en åtkomstnivå och sedan på **OK**. Det namn du angav används också senare under kursen. 

    ![Skapa lagringsbehållare](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Ladda upp en fil till behållaren för nyligen skapade genom att klicka på behållarens namn, sedan **överför**, sedan välja en fil och sedan på **överför**.

    ![Ladda upp textfil](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Ge din användare tilldelas MSI åtkomst till en Azure Storage-behållare

Med hjälp av en MSI, kan din kod få åtkomsttokens för att autentisera de resurser som har stöd för Azure AD-autentisering. I den här självstudien använder du Azure Storage.

Först beviljar du åtkomst för MSI-identitet till en Azure Storage-behållare. I det här fallet kan du använda den behållare som skapade tidigare. Uppdatera värden för `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, och `<CONTAINER NAME>` som passar din miljö. Dessutom Ersätt `<MSI PRINCIPALID>` med den `principalId` egenskapen som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

Svaret innehåller information om rolltilldelning som skapats:

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

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Få ett åtkomsttoken med hjälp av MSI för Användartilldelad identitet och använda den för att anropa Azure Storage

Under resten av kursen som du behöver arbeta från den virtuella datorn som du skapade tidigare.

För att slutföra de här stegen, måste en SSH-klient. Om du använder Windows kan du använda SSH-klient i den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp att konfigurera nycklar för SSH-klienten kan se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**går du till din Linux-dator, sedan från den **översikt** klickar du på **Connect** högst upp. Kopiera strängen som ska ansluta till den virtuella datorn.
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat. 
3. I terminalfönstret, med CURL, skicka en förfrågan till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure Storage.

   CURL förfrågan om att hämta en åtkomsttoken visas i följande exempel. Se till att ersätta `<CLIENT ID>` med den `clientId` egenskapen som returneras av den `az identity create` i [skapa en Användartilldelad MSI](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > I föregående begäran, värdet för den `resource` parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder Azure Storage resurs-ID, måste du inkluderar det avslutande snedstrecket på URI: N.
   > I följande svar access_token elementet som kortats ned kortfattat.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Använd nu åtkomsttoken för att få åtkomst till Azure Storage, till exempel för att läsa innehållet i exempelfilen som du tidigare har överförts till behållaren. Ersätt värdena för `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, och `<FILE NAME>` med de värden som du angav tidigare, och `<ACCESS TOKEN>` med den token som returneras i föregående steg.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Svaret innehåller innehållet i filen:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Nästa steg

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- Läs hur du gör det här samma självstudier med storage SAS-autentiseringsuppgifter i [använda en Linux VM hanterad tjänstidentitet för åtkomst till Azure Storage via SAS-autentiseringsuppgifter](msi-tutorial-linux-vm-access-storage-sas.md)
- Mer information om SAS-funktionen i Azure Storage-konto finns:
  - [Använda signaturer för delad åtkomst (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Skapa en tjänst-SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.





