---
title: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Storage
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Azure Storage.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fd2e30f7ae96ff38b0ded11c158fcef636e3a26
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448807"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Självstudier: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Storage via åtkomstnyckel

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig att hämta åtkomstnycklar till lagringskonton med en systemtilldelad hanterad identitet för en virtuell Windows-dator. Du kan använda lagringsåtkomstnycklar som vanligt när du utför lagringsåtgärder, till exempel när du använder Storage SDK. I den här självstudien laddar vi upp och hämtar blobar med Azure Storage PowerShell. Du lär dig hur du:


> [!div class="checklist"]
> * skapar ett lagringskonto
> * Ger den virtuella datorn åtkomst till åtkomstnycklar för lagringskonton i Resource Manager 
> * Få en åtkomsttoken med hjälp av den virtuella datorns identitet, och använda den och hämta lagringsåtkomstnycklarna från Resource Manager 

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Nu skapar du ett lagringskonto, om du inte redan har ett. Du kan även hoppa över det här steget och ge den virtuella datorns systemtilldelade hanterade identitet åtkomst till nycklarna till ett befintligt lagringskonto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i det övre vänstra hörnet på Azure Portal.
2. Klicka på **Lagring** och sedan på **Lagringskonto**. Panelen ”Skapa lagringskonto” visas.
3. Ange ett namn för lagringskontot. Du ska använda namnet senare.  
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

Azure Storage har inte inbyggt stöd för Azure AD-autentisering.  Men du kan använda den virtuella datorns systemtilldelade hanterade identitet till att hämta åtkomstnycklar för lagringskonton från Resource Manager, och sedan få åtkomst till lagringen med hjälp av en nyckel.  I det här steget ger du den virtuella datorns systemtilldelade hanterade identitet åtkomst till nycklarna till lagringskontot.   

1. Gå tillbaka till det lagringskonto som du nyss skapade.  
2. Klicka på länken **Åtkomstkontroll (IAM)** på den vänstra panelen.  
3. Klicka på **+ Lägg till rolltilldelning** längst upp på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ställ in **Roll** på ”Tjänstroll som operatör av lagringskontonyckel”, till höger på sidan. 
5. I nästa listruta väljer du resursen Virtuell dator under **Tilldela behörighet till**.  
6. Kontrollera sedan att rätt prenumeration visas i listrutan **Prenumeration**. Välj Alla resursgrupper under **Resursgrupper**.  
7. Under **Välj** väljer du sedan din virtuella Windows-dator i listrutan och klickar på **Spara**. 

    ![Alternativ bildtext](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns systemtilldelade hanterade identitet för att anropa Azure Resource Manager 

Under resten av självstudien arbetar vi från den virtuella datorn som vi skapade tidigare. 

Du måste använda Azure Resource Manager PowerShell-cmdletar i den här delen.  Om du inte har installerat det [hämtar du den senaste versionen](https://docs.microsoft.com/powershell/azure/overview) innan du fortsätter.

1. Gå till **Virtuella datorer** på Azure Portal, gå till din virtuella Windows-dator och klicka sedan på **Anslut** längst upp på sidan **Översikt**. 
2. Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.
4. Använd PowerShells Invoke-WebRequest och skicka en begäran till den lokala slutpunkten för hanterad identitet för Azure-resurser för att hämta en åtkomsttoken för Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Värdet för parametern ”resource” måste vara en exakt matchning av vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID:t måste du ta med det avslutande snedstrecket i URI:n.
    
    Extrahera sedan elementet ”Content”, som lagras som en JSON-formaterad sträng (JavaScript Object Notation) i $response-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahera sedan åtkomsttoken från svaret.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Hämta åtkomstnycklar för lagringskonton från Azure Resource Manager för att göra lagringsanrop  

Anropa Resource Manager via PowerShell och använd den åtkomsttoken som vi hämtade i föregående avsnitt för att hämta lagringsåtkomstnyckeln. När vi har fått lagringsåtkomstnyckeln kan vi anropa uppladdnings- och nedladdningsåtgärder för lagring.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> Eftersom URL:en är skiftlägeskänslig måste du använda exakt samma skiftläge som du använde tidigare när du namngav resursgruppen, inklusive versalt ”G” i ”resourceGroups”. 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Nu ska vi skapa en fil med namnet ”test.txt”. Använd sedan lagringsåtkomstnyckeln för att autentisera med cmdleten `New-AzStorageContent`, ladda upp filen till vår blob-container och ladda sedan ned filen.

```bash
echo "This is a test text file." > test.txt
```

Se till att installera alla Azure Storage-cmdlets först med hjälp av `Install-Module Az.Storage`. Ladda upp bloben som du precis skapat med hjälp av PowerShell-cmdleten `Set-AzStorageBlobContent`:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Svar:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Du kan också ladda ned bloben som du precis laddade upp med hjälp av PowerShell-cmdleten `Get-AzStorageBlobContent`:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Svar:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en systemtilldelad hanterad identitet för att komma åt Azure Storage med hjälp av en åtkomstnyckel.  Läs mer om Azure Storage-åtkomstnycklar här:

> [!div class="nextstepaction"]
>[Hantera dina lagringsåtkomstnycklar](/azure/storage/common/storage-create-storage-account)

