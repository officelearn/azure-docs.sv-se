---
title: Hantera Azure Blob Storage-resurser med Storage Explorer | Microsoft Docs
description: Hantera Azure Blob-behållare och Blobar med Storage Explorer
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: 8315647afec0166b41ce7a75f491077edd56db46
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036268"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Hantera Azure Blob Storage-resurser med Storage Explorer
## <a name="overview"></a>Översikt
[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) är en tjänst för att lagra stora mängder Ostrukturerade data, exempelvis text eller binära data som kan nås från var som helst i världen via HTTP eller HTTPS.
Du kan använda Blob Storage för att exponera data offentligt eller lagra programdata privat. I den här artikeln lär du dig att använda Storage Explorer för att arbeta med blobbehållare och blobbar.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra stegen i den här artikeln:

* [Hämta och installera Storage Explorer](http://www.storageexplorer.com)
* [Ansluta till ett Azure-lagringskonto eller en Azure-lagringstjänst](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Skapa en blobcontainer
Alla blobbar måste finnas i en blobbehållare, vilket är en logisk gruppering av blobar. Ett konto kan innehålla ett obegränsat antal behållare och varje behållare kan lagra ett obegränsat antal blobbar.

Följande steg illustrerar hur du skapar en blob-behållare i Storage Explorer.

1. Öppna Storage Explorer.
2. Expandera det lagringskonto där du vill skapa blob-behållare i den vänstra rutan.
3. Högerklicka på **Blobbehållare**, och på snabbmenyn - Välj **skapa Blobbehållare**.

   ![Skapa snabbmenyn för blob-behållare][0]
4. En textruta visas nedanför den **Blobbehållare** mapp. Ange namnet på blob-containern. Se den [skapa behållaren och ange behörigheter](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) information om regler och begränsningar för namngivning av blob-behållare.

   ![Skapa Blob-behållare textruta][1]
5. Tryck på **RETUR** när du är klar för att skapa blobbehållare eller **Esc** att avbryta. När blob-behållaren har skapats visas den visas den **Blobbehållare** mapp för det valda lagringskontot.

   ![BLOB-behållare skapas][2]

## <a name="view-a-blob-containers-contents"></a>Visa innehållet i en blob-behållare
BLOB-behållare innehålla blobbar och mappar (som kan också innehålla blobbar).

Följande steg illustrerar hur du visar innehållet i en blob-behållare i Storage Explorer:

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren du vill visa i den vänstra rutan.
3. Expandera lagringskontot **Blobbehållare**.
4. Högerklicka på blob-behållaren som du vill visa och på snabbmenyn - Välj **Öppna Redigeraren för Blob-behållaren**.
   Du kan också dubbelklicka på blob-behållaren som du vill visa.

   ![Öppna blob-behållare redigeraren snabbmenyn][19]
5. I huvudfönstret visas innehållet för blob-behållaren.

   ![Redigeraren för BLOB-behållare][3]

## <a name="delete-a-blob-container"></a>Ta bort en blob-behållare
BLOB-behållare kan enkelt skapas och tas bort efter behov. (Information om hur finns i avsnittet om du vill ta bort enskilda blobar [hantera blobar i en blobbehållare](#managing-blobs-in-a-blob-container).)

Följande steg illustrerar hur du tar bort en blob-behållare i Storage Explorer:

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren du vill visa i den vänstra rutan.
3. Expandera lagringskontot **Blobbehållare**.
4. Högerklicka på blob-behållaren som du vill ta bort och på snabbmenyn - Välj **ta bort**.
   Du kan också trycka på **ta bort** att ta bort markerade blob-behållaren.

   ![Ta bort snabbmenyn för blob-behållare][4]
5. Välj **Ja** i bekräftelsedialogen.

   ![Ta bort blob Container-bekräftelse][5]

## <a name="copy-a-blob-container"></a>Kopiera en blob-behållare
Storage Explorer kan du kopiera en blob-behållare till Urklipp och klistra in den blob-behållaren i ett annat lagringskonto. (Information om hur du kopiera enskilda blobar finns i avsnittet [hantera blobar i en blobbehållare](#managing-blobs-in-a-blob-container).)

Följande steg illustrerar hur du kopierar en blob-behållare från ett lagringskonto till ett annat.

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren du vill kopiera i den vänstra rutan.
3. Expandera lagringskontot **Blobbehållare**.
4. Högerklicka på blob-behållaren som du vill kopiera och på snabbmenyn - Välj **kopiera Blob-behållare**.

   ![Kopiera snabbmenyn för blob-behållare][6]
5. Högerklicka på det önskade ”target”-lagringskonto som du vill klistra in blob-behållaren och på snabbmenyn - Välj **klistra in Blob-behållare**.

   ![Snabbmenyn för Klistra in blob-behållare][7]

## <a name="get-the-sas-for-a-blob-container"></a>Hämta SAS för en blobcontainer
En [signatur för delad åtkomst (Shared Access Signature, SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) ger delegerad åtkomst till resurser på ditt lagringskonto.
Det innebär att du kan ge en klient begränsad behörighet till objekt på ditt lagringskonto under en angiven tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela nycklarna för åtkomst till ditt konto.

Följande steg illustrerar hur du skapar en SAS för en blob-behållare:

1. Öppna Storage Explorer.
2. I den vänstra rutan expanderar du det lagringskonto som innehåller blob-behållaren som du vill hämta en SAS.
3. Expandera lagringskontot **Blobbehållare**.
4. Högerklicka på önskad blob-behållaren och på snabbmenyn - Välj **hämta signatur för delad åtkomst**.

   ![Hämta SAS-snabbmeny][8]
5. I dialogrutan **Signatur för delad åtkomst** anger du princip, start- och förfallodatum, tidszon och åtkomstnivåer som du vill använda för resursen.

   ![Hämta SAS-alternativ][9]
6. När du är klar med att ange SAS-alternativen väljer du **Skapa**.
7. En andra **signatur för delad åtkomst** dialogrutan visas sedan som visar en lista över blob-behållaren tillsammans med URL: en och frågesträngar som du kan använda för att få åtkomst till lagringsresursen.
   Välj **Kopiera** bredvid den URL som du vill kopiera till Urklipp.

   ![Kopiera SAS URL: er][10]
8. När du är klar väljer du **Stäng**.

## <a name="manage-access-policies-for-a-blob-container"></a>Hantera åtkomstprinciper för en blob-behållare
Följande steg illustrerar hur du hanterar (lägger till och ta bort) åtkomstprinciper för en blob-behållare:

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren åtkomstprinciper som du vill hantera i den vänstra rutan.
3. Expandera lagringskontot **Blobbehållare**.
4. Välj önskad blob-behållaren och på snabbmenyn - Välj **hantera åtkomstprinciper**.

   ![Snabbmeny för hantering av åtkomstprinciper][11]
5. Den **åtkomstprinciper** dialogrutan visar en lista över alla åtkomstprinciper som redan har skapats för den valda blob-behållaren.

   ![Alternativ för principer åtkomst][12]        
6. Gör följande beroende på åtkomstprincipens hanteringsuppgift:

   * **Lägg till en ny åtkomstprincip** – Välj **Lägg till**. När åtkomstprinciepn har skapats visas den som nyligen tillagd i dialogen **Åtkomstprinciper** (med standardinställningarna).
   * **Redigera en åtkomstprincip** – gör önskade redigeringar och välj **spara**.
   * **Ta bort en åtkomstprincip** – Välj **Ta bort** bredvid den åtkomstprincip som du vill ta bort.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Ange offentlig åtkomstnivå för en blob-behållare
Varje blob-behållare är som standard ”ingen offentlig åtkomst”.

Följande steg illustrerar hur du anger en offentlig åtkomstnivå för en blob-behållare.

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren åtkomstprinciper som du vill hantera i den vänstra rutan.
3. Expandera lagringskontot **Blobbehållare**.
4. Välj önskad blob-behållaren och på snabbmenyn - Välj **ange offentlig åtkomstnivå**.

   ![Ange offentlig åtkomst på snabbmenyn][13]
5. I den **ange behållaren offentlig åtkomstnivå** dialogrutan Ange önskad åtkomstnivå.

   ![Ställa in offentlig åtkomst][14]
6. Välj **Använd**.

## <a name="managing-blobs-in-a-blob-container"></a>Hantera blobar i en blob-behållare
När du har skapat en blobbehållare kan du ladda upp en blob till den blob-behållaren, laddar ned en blob till den lokala datorn, öppna en blob på den lokala datorn och mycket mer.

Följande steg illustrerar hur du hanterar blobar (och mappar) inom en blob-behållare.

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren du vill hantera i den vänstra rutan.
3. Expandera lagringskontot **Blobbehållare**.
4. Dubbelklicka på blob-behållaren som du vill visa.
5. I huvudfönstret visas innehållet för blob-behållaren.

   ![Visa blob-behållare][3]
6. I huvudfönstret visas innehållet för blob-behållaren.
7. Gör följande beroende på vilken aktivitet du vill utföra:

   * **Ladda upp filer till en blob-behållare**

     1. Gå till verktygsfältet i huvudfönstret och välj **Överför**, och sedan **Överför filer** i den nedrullningsbara menyn.

        ![Menyn Ladda upp filer][15]
     2. I dialogen **Överför filer** dialogrutan klickar du på knappen med tre punkter (**...** ) på höger sida av textrutan **Filer** och markerar den eller de filer du vill överföra.

        ![Uppladdningsalternativ filer][16]
     3. Ange vilken typ av **typ av Blob**. Se [skapa behållaren och ange behörigheter](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) för mer information.
     4. Du kan också ange en målmapp där de markerade filerna ska överföras. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Ladda upp en mapp till en blob-behållare**

     1. Gå till verktygsfältet i huvudfönstret och klicka på **Överför**, och sedan på **Överför mapp** i den nedrullningsbara menyn.

        ![Menyn för mappöverföring][17]
     2. I dialogen **Överför mapp** klickar du på knappen med tre punkter (**...** ) på höger sida av textrutan **Mapp** och väljer den mapp vars innehåll du vill överföra.

        ![Ladda upp Mappalternativ][18]
     3. Ange vilken typ av **typ av Blob**. Se [skapa behållaren och ange behörigheter](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) för mer information.
     4. Du kan även ange en målmapp som den markerade mappens innehåll ska överföras till. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Ladda ned en blob till den lokala datorn**

     1. Välj den blob som du vill hämta.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
     3. I den **ange var du vill spara den hämta blobben** dialogrutan, ange den plats där du vill att blob hämtas och det namn du vill ge den.  
     4. Välj **Spara**.
   * **Öppna en blob på den lokala datorn**

     1. Välj den blob som du vill öppna.
     2. Gå till verktygsfältet i huvudfönstret och välj **Öppna**.
     3. Blobben som ska laddas ned och öppnas med det program som är associerade med blobens underliggande filtyp.
   * **Kopiera en blob till Urklipp**

     1. Välj den blob som du vill kopiera.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Kopiera**.
     3. Navigera till en annan blob-behållare i den vänstra rutan och dubbelklicka på den för att visa det i huvudfönstret.
     4. I huvudfönstrets verktygsfält väljer **klistra in** att skapa en kopia av blobben.
   * **Ta bort en blob**

     1. Välj den blob som du vill ta bort.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Ta bort**.
     3. Välj **Ja** i bekräftelsedialogen.

## <a name="next-steps"></a>Nästa steg
* Visa [viktig information och videor för den senaste Storage Explorer-versionen](http://www.storageexplorer.com).
* Läs mer om hur du [skapar program med Azure-blobbar, tabeller köer och filer](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
