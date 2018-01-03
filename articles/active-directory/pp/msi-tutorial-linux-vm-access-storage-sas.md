---
title: "Använda en virtuell Linux-MSI för åtkomst till Azure Storage med hjälp av en SAS-autentiseringsuppgifter"
description: "En självstudiekurs som visar hur du använder en Linux VM hanterade tjänsten identitet (MSI) för åtkomst till Azure Storage, med hjälp av en SAS-autentiseringsuppgifter i stället för en åtkomstnyckeln för lagringskontot."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f947d1c0a26a92e6ec179b82ca5015df5e45a634
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Använd en Linux VM hanteras tjänstidentitet för åtkomst till Azure Storage via en SAS-autentiseringsuppgifter

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här kursen visar hur du aktiverar hanterade tjänsten identitet (MSI) för en virtuell Linux-dator och sedan använda MSI för att hämta autentiseringsuppgifterna för delad åtkomst signatur (SAS) en lagring. Mer specifikt en [tjänst-SAS-autentiseringsuppgifter](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

En tjänst-SAS ger möjlighet att ge begränsad åtkomst till objekt i ett lagringskonto för en begränsad tid och en specifik tjänst (i vårt fall blob-tjänsten), utan att exponera en åtkomstnyckel. Du kan använda en SAS-autentiseringsuppgifter som vanligt när gör lagringsåtgärder, till exempel när du använder Storage SDK: N. Den här kursen visar vi överföra och ladda ned en blob med Azure Storage CLI. Du får lära dig hur du:


> [!div class="checklist"]
> * Aktivera MSI på en virtuell Linux-dator 
> * Ge dina VM-åtkomst till ett lagringskonto SAS i Resource Manager 
> * Få en åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att hämta SAS från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Linux-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Linux VM. Du kan också aktivera MSI på en befintlig virtuell dator.

1. Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Ange informationen för den virtuella datorn. För **autentiseringstyp**väljer **offentliga SSH-nyckeln** eller **lösenord**. Autentiseringsuppgifterna som har skapats kan du logga in på den virtuella datorn.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Välj en **prenumeration** för den virtuella datorn i listrutan.
5. Att välja en ny **resursgruppen** du vill att den virtuella datorn ska skapas i, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6. Välj storlek för den virtuella datorn. Om du vill se mer storlekar, Välj **visa alla** eller ändra typ-filter stöds disk. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI kan du få åtkomst-token från Azure AD utan att du behöver publicera autentiseringsuppgifter i koden. Under försättsbladen, aktivera MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och det möjliggör hanterade tjänstidentiteten för den virtuella datorn.  

1. Gå till resursgruppen för den nya virtuella datorn och välj den virtuella dator som du skapade i föregående steg.
2. Under den virtuella datorn ”inställningar” till vänster klickar du på **Configuration**.
3. För att registrera och aktivera MSI-filerna, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera vilka tillägg finns på den virtuella datorn, klickar du på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforLinux** visas i listan.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Om du inte redan har ett kommer du nu skapa ett lagringskonto.  Du kan också hoppa över detta steg och ger dina VM MSI tillgång till nycklarna i ett befintligt lagringskonto. 

1. Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Klicka på **lagring**, sedan **Lagringskonto**, och en ny panel ”skapa storage-konto” visas.
3. Ange en **namn** för storage-konto som du vill använda senare.  
4. **Distributionsmodell** och **konto kind** respektive ska vara inställd på ”Resource manager” och ”generella”. 
5. Se till att den **prenumeration** och **resursgruppen** matchar de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blobbbehållare i storage-konto

Senare kommer att ladda upp och hämta en fil till det nya lagringskontot. Eftersom filer kräver blob-lagring, som vi behöver skapa en blobbbehållare där filen ska sparas.

1. Gå tillbaka till det nyligen skapade lagringskontot.
2. Klicka på den **behållare** länken i den vänstra rutan, under ”Blob-tjänst”.
3. Klicka på **+ behållare** överst på sidan och en ”ny behållare” panelen bilder ut.
4. Namnge behållaren, Välj en åtkomstnivå och klicka sedan på **OK**. Det namn du angav används senare under kursen. 

    ![Skapa lagringsbehållaren](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Ge den Virtuella datorns MSI-åtkomst för att använda en SAS-lagring 

Azure Storage stöder inte Azure AD authentication internt.  Dock kan du använda en MSI för att hämta en SAS-lagring från Resource Manager sedan använda SAS åtkomst till lagring.  I det här steget kan bevilja du VM MSI-åtkomst till ditt lagringskonto SAS.   

1. Gå tillbaka till det nyligen skapade lagringskontot...   
2. Klicka på den **åtkomstkontroll (IAM)** länken i den vänstra panelen.  
3. Klicka på **+ Lägg till** på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ange **roll** till ”Storage-konto bidragsgivare”, till höger på sidan. 
5. I nästa listrutan, ange **bevilja åtkomst till** resursen ”virtuell dator”.  
6. Kontrollera sedan att korrekt prenumeration visas i **prenumeration** och sedan ange **resursgruppen** till ”alla resursgrupper”.  
7. Slutligen under **Välj** Välj din virtuella Linux-dator i listrutan och klicka sedan på **spara**.  

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Få en åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att anropa Azure Resource Manager

Vi kommer att fungera från den virtuella datorn som vi skapade tidigare under resten av kursen.

För att slutföra de här stegen behöver du en SSH-klient. Om du använder Windows, kan du använda SSH-klienten i den [Windows undersystem för Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Om du behöver hjälp konfigurerar SSH-klientens nycklar, se [hur du använder SSH-nycklar med Windows på Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), eller [hur du skapar och använder ett SSH offentliga och privata nyckelpar för Linux virtuella datorer i Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. I Azure-portalen går du till **virtuella datorer**, gå till din Linux virtuella dator, sedan från den **översikt** klickar **Anslut** längst upp. Kopiera strängen som ska ansluta till den virtuella datorn. 
2. Ansluta till den virtuella datorn med hjälp av SSH-klienten.  
3. Därefter uppmanas du att ange i din **lösenord** du har lagt till när du skapar den **Linux VM**. Du bör sedan vara loggat in.  
4. Använd CURL för att hämta ett åtkomsttoken för Azure Resource Manager.  

    CURL förfrågan och svar för åtkomsttoken understiger:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > Värdet för ”resurser”-parametern måste vara en exakt matchning för vad som förväntas av Azure AD i den tidigare begäranden. När du använder Azure Resource Manager resurs-ID, måste du inkluderar det avslutande snedstrecket på URI.
    > I följande svaret access_token elementet som kortats planeringsaspekter.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Hämta en SAS-autentiseringsuppgifter från Azure Resource Manager för att göra anrop för lagring

Använda CURL för att anropa Resource Manager med den åtkomst-token som vi hämta i föregående avsnitt för att skapa en lagring SAS-autentiseringsuppgifter. När vi har SAS-autentiseringsuppgifter kan kallar vi överför/hämta lagringsåtgärder.

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

Parametrarna ingår i själva efter begäran om SAS-autentiseringsuppgifter. Mer information om parametrar för att skapa en SAS-autentiseringsuppgifter finns i [listan Service SAS REST-referens](/rest/api/storagerp/storageaccounts/listservicesas).

Använd följande CURL-begäran för att hämta SAS-autentiseringsuppgifter. Se till att ersätta den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>`, och `<EXPIRATION TIME>` parametervärden med egna värden. Ersätt den `<ACCESS TOKEN>` värde med den åtkomsttoken som du hämtade tidigare:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Texten i den tidigare URL: en är skiftlägeskänsligt, så kontrollera om du använder övre gemena för resursgrupperna återspeglar den därför. Dessutom är det viktigt att veta att det här är en POST-begäran inte en GET-begäran.

Svaret CURL returnerar SAS-autentiseringsuppgifter:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Skapa en exempelfil blob att överföra till blob storage-behållare. Du kan göra detta med följande kommando på en Linux-VM. 

```bash
echo "This is a test file." > test.txt
```

Därefter autentisera med CLI `az storage` kommando med hjälp av SAS-autentiseringsuppgifter och överföra filen till blob-behållaren. För det här steget behöver du [installera den senaste Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på den virtuella datorn, om du inte redan har gjort.

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

Dessutom kan du hämta filen med hjälp av Azure CLI och autentiseras med SAS-autentiseringsuppgifter. 

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

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).
- Mer information om hur du gör detta samma självstudier med en lagringskontonyckel kan visa [använder en Linux VM hanteras tjänstidentitet för åtkomst till Azure Storage](msi-tutorial-linux-vm-access-storage.md)
- Mer information om SAS-funktionen i Azure Storage-konto finns:
  - [Använda signaturer för delad åtkomst (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Hur du skapar en tjänst-SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
