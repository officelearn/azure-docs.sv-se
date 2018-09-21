---
title: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Storage.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: daveba
ms.openlocfilehash: 05e054dad28faf5dfba5ed8ac63bbe851c62afe4
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159970"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att komma åt Azure Storage. Lär dig att:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapa en blobcontainer i ett lagringskonto
> * Ge en hanterade identitet på en virtuell Linux-dator åtkomst till en Azure Storage-container
> * Hämta en åtkomsttoken och använda den för att anropa Azure Storage

> [!NOTE]
> Azure Active Directory-autentisering för Azure Storage finns i en allmänt tillgänglig förhandsversion.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Logga in på Azure-portalen](https://portal.azure.com)

- [Skapa en virtuell Linux-dator](/azure/virtual-machines/linux/quick-create-portal)

- [Aktivera systemtilldelade hanterade identiteter på din virtuella dator](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

Om du vill köra CLI-exempelskripten i den här självstudien har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) antingen från Azure Portal eller via knappen **Prova**, som du hittar i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda en lokal CLI-konsol.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

I det här avsnittet skapar du ett lagringskonto. 

1. Klicka på knappen **+ Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Klicka på **Lagring** och sedan på **Lagringskonto – blob, fil, tabell, kö**.
3. Under **Namn** anger du ett namn för lagringskontot.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på **Resurshanterare** respektive **Lagring (generell användning v1)**. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa ett nytt lagringskonto](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Skapa en blobcontainer och ladda upp en fil till lagringskontot

Eftersom filer behöver bloblagring måste du skapa en blobcontainer som du lagrar filen i. Sedan laddar du upp en fil till blobcontainern i det nya lagringskontot.

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Under **Blob Service** klickar du på **Containers**.
3. Klicka på **+ Container** överst på sidan.
4. Under **Ny container**anger du ett namn för containern och under **Offentlig åtkomstnivå** behåller du standardvärdet.

    ![Skapa lagringscontainer](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Med hjälp av valfritt redigeringsprogram skapar du en fil som heter *hello world.txt* på den lokala datorn.  Öppna filen och lägg till följande text (utan citattecken): ”Hello world! :)”och spara den. 

6. Ladda upp filen till containern du skapade genom att klicka på containernamnet och sedan på **Ladda upp**
7. Under **Filer** i fönstret **Ladda upp blob** klickar du på mappikonen och bläddrar till filen **hello_world.txt** på din lokala dator. Välj sedan filen och klicka på **Ladda upp**.

    ![Ladda upp textfil](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Ge din virtuella dator åtkomst till en Azure Storage-container 

Du kan använda den hanterade identiteten på den virtuella datorn till att hämta data i Azure Storage Blob.   

1. Gå tillbaka till det lagringskonto du nyss skapade.  
2. Klicka på länken **Åtkomstkontroll (IAM)** i vänstra fönstret.  
3. Klicka på **+ Lägg till** högst upp på sidan om du vill lägga till en ny rolltilldelning för din virtuella dator.
4. Välj **Storage Blob Data-läsare (förhandsgranskning)** i listrutan under **Roll**. 
5. I listrutan under **Tilldela behörighet till** väljer du **Virtuell dator**.  
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan **Prenumeration**. Under **Resursgrupper** väljer du **Alla resursgrupper**.  
7. Under **Välj** väljer du din virtuella dator och klickar sedan på **Spara**.

    ![Tilldela behörigheter](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Hämta en åtkomsttoken och anropa Azure Storage med den

Azure Storage har inbyggt stöd för Azure Active Directory-autentisering, vilket gör att åtkomsttoken som hämtas med en hanterad identitet kan accepteras direkt. Detta är en del av Azure Storages integrering med Azure AD, och skiljer sig från att ange autentiseringsuppgifter i anslutningssträngen.

För att kunna utföra följande steg måste du arbeta från den virtuella datorn som du skapade tidigare och du behöver en SSH-klient för att ansluta till den. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Använda SSH-nycklar med Windows i Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).

1. Gå till **Virtuella datorer** i Azure-portalen och sedan till den virtuella Linux-datorn. På sidan **Överblick** klickar du på **Anslut**. Kopiera strängen för att ansluta till din virtuella dator.
2. **Anslut** till den virtuella datorn med valfri SSH-klient. 
3. I terminalfönstret skickar du en begäran till den lokala slutpunkten för hanterad identitet för att hämta en åtkomsttoken för Azure Storage.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Nu använder du åtkomsttoken för att få åtkomst till Azure Storage, till exempel för att läsa innehåll i exempelfilen som du tidigare laddade upp till containern. Ersätt värdena för `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` och `<FILE NAME>` med de värden som du angav tidigare, och `<ACCESS TOKEN>` med den token som returnerades i föregående steg.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Svaret innehåller filens innehåll:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Storage.  Läs mer om Azure Storage här:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
