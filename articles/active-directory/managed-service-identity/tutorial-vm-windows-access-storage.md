---
title: Använda en Windows VM hanteras identitet för åtkomst till Azure Storage
description: En självstudiekurs som vägleder dig genom processen med att använda en Windows hanteras identitet för att få åtkomst till Azure Storage.
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
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: 9ccc94727a18fbcd77f00000531934be57b8e132
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801372"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>Självstudier: Använda en Windows VM hanteras identitet för åtkomst till Azure Storage

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här kursen visar hur du aktiverar hanteras identitet för en virtuell Windows-dator och använda identitet för åtkomst till Azure Storage.  Lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator i en ny resursgrupp 
> * Aktivera hanterade identiteten på en Windows-dator (VM)
> * Skapa en blobbbehållare i ett lagringskonto
> * Ge din Windows-VM hanteras identitet åtkomst till ett lagringskonto 
> * Hämta en tillgång och använda den för att anropa Azure Storage 

> [!NOTE]
> Azure Active Directory-autentisering för Azure Storage är tillgänglig som förhandsversion.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

I det här avsnittet skapar du en virtuell Windows-dator som en hanterad identitet ges senare.

1.  Klicka på den **+/ Skapa ny tjänst** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. Den **användarnamn** och **lösenord** skapade här är de autentiseringsuppgifter som du använder för att logga in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Att välja en ny **resursgruppen** du skulle vilja virtuella datorn ska skapas i, Välj **Skapa nytt**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

    ![ALT bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-identity-on-your-vm"></a>Aktivera hanterade identitet på den virtuella datorn

En identitet för virtuell dator hanteras kan du få åtkomst-token från Azure AD utan att behöva placera autentiseringsuppgifter i din kod. Under försättsbladen, aktivera hanteras identiteten på en virtuell dator via Azure portal gör två saker: registrerar den virtuella datorn med Azure AD för att skapa en hanterad identitet och konfigurerar den identiteten på den virtuella datorn. 

1. Gå till resursgruppen för den nya virtuella datorn och välj den virtuella dator som du skapade i föregående steg.
2. Under den **inställningar** kategori, klickar du på **Configuration**.
3. Välj för att aktivera hanteras identiteten **Ja**.
4. Klicka på **spara** att tillämpa konfigurationen. 

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

I det här avsnittet skapar du ett lagringskonto. 

1. Klicka på den **+ skapa en resurs** knapp hittades i det övre vänstra hörnet i Azure-portalen.
2. Klicka på **lagring**, sedan **lagringskonto - blob, fil, tabell, kö**.
3. Under **namn**, ange ett namn för lagringskontot.  
4. **Distributionsmodell** och **konto kind** ska anges till **Resource manager** och **lagring (generella v1)**. 
5. Se till att den **prenumeration** och **resursgruppen** matchar de som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa nytt lagringskonto](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Skapa en blob-behållare och överföra en fil till lagringskontot

Filerna kräver blobblagring så måste du skapa en blob-behållare där filen ska sparas. Du kan sedan överföra en fil till blob-behållare i det nya lagringskontot.

1. Gå tillbaka till det nyligen skapade lagringskontot.
2. Under **Blob-tjänsten**, klickar du på **behållare**.
3. Klicka på **+ behållare** överst på sidan.
4. Under **ny behållare**, ange ett namn för behållaren och under **offentliga åtkomstnivå** Behåll standardvärdet.

    ![Skapa lagringsbehållaren](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

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

Här är ett .net-kodexempel för att öppna en anslutning till Azure Storage med hjälp av en åtkomst-token och sedan läsa innehållet i filen som du skapade tidigare. Den här koden måste köras på den virtuella datorn för att kunna komma åt den virtuella datorn hanteras identitet slutpunkt. .NET framework 4.6 eller senare krävs för att använda åtkomstmetoden-token. Ersätt värdet för `<URI to blob file>` därefter. Du kan hämta det här värdet genom att navigera till filen som du skapat och överförs till blob-lagring och kopiera den **URL** under **egenskaper** den **översikt** sidan.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

Svaret innehåller innehållet i filen:

`Hello world! :)`

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig hur aktivera Windows-dator hanterad identitet för åtkomst till Azure Storage.  Om du vill veta mer om Azure Storage, se:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



