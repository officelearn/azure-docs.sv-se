---
title: Använda en Windows VM hanterad identitet för åtkomst till Azure Storage med SAS-autentiseringsuppgifter
description: En självstudiekurs som visar hur du använder en Windows VM hanterad tjänstidentitet för att komma åt Azure Storage, med hjälp av SAS-autentiseringsuppgifter i stället för en åtkomstnyckel för lagringskontot.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 7f6049e874f329c1e3a4f72417dd9a7eebc42628
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887071"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Självstudier: Använda en Windows VM hanterad tjänstidentitet för åtkomst till Azure Storage via SAS-autentiseringsuppgifter

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du använder en systemtilldelad identitet för en Windows virtuell dator (VM) för att hämta en signatur för delad åtkomst (SAS) storage-autentiseringsuppgifter. Mer specifikt, en [autentiseringsuppgift för tjänst-SAS](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

En SAS för tjänst ger möjlighet att bevilja begränsad åtkomst till objekt i ett lagringskonto under begränsad tid och en specifik tjänst (i vårt fall blob service), utan att exponera en åtkomstnyckel. Du kan en använda SAS-autentiseringsuppgift som vanligt när du gör lagringsåtgärder, till exempel när du använder Storage SDK. Den här självstudien visar vi överföra och ladda ned en blob med hjälp av PowerShell för Azure Storage. Du lär dig hur du:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Ge den virtuella datorn åtkomst till en SAS för lagringskonton i Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använde den när du hämtar SAS:en från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Logga in på Azure-portalen](https://portal.azure.com)

- [Skapa en Windows-dator](/azure/virtual-machines/windows/quick-create-portal)

- [Aktivera systemtilldelad identitet på den virtuella datorn](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)


## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Nu skapar du ett lagringskonto, om du inte redan har ett. Du kan även hoppa över detta steg och ge din VM hanterad tjänstidentitet åtkomst till SAS-autentiseringsuppgifter för ett befintligt lagringskonto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i det övre vänstra hörnet på Azure Portal.
2. Klicka på **Lagring** och sedan på **Lagringskonto**. Panelen ”Skapa lagringskonto” visas.
3. Ange ett namn för lagringskontot. Du ska använda namnet senare.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på Resurshanterare respektive Generell användning. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa ett nytt lagringskonto](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blob-container i lagringskontot

Senare ska vi ladda upp och ned en fil till det nya lagringskontot. Eftersom filer kräver blob-lagring måste vi skapa en blob-container som filen ska lagras i.

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Klicka på länken **Containers** i vänstra panelen, under Blob Service.
3. När du klickar på **+Container** högst upp på sidan visas fönstret Ny container.
4. Ge containern ett namn, välj en åtkomstnivå och klicka sedan på **OK**. Du ska använda det här namnet senare i självstudien. 

    ![Skapa en lagringscontainer](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-managed-service-identity-access-to-use-a-storage-sas"></a>Ge den virtuella datorns hanterade tjänstidentitet behörighet att använda lagrings-SAS 

Azure Storage har inte inbyggt stöd för Azure AD-autentisering.  Du kan dock använda en hanterad tjänstidentitet för att hämta en SAS-lagring från Resource Manager och sedan använda SAS åtkomst till lagring.  I det här steget ger du den virtuella datorns hanterade tjänstidentitet åtkomst till lagringskontots SAS.   

1. Gå tillbaka till det lagringskonto som du nyss skapade.   
2. Klicka på länken **Åtkomstkontroll (IAM)** på den vänstra panelen.  
3. Klicka på **+Lägg till** högst upp på sidan om du vill lägga till en ny rolltilldelning för din virtuella dator
4. Ställ in Lagringskontodeltagare som **Roll**, till höger på sidan.  
5. I nästa listruta väljer du resursen Virtuell dator under **Tilldela behörighet till**.  
6. Kontrollera sedan att rätt prenumeration visas i listrutan **Prenumeration**. Välj Alla resursgrupper under **Resursgrupper**.  
7. Under **Välj** väljer du sedan din virtuella Windows-dator i listrutan och klickar på **Spara**. 

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Azure Resource Manager 

Under resten av självstudiekursen arbetar vi från den virtuella datorn som vi skapade tidigare.

Du måste använda Azure Resource Manager PowerShell-cmdletar i den här delen.  Om du inte har installerat det [hämtar du den senaste versionen](https://docs.microsoft.com/powershell/azure/overview) innan du fortsätter.

1. Gå till **Virtuella datorer** på Azure Portal, gå till din virtuella Windows-dator och klicka sedan på **Anslut** längst upp på sidan **Översikt**.
2. Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen. 
4. Använd PowerShells Invoke-WebRequest och skicka en begäran till den lokala slutpunkten för hanterad tjänstidentitet för att hämta en åtkomsttoken för Azure Resource Manager.

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

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Hämta en SAS-autentiseringsuppgift från Azure Resource Manager om du vill göra lagringsanrop 

Nu använda PowerShell för att anropa Resource Manager med den åtkomsttoken som vi hämtade i föregående avsnitt, för att skapa SAS-autentiseringsuppgifter lagring. När vi har SAS-autentiseringsuppgifter, kan du anropa lagringsåtgärder.

För denna begäran använder vi följande begäranparametrar och skapa SAS-autentiseringsuppgiften:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Dessa parametrar används i SAS-autentiseringsuppgiftens POST-begäran. Mer information om parametrarna som krävs när du skapar en SAS-autentiseringsuppgift finns i [referensen för tjänsten för SAS REST-listan](/rest/api/storagerp/storageaccounts/listservicesas).

Först konvertera parametrarna till JSON och sedan anropa storage `listServiceSas` slutpunkt för att skapa SAS-autentiseringsuppgifter:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Eftersom URL:en är skiftlägeskänslig måste du använda exakt samma skiftläge som du använde tidigare när du namngav resursgruppen, inklusive versalt ”G” i ”resourceGroups”. 

Nu kan vi extrahera SAS-autentiseringsuppgifter från svaret:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Om du inspektera SAS-Använ visas ungefär så här:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Nu ska vi skapa en fil med namnet ”test.txt”. Använder SAS-autentiseringsuppgifter för att autentisera den `New-AzureStorageContent` cmdlet, ladda upp filen till vår blob-behållare och sedan ladda ned filen.

```bash
echo "This is a test text file." > test.txt
```

Se till att installera alla Azure Storage-cmdlets först med hjälp av `Install-Module Azure.Storage`. Ladda upp bloben som du precis skapat med hjälp av PowerShell-cmdleten `Set-AzureStorageBlobContent`:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Svar:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Du kan också ladda ned bloben som du precis laddade upp med hjälp av PowerShell-cmdleten `Get-AzureStorageBlobContent`:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Svar:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en hanterad tjänstidentitet för att komma åt Azure Storage med SAS-autentiseringsuppgifter.  Läs mer om Azure Storage SAS här:

> [!div class="nextstepaction"]
>[Använda signaturer för delad åtkomst (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)


