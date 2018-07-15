---
title: Komma åt Azure Storage med en hanterad tjänstidentitet för virtuell Windows-dator
description: En självstudiekurs som steg för steg beskriver hur du använder en hanterad tjänstidentitet på en virtuell Windows-dator och kommer åt Azure Storage.
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
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: a9b0ddbd8d0a348d00e57526e6d248e59a8a1e79
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903969"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>Självstudie: Komma åt Azure Storage med en hanterad tjänstidentitet för virtuell Windows-dator

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du aktiverar hanterad identitet för en virtuell Windows-dator och sedan använder identiteten och kommer åt Azure Storage.  Lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator i en ny resursgrupp 
> * Aktivera hanterad identitet på en virtuell Windows-dator (VM)
> * Skapa en blobcontainer i ett lagringskonto
> * Ge den virtuella Windows-datorns hanterade identitet åtkomst till ett lagringskonton 
> * Hämta en åtkomsttoken, och använda den och anropa Azure Storage 

> [!NOTE]
> Azure Active Directory-autentisering för Azure Storage finns i en allmänt tillgänglig förhandsversion.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Skapa en virtuell Windows-dator i en ny resursgrupp

I det här avsnittet skapar du en virtuell Windows-dator som sedan beviljas en hanterad identitet.

1.  Klicka på knappen **+/Skapa ny tjänst** som finns i övre vänstra hörnet i Azure-portalen.
2.  Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. 
3.  Ange informationen för den virtuella datorn. **Användarnamnet** och **lösenordet** som skapas här är de autentiseringsuppgifter som du använder när du loggar in på den virtuella datorn.
4.  Välj rätt **prenumeration** för den virtuella datorn i listrutan.
5.  Välj en ny **Resursgrupp** som den virtuella datorn ska skapas i genom att klicka på **Skapa ny**. När du är klar klickar du på **OK**.
6.  Välj storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. Acceptera alla standardvärden på bladet Inställningar och klicka på **OK**.

    ![Alternativ bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-identity-on-your-vm"></a>Aktivera hanterad identitet på den virtuella datorn

Med en hanterad identitet på en virtuell dator kan du få åtkomsttoken från Azure Active Directory utan att du behöver skriva in autentiseringsuppgifter i koden. När du aktiverar hanterad identitet på en virtuell dator via Azure-portalen sker två saker: din virtuella dator registreras med Azure Active Directory och skapar en hanterad identitet, och identiteten konfigureras på den virtuella datorn. 

1. Gå till den nya virtuella datorns resursgrupp och välj den virtuella dator som du skapade i förra steget.
2. Under kategorin **Inställningar** klickar du på **Konfiguration**.
3. Välj **Ja** om du vill aktivera hanterad identitet.
4. Klicka på **Spara** och tillämpa konfigurationen. 

## <a name="create-a-storage-account"></a>skapar ett lagringskonto 

I det här avsnittet skapar du ett lagringskonto. 

1. Klicka på knappen **+ Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Klicka på **Lagring** och sedan på **Lagringskonto – blob, fil, tabell, kö**.
3. Under **Namn** anger du ett namn för lagringskontot.  
4. **Distributionsmodell** och **Typ av konto** ska vara inställda på **Resurshanterare** respektive **Lagring (generell användning v1)**. 
5. Kontrollera att informationen under **Prenumeration** och **Resursgrupp** stämmer överens med den du angav när du skapade den virtuella datorn i förra steget.
6. Klicka på **Skapa**.

    ![Skapa ett nytt lagringskonto](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Skapa en blobcontainer och ladda upp en fil till lagringskontot

Eftersom filer kräver bloblagring måste du skapa en blobcontainer som du lagrar filen i. Sedan laddar du upp en fil till blobcontainern i det nya lagringskontot.

1. Gå tillbaka till det lagringskonto du nyss skapade.
2. Under **Blob Service** klickar du på **Containers**.
3. Klicka på **+ Container** överst på sidan.
4. Under **Ny container**anger du ett namn för containern och under **Offentlig åtkomstnivå** behåller du standardvärdet.

    ![Skapa lagringscontainer](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Med hjälp av valfritt redigeringsprogram skapar du en fil som heter *hello world.txt* på den lokala datorn.  Öppna filen och lägg till följande text (utan citattecken): ”Hello world! :)”och spara den. 
6. Ladda upp filen till containern du skapade genom att klicka på containernamnet och sedan på **Ladda upp**
7. I fönstret **Ladda upp blob**, under **Filer**, klickar du på mappikonen och bläddrar till filen **hello_world.txt** på den lokala datorn. Välj sedan filen och klicka på **Ladda upp**.
    ![Ladda upp textfil](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Ge din virtuella dator åtkomst till en Azure Storage-container 

Du kan använda den virtuella datorns hanterade identitet och hämta data i Azure Storage-bloben.   

1. Gå tillbaka till det lagringskonto du nyss skapade.  
2. Klicka på länken **åtkomstkontroll (IAM)** i vänstra panelen.  
3. Klicka på **+ Lägg till** högst upp på sidan om du vill lägga till en ny rolltilldelning för din virtuella dator.
4. I listrutan under **Roll** väljer du **Storage Blob Data-läsare (förhandsgranskning)**. 
5. I listrutan under **Tilldela behörighet till** väljer du **Virtuell dator**.  
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan **Prenumeration**. Under **Resursgrupper** väljer du **Alla resursgrupper**.  
7. Under **Välj** väljer du din virtuella dator och klickar sedan på **Spara**. 

    ![Tilldela behörigheter](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Hämta en åtkomsttoken och använda den när du anropar Azure Storage 

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

I den här självstudien har du lärt dig att aktivera en hanterad identitet för virtuell Windows-dator och komma åt Azure Storage.  Läs mer om Azure Storage här:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



