---
title: Använd en Linux VM hanteras identitet för åtkomst till Azure Storage
description: En självstudiekurs som vägleder dig genom processen att använda en System-Assigned hanteras identitet på en Linux-VM för att komma åt Azure Storage.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: 1bd4c422bd952cb6abe2432904adfd9ae9e2fd4f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801712"
---
# <a name="tutorial-use-a-linux-vms-managed-identity-to-access-azure-storage"></a>Självstudier: Använd en Linux VM hanteras identitet för åtkomst till Azure Storage 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Den här kursen visar hur du skapar och använder en Linux VM hanteras identitet för åtkomst till Azure Storage. Lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell Linux-dator i en ny resursgrupp
> * Aktivera hanterade identiteten på en Linux-dator (VM)
> * Skapa en blobbbehållare i ett lagringskonto
> * Bevilja Linux VM hanteras identitet åtkomst till en Azure Storage-behållare
> * Hämta en åtkomst-token och använda den för att anropa Azure Storage

> [!NOTE]
> Azure Active Directory-autentisering för Azure Storage är tillgänglig som förhandsversion.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com) innan du fortsätter.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Om du vill köra CLI skript exemplen i den här kursen har du två alternativ:

- Använd [Azure Cloud Shell](~/articles/cloud-shell/overview.md) från Azure-portalen, eller via den **prova** knapp som finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 eller senare) om du föredrar att använda den lokala CLI-konsolen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

I det här avsnittet skapar du en Linux VM som beviljas senare en hanterad identitet.

1. Välj den **ny** i det övre vänstra hörnet på Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

   ![”Grunderna”-rutan för att skapa en virtuell dator](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. I den **prenumeration** väljer du en prenumeration för den virtuella datorn.
5. Om du vill välja en ny resursgrupp som du vill att den virtuella datorn skapas i **resursgruppen** > **Skapa nytt**. När du är klar väljer **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. I inställningsfönstret behålla standardinställningarna och välj **OK**.

## <a name="enable-managed-identity-on-your-vm"></a>Aktivera hanterade identitet på den virtuella datorn

En identitet för virtuell dator hanteras kan du få åtkomst-token från Azure AD utan att behöva placera autentiseringsuppgifter i din kod. Under försättsbladen, aktivera hanteras identiteten på en virtuell dator via Azure portal gör två saker: registrerar den virtuella datorn med Azure AD för att skapa en hanterad identitet och konfigurerar den identiteten på den virtuella datorn.

1. Gå till resursgruppen för den nya virtuella datorn och välj den virtuella dator som du skapade i föregående steg.
2. Under den **inställningar** kategori, klicka på **Configuration**.
3. Välj för att aktivera hanteras identitet **Ja**.
4. Klicka på **spara** att tillämpa konfigurationen. 

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

I det här avsnittet skapar du ett lagringskonto. 

1. Klicka på den **+ skapa en resurs** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Klicka på **lagring**, sedan **lagringskonto - blob, fil, tabell, kö**.
3. Under **namn**, ange ett namn för lagringskontot.  
4. **Distributionsmodell** och **konto kind** ska anges till **Resource manager** och **lagring (generella v1)**. 
5. Se till att den **prenumeration** och **resursgruppen** matchar de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Skapa en blob-behållare och överföra en fil till lagringskontot

Filerna kräver blobblagring så måste du skapa en blob-behållare där filen ska sparas. Du kan sedan överföra en fil till blob-behållare i det nya lagringskontot.

1. Gå tillbaka till det nyligen skapade lagringskontot.
2. Under **Blob-tjänsten**, klickar du på **behållare**.
3. Klicka på **+ behållare** överst på sidan.
4. Under **ny behållare**, ange ett namn för behållaren och under **offentliga åtkomstnivå** Behåll standardvärdet.

    ![Skapa lagringsbehållaren](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Med hjälp av ett redigeringsprogram, skapa en fil som benämns *hello world.txt* på den lokala datorn.  Öppna filen och Lägg till text (utan citattecken) ”Hello world! :) ”och spara den. 

6. Överföra filen till den nya behållaren genom att klicka på behållarens namn, sedan **överför**
7. I den **överför blob** rutan under **filer**, klicka på mappikonen och bläddra till filen **hello_world.txt** på den lokala datorn, markera filen och klicka på **Överför**.

    ![Överför textfil](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Ge dina VM-åtkomst till en Azure Storage-behållare 

Du kan använda hanterade identiteten för den virtuella datorn för att hämta data i Azure storage blob.   

1. Gå tillbaka till det nyligen skapade lagringskontot.  
2. Klicka på den **åtkomstkontroll (IAM)** länken i den vänstra panelen.  
3. Klicka på **+ Lägg till** på sidan för att lägga till en ny rolltilldelning för den virtuella datorn.
4. Under **rollen**, i listrutan, Välj **Blob för lagring-dataläsare (förhandsgranskning)**. 
5. I nästa listrutan under **bevilja åtkomst till**, Välj **virtuella**.  
6. Kontrollera sedan att korrekt prenumeration visas i **prenumeration** listrutan och ange sedan **resursgruppen** till **alla resursgrupper**.  
7. Under **Välj**, Välj den virtuella datorn och klicka sedan på **spara**.

    ![Tilldela behörigheter](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Hämta en åtkomst-token och använda den för att anropa Azure Storage

Azure Storage inbyggt stöd för Azure AD-autentisering, så den kan acceptera åtkomsttoken direkt hämtas med hjälp av en hanterad identitet. Detta är en del av Azure Storage-integrering med Azure AD och skiljer sig från att tillhandahålla autentiseringsuppgifter i anslutningssträngen.

Slutför följande steg du behöver arbeta från den virtuella datorn skapades tidigare och du behöver en SSH-klient kan ansluta till den. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**, gå till din Linux virtuella dator, sedan från den **översikt** klickar **Anslut**. Kopiera strängen som ska ansluta till den virtuella datorn.
2. **Ansluta** till den virtuella datorn med SSH-klient av önskat slag. 
3. I fönstret terminal med CURL, gör en begäran till den lokala slutpunkten hanteras identitet att hämta en åtkomst-token för Azure Storage.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Nu använda åtkomsttoken att få åtkomst till Azure Storage, till exempel för att läsa innehållet i exempelfilen som du tidigare har överförts till behållaren. Ersätt värdena för `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, och `<FILE NAME>` med de värden du angav tidigare, och `<ACCESS TOKEN>` med den token som returneras i föregående steg.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Svaret innehåller innehållet i filen:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig hur aktivera en hanterad identitet för åtkomst till Azure Storage med Linux-dator.  Om du vill veta mer om Azure Storage, se:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
