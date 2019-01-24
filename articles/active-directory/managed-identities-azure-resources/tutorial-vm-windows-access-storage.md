---
title: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Storage
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Azure Storage.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: ff5ff237def4bbb5da1c62aca57bd09ee22e574b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432395"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Självstudier: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Storage

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Azure Storage. Lär dig att:

> [!div class="checklist"]
> * Skapa en blobcontainer i ett lagringskonto
> * Ge den systemtilldelade hanterade identiteten för din virtuella Windows-dator åtkomst till ett lagringskonto 
> * Hämta en åtkomsttoken, och använda den och anropa Azure Storage 

> [!NOTE]
> Azure Active Directory-autentisering för Azure Storage finns i en allmänt tillgänglig förhandsversion.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

I det här avsnittet skapar du ett lagringskonto. 

1. Klicka på knappen **+ Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Klicka på **Lagring** och sedan på **Lagringskonto – blob, fil, tabell, kö**.
3. Under **Namn** anger du ett namn för lagringskontot.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på **Resurshanterare** respektive **Lagring (generell användning v1)**. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** matchar informationen som du angav när du skapade den virtuella datorn i föregående steg.
6. Klicka på **Skapa**.

    ![Skapa ett nytt lagringskonto](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Skapa en blobcontainer och ladda upp en fil till lagringskontot

Eftersom filer kräver bloblagring måste du skapa en blobcontainer som du lagrar filen i. Sedan laddar du upp en fil till blobcontainern i det nya lagringskontot.

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Under **Blob Service** klickar du på **Containers**.
3. Klicka på **+ Container** överst på sidan.
4. Under **Ny container**anger du ett namn för containern och under **Offentlig åtkomstnivå** behåller du standardvärdet.

    ![Skapa lagringscontainer](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Med hjälp av valfritt redigeringsprogram skapar du en fil som heter *hello world.txt* på den lokala datorn.  Öppna filen och lägg till följande text (utan citattecken): ”Hello world! :)”och spara den. 
6. Ladda upp filen till containern du skapade genom att klicka på containernamnet och sedan på **Ladda upp**
7. I fönstret **Ladda upp blob**, under **Filer**, klickar du på mappikonen och bläddrar till filen **hello_world.txt** på den lokala datorn. Välj sedan filen och klicka på **Ladda upp**.
    ![Ladda upp textfil](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Ge din virtuella dator åtkomst till en Azure Storage-container 

Du kan använda den virtuella datorns systemtilldelade hanterade identitet för att hämta data i Azure Storage Blob.   

1. Gå tillbaka till det lagringskonto som du nyss skapade.  
2. Klicka på länken **Åtkomstkontroll (IAM)** på den vänstra panelen.  
3. Klicka på **+ Lägg till rolltilldelning** längst upp på sidan för att lägga till en ny rolltilldelning för den virtuella datorn.
4. Välj **Storage Blob Data-läsare (förhandsgranskning)** i listrutan under **Roll**. 
5. I listrutan under **Tilldela behörighet till** väljer du **Virtuell dator**.  
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan **Prenumeration**. Under **Resursgrupper** väljer du **Alla resursgrupper**.  
7. Under **Välj** väljer du din virtuella dator och klickar sedan på **Spara**. 

    ![Tilldela behörigheter](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Hämta en åtkomsttoken och anropa Azure Storage med den 

Azure Storage har inbyggt stöd för Azure Active Directory-autentisering, vilket gör att åtkomsttoken som hämtas med en hanterad identitet kan accepteras direkt. Detta är en del av integreringen av Azure Storage med Azure Active Directory, och skiljer sig från att ange autentiseringsuppgifter i anslutningssträngen.

Här är ett .Net-exempel på att öppna en anslutning till Azure Storage med hjälp av en åtkomsttoken, och sedan läsa innehållet i filen du skapade tidigare. Koden måste köras på den virtuella datorn om du vill komma åt slutpunkten för den virtuella datorns hanterade identitet. .Net Framework 4.6 eller senare krävs om du vill använda metoden med åtkomsttoken. Ersätt värdet för `<URI to blob file>` därefter. Du kan hämta värdet genom att gå till filen du skapade och laddade upp till bloblagringen, och kopiera **URL:en** under **Egenskaper** på sidan **Översikt**.

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

Svaret innehåller filens innehåll:

`Hello world! :)`

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar en systemtilldelad identitet för en virtuell Windows-dator för att få åtkomst till Azure Storage.  Läs mer om Azure Storage här:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



