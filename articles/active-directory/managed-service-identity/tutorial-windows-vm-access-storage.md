---
title: Använda en Windows VM-MSI för åtkomst till Azure Storage
description: En självstudiekurs som vägleder dig genom processen med att använda en Windows VM hanterade tjänsten identitet (MSI) för att få åtkomst till Azure Storage.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 848033f79674e6ad1457d885c75215fc5c434d93
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Använda en Windows VM hanteras tjänstidentitet för åtkomst till Azure Storage via snabbtangent

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här kursen visar hur du aktivera hanterade tjänsten identitet (MSI) för en virtuell Windows-dator och sedan använda identitet för att hämta nycklar för lagringskonto åtkomst. Du kan använda åtkomstnycklar för lagring som vanligt när gör lagringsåtgärder, till exempel när du använder Storage SDK: N. Den här självstudiekursen kommer vi ladda upp och hämta blobar med hjälp av Azure Storage PowerShell. Du får lära dig hur du:


> [!div class="checklist"]
> * Aktivera MSI på en virtuell dator för Windows 
> * Ger dina VM tillgång till åtkomstnycklarna för storage-konto i Resource Manager 
> * Få en åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att hämta åtkomstnycklar för lagring från Resource Manager 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

Den här självstudiekursen skapar vi en ny Windows virtuell dator. Du kan också aktivera MSI på en befintlig virtuell dator.

1.  Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Att välja en ny **resursgruppen** du skulle vilja virtuella datorn ska skapas i, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

    ![ALT bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivera MSI på den virtuella datorn

En virtuell dator MSI kan du få åtkomst-token från Azure AD utan att du behöver publicera autentiseringsuppgifter i koden. Under försättsbladen, aktivera MSI gör två saker: registrerar den virtuella datorn med Azure Active Directory för att skapa hanterade identitet och konfigurerar identiteten på den virtuella datorn.

1. Gå till resursgruppen för den nya virtuella datorn och välj den virtuella dator som du skapade i föregående steg.
2. Under den virtuella datorn ”inställningar” till vänster klickar du på **Configuration**.
3. För att registrera och aktivera MSI-filerna, Välj **Ja**, om du vill inaktivera det, väljer du Nej.
4. Se till att du klickar på **spara** att spara konfigurationen.

    ![ALT bildtext](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Om du inte redan har ett kommer du nu skapa ett lagringskonto. Du kan också hoppa över detta steg och ger dina VM MSI tillgång till nycklarna i ett befintligt lagringskonto. 

1. Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Klicka på **lagring**, sedan **Lagringskonto**, och en ny panel ”skapa storage-konto” visas.
3. Ange ett namn för storage-konto som du vill använda senare.  
4. **Distributionsmodell** och **konto kind** respektive ska vara inställd på ”Resource manager” och ”generella”. 
5. Se till att den **prenumeration** och **resursgruppen** matchar de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Skapa en blobbbehållare i storage-konto

Senare kommer att ladda upp och hämta en fil till det nya lagringskontot. Eftersom filer kräver blob-lagring, som vi behöver skapa en blobbbehållare där filen ska sparas.

1. Gå tillbaka till det nyligen skapade lagringskontot.
2. Klicka på den **behållare** länken till vänster under ”Blob-tjänst”.
3. Klicka på **+ behållare** överst på sidan och en ”ny behållare” panelen bilder ut.
4. Namnge behållaren, Välj en åtkomstnivå och klicka sedan på **OK**. Det namn du angav används senare under kursen. 

    ![Skapa lagringsbehållaren](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Ge den Virtuella datorns MSI åtkomst för att använda åtkomst lagringskontonycklar 

Azure Storage stöder inte Azure AD authentication internt.  Dock kan du använda en MSI för att hämta nycklar för lagringskonto åtkomst från Resource Manager sedan använda en för att få åtkomst till lagring.  I det här steget kan bevilja du din VM MSI tillgång till nycklarna till ditt lagringskonto.   

1. Gå tillbaka till det nyligen skapade lagringskontot.  
2. Klicka på den **åtkomstkontroll (IAM)** länken i den vänstra panelen.  
3. Klicka på **+ Lägg till** på sidan för att lägga till en ny rolltilldelning för den virtuella datorn
4. Ange **rollen** ”Storage-konto nyckeln operatorn Service roll”, till höger på sidan. 
5. I nästa listrutan, ange **bevilja åtkomst till** resursen ”virtuell dator”.  
6. Kontrollera sedan att korrekt prenumeration visas i **prenumeration** och sedan ange **resursgruppen** till ”alla resursgrupper”.  
7. Slutligen under **Välj** välja din Windows-dator i listrutan och klicka sedan på **spara**. 

    ![ALT bildtext](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Få en åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att anropa Azure Resource Manager 

Vi kommer att fungera från den virtuella datorn som vi skapade tidigare under resten av kursen. 

Du behöver använda Azure Resource Manager PowerShell-cmdlets i den här delen.  Om du inte har installerats, [hämta den senaste versionen](https://docs.microsoft.com/powershell/azure/overview) innan du fortsätter.

1. I Azure-portalen går du till **virtuella datorer**, gå till din Windows-dator, sedan från den **översikt** klickar **Anslut** längst upp. 
2. Ange i din **användarnamn** och **lösenord** för som du har lagt till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du PowerShell i fjärrsessionen.
4. Använder PowerShells Invoke-WebRequest, gör en begäran till den lokala MSI-slutpunkten för att hämta ett åtkomsttoken för Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Värdet för ”resurser”-parametern måste vara en exakt matchning för vad som förväntas av Azure AD. När du använder Azure Resource Manager resurs-ID, måste du inkluderar det avslutande snedstrecket på URI.
    
    Extrahera sedan elementet ”innehåll”, som lagras som en JavaScript Object Notation (JSON) formaterad sträng i $response-objektet. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extrahera sedan den åtkomst-token från svaret.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Hämta nycklar för lagringskonto åtkomst från Azure Resource Manager för att göra anrop för lagring  

Nu använda PowerShell för att anropa Resource Manager med den åtkomst-token som vi hämtades i föregående avsnitt, att hämta lagringsåtkomstnyckel. När vi har lagringsåtkomstnyckel kallar vi överför/hämta lagringsåtgärder.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> URL: en är skiftlägeskänsligt, så se till att du använder samma skiftläge används tidigare, när du gav resursgrupp, inklusive versaler ”G” i ”resursgrupper”. 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Därefter kan vi skapa en fil med namnet ”test.txt”. Använder lagringsåtkomstnyckel för att autentisera den `New-AzureStorageContent` cmdlet, överföra filen till vår blob-behållaren och sedan ladda ned filen.

```bash
echo "This is a test text file." > test.txt
```

Måste du först installera Azure Storage-cmdlets med `Install-Module Azure.Storage`. Överför blob som du precis skapade, med hjälp av den `Set-AzureStorageBlobContent` PowerShell-cmdlet:

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

Du kan också hämta blob som du just har överfört, med hjälp av den `Get-AzureStorageBlobContent` PowerShell-cmdlet:

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

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
- Information om hur du gör detta samma självstudier med lagring SAS-autentiseringsuppgifter finns [använder en Windows VM hanteras tjänstidentitet för åtkomst till Azure Storage via en SAS-autentiseringsuppgifter](tutorial-windows-vm-access-storage-sas.md)
- Mer information om SAS-funktionen i Azure Storage-konto finns:
  - [Använda signaturer för delad åtkomst (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Hur du skapar en tjänst-SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet


