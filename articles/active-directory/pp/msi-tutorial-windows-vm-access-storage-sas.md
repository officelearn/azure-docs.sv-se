---
title: Använd en virtuell MSI-dator för Windows för att få åtkomst till Azure Storage med SAS-autentiseringsuppgifter
description: En självstudie som visar hur du använder en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Storage, med hjälp av SAS-autentiseringsuppgifter i stället för en åtkomstnyckel för lagringskontot.
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
ms.openlocfilehash: 95dac6a6593d8ded98a245005ae78ee50f75aeeb
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056155"
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Använda en Windows VM hanterad tjänstidentitet för åtkomst till Azure Storage via SAS-autentiseringsuppgifter

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en Windows-dator och sedan använda MSI för att hämta autentiseringsuppgift för signatur för delad åtkomst (SAS) storage. Mer specifikt, en [autentiseringsuppgift för tjänst-SAS](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

En SAS för tjänst ger möjlighet att bevilja begränsad åtkomst till objekt i ett lagringskonto under begränsad tid och en specifik tjänst (i vårt fall blob service), utan att exponera en åtkomstnyckel. Du kan en använda SAS-autentiseringsuppgift som vanligt när du gör lagringsåtgärder, till exempel när du använder Storage SDK. Den här självstudien visar vi överföra och ladda ned en blob med hjälp av PowerShell för Azure Storage. Du lär dig att göra följande:


> [!div class="checklist"]
> * Aktivera MSI på en Windows-dator 
> * Ge den virtuella datorn åtkomst till en SAS för lagringskonton i Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använde den när du hämtar SAS:en från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

I den här självstudiekursen skapar vi en ny virtuell Windows-dator. Du kan även aktivera MSI på en befintlig virtuell dator.

1.  Klicka på knappen **+/Skapa ny tjänst** som finns i övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. **Användarnamnet** och **lösenordet** som skapas här är de autentiseringsuppgifter som du använder när du loggar in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Välj en ny **Resursgrupp** som den virtuella datorn ska skapas i genom att klicka på **Skapa ny**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

Med en MSI för virtuell dator kan du få åtkomsttoken från Azure Active Directory utan att du behöver skriva in autentiseringsuppgifter i koden. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI för den virtuella datorn.  

1. Gå till den nya virtuella datorns resursgrupp och välj den virtuella dator som du skapade i förra steget.
2. Under den virtuella datorn ”inställningar” i den vänstra rutan klickar du på **Configuration**.
3. Om du vill registrera och aktivera den hanterade tjänstidentiteten väljer du **Ja**. Om du vill inaktivera den väljer du Nej.
4. Klicka på **Spara** om du vill spara konfigurationen.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera vilka tillägg som finns på den virtuella datorn, klicka på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforWindows** visas i listan.

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto 

Nu skapar du ett lagringskonto, om du inte redan har ett. Du kan även hoppa över detta steg och ge din VM MSI åtkomst till SAS-autentiseringsuppgifter för ett befintligt lagringskonto. 

1. Klicka på knappen **+/Skapa ny tjänst** som finns i övre vänstra hörnet i Azure-portalen.
2. Fönstret Skapa lagringskonto visas om du klickar på **Lagring** och sedan **Lagringskonto**.
3. Ange ett namn för storage-konto som du ska använda senare.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på Resurshanterare respektive Generell användning. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** stämmer överens med den du angav när du skapade den virtuella datorn i förra steget.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blob-container i lagringskontot

Senare laddar vi upp och ned en fil i det nya lagringskontot. Vi skapar en blob-container att lagra filen i, eftersom filer kräver blob-lagring.

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Klicka på länken **Containers** i vänstra panelen, under Blob Service.
3. När du klickar på **+Container** högst upp på sidan visas fönstret Ny container.
4. Ge containern ett namn, välj en åtkomstnivå och klicka sedan på **OK**. Namnet du angav används senare i självstudien. 

    ![Skapa lagringscontainer](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Ge den virtuella datorns MSI behörighet att använda en lagrings-SAS 

Azure Storage har inte inbyggt stöd för Azure Active Directory-autentisering.  Du kan dock använda en MSI för att hämta en SAS-lagring från Resource Manager och sedan använda SAS åtkomst till lagring.  I det här steget ger du din virtuella dators MSI åtkomst till SAS:en för lagringskontot.   

1. Gå tillbaka till det lagringskonto du nyss skapade.   
2. Klicka på länken **åtkomstkontroll (IAM)** i vänstra panelen.  
3. Klicka på **+Lägg till** högst upp på sidan om du vill lägga till en ny rolltilldelning för din virtuella dator
4. Ställ in Lagringskontodeltagare som **Roll**, till höger på sidan.  
5. I nästa listruta väljer du resursen Virtuell dator under **Tilldela behörighet till**.  
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan **Prenumeration**. Välj Alla resursgrupper under **Resursgrupper**.  
7. Slutligen går **Välj** väljer din Windows-dator i listrutan och klickar sedan på **spara**. 

    ![Alternativ bildtext](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Skaffa en åtkomsttoken med hjälp av den virtuella datorns identitet, och använd den och anropa Azure Resource Manager 

Under resten av självstudien arbetar vi från den virtuella datorn som vi skapade tidigare.

Du behöver använda Azure Resource Managers PowerShell-cmdlets i den här delen.  Om du inte har installerats, [hämta den senaste versionen](https://docs.microsoft.com/powershell/azure/overview) innan du fortsätter.

1. I Azure-portalen går du till **virtuella datorer**går du till Windows virtuella datorn, sedan från den **översikt** klickar du på **Connect** högst upp.
2. Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen. 
4. Med hjälp av Powershell-Invoke-WebRequest, skicka en förfrågan till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Värdet för ”resource”-parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder resurs-ID:t för Azure Resource Manager måste du ta med det avslutande snedstrecket i URI:n.
    
    Extrahera sedan elementet ”innehåll”, som lagras som en JavaScript Object Notation (JSON)-formaterad sträng i $response-objektet. 
    
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
> URL: en är skiftlägeskänsligt, så se till att du används på exakt samma sätt som använde tidigare, när du med namnet på resursgruppen, inklusive versaler ”G” i ”resourceGroups”. 

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

Därefter skapar vi en fil med namnet ”test.txt”. Använder SAS-autentiseringsuppgifter för att autentisera den `New-AzureStorageContent` cmdlet, ladda upp filen till vår blob-behållare och sedan ladda ned filen.

```bash
echo "This is a test text file." > test.txt
```

Var noga med att installera Azure Storage-cmdletar först med `Install-Module Azure.Storage`. Ladda upp blob som du just skapade med den `Set-AzureStorageBlobContent` PowerShell-cmdlet:

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

Du kan också ladda ned bloben du laddade upp, med hjälp av den `Get-AzureStorageBlobContent` PowerShell-cmdlet:

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


## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- Lär dig hur du gör det här samma självstudier med hjälp av en lagringskontonyckel, se [använda en Windows VM hanterad tjänstidentitet för åtkomst till Azure Storage](msi-tutorial-windows-vm-access-storage.md)
- Mer information om SAS-funktionen i Azure Storage-konto finns:
  - [Använda signaturer för delad åtkomst (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Skapa en tjänst-SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.


