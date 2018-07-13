---
title: Använd en virtuell MSI-dator för Linux för att få åtkomst till Azure Storage med SAS-autentiseringsuppgifter
description: En självstudie som visar hur du använder en Linux VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Storage, med hjälp av SAS-autentiseringsuppgifter i stället för en åtkomstnyckel för lagringskontot.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59e74ee586a28935b5fde4b5c1089264e98e5087
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003103"
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Använda en Linux VM hanterad tjänstidentitet för åtkomst till Azure Storage via SAS-autentiseringsuppgifter

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en Linux-dator och sedan använda MSI för att hämta autentiseringsuppgift för signatur för delad åtkomst (SAS) storage. Mer specifikt en [SAS för tjänst för autentiseringsuppgift](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

En SAS för tjänst ger möjlighet att bevilja begränsad åtkomst till objekt i ett lagringskonto för en begränsad tid och en specifik tjänst (i vårt fall blob service), utan att exponera en åtkomstnyckel. Du kan använda SAS-autentiseringsuppgifter som vanligt vid lagringsåtgärder, till exempel när du använder Storage-SDK. Den här självstudien visar vi överföra och ladda ned en blob med hjälp av Azure Storage CLI. Du får lära dig hur du:


> [!div class="checklist"]
> * Aktivera MSI på en Linux-dator 
> * Ge din VM-åtkomst till ett lagringskonto SAS i Resource Manager 
> * Få ett åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att hämta SAS från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en Linux-dator i en ny resursgrupp.

I den här självstudiekursen skapar vi en ny Linux-VM. Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på den **+/ Skapa ny tjänst** knappen hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentlig SSH-nyckel** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgrupp** som den virtuella datorn ska skapas i, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer **visa alla** eller ändra det typ av filtret som stöds disk. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI-dator kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att hanterade tjänstidentiteten för den virtuella datorn.  

1. Navigera till resursgruppen för den nya virtuella datorn och välj den virtuella dator som du skapade i föregående steg.
2. Under den virtuella datorn ”inställningar” till vänster klickar du på **Configuration**.
3. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera vilka tillägg som finns på den virtuella datorn, klicka på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforLinux** visas i listan.

    ![ALT bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Om du inte redan har en, kommer du nu skapa ett lagringskonto.  Du kan även hoppa över detta steg och ge din VM MSI åtkomst till nycklarna i ett befintligt lagringskonto. 

1. Klicka på den **+/ Skapa ny tjänst** knappen hittades på det övre vänstra hörnet i Azure-portalen.
2. Klicka på **Storage**, sedan **Lagringskonto**, och en ny panel ”skapa storage-konto” visas.
3. Ange en **namn** för storage-konto som du ska använda senare.  
4. **Distributionsmodellen** och **typ av konto** respektive ska vara inställd på ”Resource manager” och ”Allmänt”. 
5. Se till att den **prenumeration** och **resursgrupp** överensstämmer med de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blobbehållare i lagringskontot

Senare kommer vi ladda upp och hämta en fil till det nya lagringskontot. Eftersom filer kräver blob-lagring, som vi behöver skapa en blob-behållare där du vill lagra filen.

1. Gå tillbaka till din nyligen skapade lagringskontot.
2. Klicka på den **behållare** länken i den vänstra panelen under ”Blob service”.
3. Klicka på **+ behållare** överst på sidan och en ”ny behållare” panelen skjuts ut.
4. Ge behållaren ett namn, Välj en åtkomstnivå och sedan på **OK**. Det namn du angav används senare under kursen. 

    ![Skapa lagringsbehållare](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Ge den Virtuella datorns MSI åtkomst för att använda en SAS-lagring 

Azure Storage stöder inte internt Azure AD-autentisering.  Du kan dock använda en MSI för att hämta en SAS-lagring från Resource Manager och sedan använda SAS åtkomst till lagring.  I det här steget ska ge du din VM MSI åtkomst till ditt lagringskonto SAS.   

1. Gå tillbaka till din nyligen skapade lagringskontot...   
2. Klicka på den **åtkomstkontroll (IAM)** länken i den vänstra panelen.  
3. Klicka på **+ Lägg till** på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ange **rollen** till ”Lagringskontodeltagare”, till höger på sidan. 
5. I nästa listrutan ange **tilldela åtkomst till** resursen ”virtuell dator”.  
6. Kontrollera sedan att korrekt prenumeration visas i **prenumeration** listrutan ange **resursgrupp** till ”alla resursgrupper”.  
7. Slutligen går **Välj** väljer din Linux-dator i listrutan och klickar sedan på **spara**.  

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Få ett åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att anropa Azure Resource Manager

Under resten av kursen arbetar vi från den virtuella datorn som vi skapade tidigare.

För att slutföra de här stegen, måste en SSH-klient. Om du använder Windows kan du använda SSH-klient i den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Om du behöver hjälp att konfigurera nycklar för SSH-klienten kan se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**går du till din Linux-dator, sedan från den **översikt** klickar du på **Connect** högst upp. Kopiera strängen som ska ansluta till den virtuella datorn. 
2. Anslut till den virtuella datorn med SSH-klienten.  
3. Därefter uppmanas du att ange i din **lösenord** du har lagt till när du skapar den **Linux VM**. Du bör sedan vara har loggat in.  
4. Använda CURL för att hämta en åtkomsttoken för Azure Resource Manager.  

    CURL begäran och svaret för åtkomsttoken understiger:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > I föregående begäran, måste värdet för ”resource”-parametern vara en exakt matchning för vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID, måste du inkluderar det avslutande snedstrecket på URI: N.
    > I följande svar access_token elementet som kortats ned kortfattat.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Hämta SAS-autentiseringsuppgifter från Azure Resource Manager för att göra storage-anrop

Nu Använd vi CURL för att anropa Resource Manager med den åtkomsttoken som vi hämtade i föregående avsnitt, för att skapa SAS-autentiseringsuppgifter lagring. När vi har SAS-autentiseringsuppgifter, kan du anropa uppladdning/nedladdning lagringsåtgärder.

För denna begäran använder vi parametrarna Följ HTTP-begäran för att skapa SAS-autentiseringsuppgifter:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

De här parametrarna ingår i INLÄGGET brödtexten i begäran för SAS-autentiseringsuppgifter. Mer information om parametrar för att skapa SAS-autentiseringsuppgifter finns i den [lista Service SAS REST-referens](/rest/api/storagerp/storageaccounts/listservicesas).

Använd följande CURL-begäran för att hämta SAS-autentiseringsuppgifter. Se till att ersätta den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>`, och `<EXPIRATION TIME>` parametervärden med dina egna värden. Ersätt den `<ACCESS TOKEN>` värde med den åtkomsttoken som du hämtade tidigare:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Texten i den tidigare URL: en är skiftlägeskänsligt, så att om du använder övre-gemener för dina resursgrupper för att återspegla det i enlighet med detta. Dessutom är det viktigt att veta att det här är en POST-begäran inte en GET-begäran.

CURL svaret returnerar SAS-autentiseringsuppgifter:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Skapa en blob-exempelfil att ladda upp till blob storage-behållare. Du kan göra detta med följande kommando på en Linux-VM. 

```bash
echo "This is a test file." > test.txt
```

Nu ska autentisera med CLI `az storage` kommando med hjälp av SAS-autentiseringsuppgifter och överföra filen till blob-behållaren. Det här steget behöver du [installerar den senaste Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på den virtuella datorn, om du inte redan har gjort.

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Svar: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Dessutom kan du ladda ned filen med hjälp av Azure CLI och autentisering med SAS-autentiseringsuppgifter. 

Begäran: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Svar: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Nästa steg

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- Lär dig hur du gör det här samma självstudier med hjälp av en lagringskontonyckel, se [använda en Linux VM hanterad tjänstidentitet för åtkomst till Azure Storage](msi-tutorial-linux-vm-access-storage.md)
- Mer information om SAS-funktionen i Azure Storage-konto finns:
  - [Använda signaturer för delad åtkomst (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Skapa en tjänst-SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.
