---
title: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure Storage.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: ee26db80e3219c170c77a2bd90f7d1416676333e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352758"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Självstudier: Använda en systemtilldelade hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage via åtkomstnyckel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig att hämta åtkomstnycklar till lagringskonton med en systemtilldelad hanterad identitet för en virtuell Linux-dator. Du kan använda en lagringsåtkomstnyckel som vanligt när du gör lagringsåtgärder, till exempel när du använder Storage SDK. I den här självstudien laddar vi upp och ned blobar med hjälp av Azure CLI. Du lär dig hur du:

> [!div class="checklist"]
> * Ger den virtuella datorn åtkomst till åtkomstnycklar för lagringskonton i Resource Manager 
> * Få en åtkomsttoken med hjälp av den virtuella datorns identitet, och använda den och hämta lagringsåtkomstnycklarna från Resource Manager  

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Nu skapar du ett lagringskonto, om du inte redan har ett.  Du kan även hoppa över det här steget och ge den virtuella datorns systemtilldelade hanterade identitet åtkomst till nycklarna till ett befintligt lagringskonto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i det övre vänstra hörnet på Azure-portalen.
2. Fönstret Skapa lagringskonto visas om du klickar på **Lagring** och sedan **Lagringskonto**.
3. Ange ett **Namn** för lagringskonto, som du kommer att använda senare.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på Resurshanterare respektive Generell användning. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa ett nytt lagringskonto](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blob-container i lagringskontot

Senare ska vi ladda upp och ned en fil till det nya lagringskontot. Eftersom filer kräver blob-lagring måste vi skapa en blob-container som filen ska lagras i.

1. Gå tillbaka till det lagringskonto som du nyss skapade.
2. Klicka på länken **Containers** till vänster, under Blob Service.
3. Klicka på **+ Container** längst upp på sidan. Panelen ”Ny container” visas.
4. Ge containern ett namn, välj en åtkomstnivå och klicka sedan på **OK**. Du ska använda det här namnet senare i självstudien. 

    ![Skapa en lagringscontainer](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Bevilja den virtuella datorns systemtilldelade hanterade identitet åtkomst till att använda lagringskontots åtkomstnycklar

I det här steget ger du den virtuella datorns systemtilldelade hanterade identitet åtkomst till nycklarna till lagringskontot.   

1. Gå tillbaka till det lagringskonto som du nyss skapade.
2. Klicka på länken **Åtkomstkontroll (IAM)** på den vänstra panelen.  
3. Klicka på **+ Lägg till rolltilldelning** längst upp på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ställ in **Roll** på ”Tjänstroll som operatör av lagringskontonyckel”, till höger på sidan. 
5. I nästa listruta väljer du resursen Virtuell dator under **Tilldela behörighet till**.  
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan **Prenumeration**. Välj Alla resursgrupper under **Resursgrupper**.  
7. Under **Välj** väljer du sedan din virtuella Linux-dator i listrutan och klickar på **Spara**. 

    ![Alternativ bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Azure Resource Manager

Under resten av självstudien arbetar vi från den virtuella datorn som vi skapade tidigare.

Om du vill slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Så här använder du SSH-nycklar med Windows i Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [How to create and use an SSH public and private key pair for Linux VMs in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) (Skapa och använda SSH-nyckelpar med privata och offentliga nycklar för virtuella Linux-datorer i Azure).

1. Gå till **Virtuella datorer** på Azure-portalen, gå till den virtuella Linux-datorn och klicka sedan längst upp på **Anslut** på sidan **Översikt**. Kopiera strängen för anslutning till din virtuella dator. 
2. Anslut till den virtuella datorn med hjälp av SSH-klienten.  
3. Nu uppmanas du att ange **lösenordet** som du lade till när du skapade **den virtuella Linux-datorn**. Därefter bör du loggas in.  
4. Använd CURL och hämta en åtkomsttoken för Azure Resource Manager.  

    CURL-begäran och -svar för åtkomsttoken visas nedan:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true
    ```
    
    > [!NOTE]
    > I föregående begäran måste värdet för resource-parametern vara en exakt matchning av vad som förväntas av Azure Active Directory. När du använder resurs-ID:t för Azure Resource Manager måste du ta med det avslutande snedstrecket i URI:n.
    > I följande svar har access_token-elementet kortats ned.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Hämta åtkomstnycklar för lagringskonton från Azure Resource Manager om du vill göra lagringsanrop  

Anropa Resource Manager via CURL med hjälp av den åtkomsttoken som vi hämtade i föregående avsnitt och hämta lagringsåtkomstnyckeln. När vi har fått lagringsåtkomstnyckeln kan vi anropa uppladdnings- och nedladdningsåtgärder för lagring. Ersätt parametervärdena `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` och `<STORAGE ACCOUNT NAME>` med dina egna värden. Ersätt värdet `<ACCESS TOKEN>` med den åtkomsttoken som du hämtade tidigare:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Eftersom texten i den tidigare URL:en är skiftlägeskänslig måste du använda samma skiftläge för dina resursgrupper. Dessutom är det viktigt att du är medveten om att det här är en POST-begäran och inte en GET-begäran, samt att du skickar ett värde och registrerar en längdbegränsning med -d, som kan vara NULL.  

CURL-svaret returnerar listan med nycklar:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Skapa en blob-exempelfil att ladda upp till din bloblagringscontainer. På en virtuell Linux-dator kan du göra detta med hjälp av följande kommando. 

```bash
echo "This is a test file." > test.txt
```

Sedan autentiserar du med CLI-kommandot `az storage` med hjälp av lagringsåtkomstnyckeln och laddar upp filen till blobcontainern. För det här steget behöver du [installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på din virtuella dator, om du inte redan har gjort det.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Svar: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Du kan dessutom ladda ned filen med hjälp av Azure CLI och autentisera med lagringsåtkomstnyckeln. 

Begäran: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Svar: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure Storage med hjälp av en åtkomstnyckel.  Läs mer om Azure Storage-åtkomstnycklar här:

> [!div class="nextstepaction"]
>[Hantera dina lagringsåtkomstnycklar](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)
