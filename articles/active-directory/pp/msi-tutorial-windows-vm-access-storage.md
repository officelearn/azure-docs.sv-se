---
title: Använd en virtuell MSI-dator för Windows för att få åtkomst till Azure Storage
description: En självstudiekurs som vägleder dig genom processen med att använda en Windows VM hanterad tjänstidentitet (MSI) för att få åtkomst till Azure Storage.
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
ms.openlocfilehash: 6e1364db9ecba65b90be525141f03fb9b4a33d28
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610844"
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Använda en Windows VM hanterad tjänstidentitet för åtkomst till Azure Storage via åtkomstnyckel

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien visar hur du aktiverar hanterad tjänstidentitet (MSI) för en Windows-dator och sedan använda den identiteten för att hämta lagringskontot. Du kan använda åtkomstnycklar för lagring som vanligt vid lagringsåtgärder, till exempel när du använder Storage-SDK. Den här självstudien vi ladda upp och ladda ned blobar med Azure Storage PowerShell. Du får lära dig hur du:


> [!div class="checklist"]
> * Aktivera MSI på en Windows-dator 
> * Ge din VM-åtkomst till åtkomstnycklarna för lagringskontot i Resource Manager 
> * Få ett åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att hämta åtkomstnycklar för lagring från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en Windows-dator i en ny resursgrupp.

I den här självstudiekursen skapar vi en ny virtuell Windows-dator. Du kan också aktivera MSI på en befintlig virtuell dator.

1.  Klicka på den **+/ Skapa ny tjänst** knappen hittades på det övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Att välja en ny **resursgrupp** du skulle vilja virtuella datorn skapas i, väljer **Skapa ny**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI-dator kan du hämta åtkomsttoken från Azure AD utan att du behöver att placera autentiseringsuppgifter i din kod. Under försättsbladen, när du aktiverar MSI gör två saker: MSI VM-tillägget installeras på den virtuella datorn och gör att MSI för den virtuella datorn.  

1. Navigera till resursgruppen för den nya virtuella datorn och välj den virtuella dator som du skapade i föregående steg.
2. Under den virtuella datorn ”inställningar” till vänster klickar du på **Configuration**.
3. Om du vill registrera och aktiverar MSI, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Om du vill kontrollera vilka tillägg som finns på den virtuella datorn, klicka på **tillägg**. Om MSI aktiveras den **ManagedIdentityExtensionforWindows** visas i listan.

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Om du inte redan har en, kommer du nu skapa ett lagringskonto. Du kan även hoppa över detta steg och ge din VM MSI åtkomst till nycklarna i ett befintligt lagringskonto. 

1. Klicka på den **+/ Skapa ny tjänst** knappen hittades på det övre vänstra hörnet i Azure-portalen.
2. Klicka på **Storage**, sedan **Lagringskonto**, och en ny panel ”skapa storage-konto” visas.
3. Ange ett namn för storage-konto som du ska använda senare.  
4. **Distributionsmodellen** och **typ av konto** respektive ska vara inställd på ”Resource manager” och ”Allmänt”. 
5. Se till att den **prenumeration** och **resursgrupp** överensstämmer med de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blobbehållare i lagringskontot

Senare kommer vi ladda upp och hämta en fil till det nya lagringskontot. Eftersom filer kräver blob-lagring, som vi behöver skapa en blob-behållare där du vill lagra filen.

1. Gå tillbaka till din nyligen skapade lagringskontot.
2. Klicka på den **behållare** länken till vänster under ”Blob service”.
3. Klicka på **+ behållare** överst på sidan och en ”ny behållare” panelen skjuts ut.
4. Ge behållaren ett namn, Välj en åtkomstnivå och sedan på **OK**. Det namn du angav används senare under kursen. 

    ![Skapa lagringsbehållare](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Ge den Virtuella datorns MSI åtkomst för att använda åtkomstnycklar för lagringskontot 

Azure Storage stöder inte internt Azure AD-autentisering.  Du kan dock använda en MSI för att hämta lagringskontot från Resource Manager och sedan använda en nyckel för att få åtkomst till lagring.  I det här steget ska ge du din VM MSI åtkomst till nycklarna till ditt lagringskonto.   

1. Gå tillbaka till din nyligen skapade lagringskontot.  
2. Klicka på den **åtkomstkontroll (IAM)** länken i den vänstra panelen.  
3. Klicka på **+ Lägg till** på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ange **rollen** till ”Storage-konto nyckeln Tjänstroll som operatör”, till höger på sidan. 
5. I nästa listrutan ange **tilldela åtkomst till** resursen ”virtuell dator”.  
6. Kontrollera sedan att korrekt prenumeration visas i **prenumeration** listrutan ange **resursgrupp** till ”alla resursgrupper”.  
7. Slutligen går **Välj** väljer din Windows-dator i listrutan och klickar sedan på **spara**. 

    ![ALT bildtext](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Få ett åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att anropa Azure Resource Manager 

Under resten av kursen arbetar vi från den virtuella datorn som vi skapade tidigare. 

Du behöver använda Azure Resource Managers PowerShell-cmdlets i den här delen.  Om du inte har installerats, [hämta den senaste versionen](https://docs.microsoft.com/powershell/azure/overview) innan du fortsätter.

1. I Azure-portalen går du till **virtuella datorer**går du till Windows virtuella datorn, sedan från den **översikt** klickar du på **Connect** högst upp. 
2. Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.
4. Med hjälp av Powershell-Invoke-WebRequest, skicka en förfrågan till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Värdet för ”resource”-parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder Azure Resource Manager-resurs-ID, måste du inkluderar det avslutande snedstrecket på URI: N.
    
    Extrahera sedan elementet ”innehåll”, som lagras som en JavaScript Object Notation (JSON)-formaterad sträng i $response-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahera sedan den åtkomst-token från svaret.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Hämta lagringskontot från Azure Resource Manager för att göra storage-anrop  

Nu använda PowerShell för att anropa Resource Manager med den åtkomsttoken som vi hämtade i föregående avsnitt, att hämta åtkomstnyckel för lagring. När vi har åtkomstnyckel för lagring kan du anropa uppladdning/nedladdning lagringsåtgärder.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> URL: en är skiftlägeskänsligt, så se till att du används på exakt samma sätt som använde tidigare, när du med namnet på resursgruppen, inklusive versaler ”G” i ”resourceGroups”. 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Därefter skapar vi en fil med namnet ”test.txt”. Sedan använder lagringsåtkomstnyckeln för att autentisera den `New-AzureStorageContent` cmdlet, ladda upp filen till vår blob-behållare och sedan ladda ned filen.

```bash
echo "This is a test text file." > test.txt
```

Var noga med att installera Azure Storage-cmdletar först med `Install-Module Azure.Storage`. Ladda upp blob som du just skapade med den `Set-AzureStorageBlobContent` PowerShell-cmdlet:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
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
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](msi-overview.md).
- Läs hur du gör det här samma självstudier med storage SAS-autentiseringsuppgifter i [använda en Windows VM hanterad tjänstidentitet för åtkomst till Azure Storage via SAS-autentiseringsuppgifter](msi-tutorial-windows-vm-access-storage-sas.md)
- Mer information om SAS-funktionen i Azure Storage-konto finns:
  - [Använda signaturer för delad åtkomst (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Skapa en tjänst-SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll


