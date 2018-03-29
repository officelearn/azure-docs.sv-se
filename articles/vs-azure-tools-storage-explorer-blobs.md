---
title: Hantera Azure Blob Storage-resurser med Lagringsutforskaren (förhandsversion) | Microsoft Docs
description: Hantera Azure Blob-behållare och Blobbar med Lagringsutforskaren (förhandsversion)
services: storage
documentationcenter: na
author: cawa
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
ms.openlocfilehash: 98a1016f78587b4139041f140634b98fa47edfd1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Hantera Azure Blob Storage-resurser med Lagringsutforskaren (förhandsversion)
## <a name="overview"></a>Översikt
[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) är en tjänst för att lagra stora mängder Ostrukturerade data, till exempel text eller binära data som kan nås från var som helst i världen via HTTP eller HTTPS.
Du kan använda Blob Storage för att exponera data offentligt eller lagra programdata privat. I den här artikeln lär du dig att använda Lagringsutforskaren (förhandsversion) att arbeta med blob-behållare och blobbar.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra stegen i den här artikeln:

* [Hämta och installera Lagringsutforskaren (förhandsversion)](http://www.storageexplorer.com)
* [Ansluta till ett Azure-lagringskonto eller en Azure-lagringstjänst](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Skapa en blobbehållare
Alla blobbar måste finnas i en blobbbehållare som är en logisk gruppering av blobbar. Ett konto kan innehålla ett obegränsat antal behållare, och varje behållare kan lagra ett obegränsat antal blobbar.

Följande steg visar hur du skapar en blobbbehållare i Lagringsutforskaren (förhandsversion).

1. Öppna Lagringsutforskaren (förhandsversion).
2. Expandera det lagringskonto där du vill skapa blob-behållaren i den vänstra rutan.
3. Högerklicka på **Blobbbehållare**, och på snabbmenyn - väljer **skapa Blob-behållaren**.

   ![Skapa snabbmenyn för blob-behållare][0]
4. En textruta ska visas under den **Blobbbehållare** mapp. Ange namnet på blob-behållaren. Finns det [skapa behållaren och ange behörigheter](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) information om regler och begränsningar för namngivning av blob-behållare.

   ![Skapa Blob-behållare textruta][1]
5. Tryck på **RETUR** när du är klar för att skapa blob-behållare eller **Esc** att avbryta. När blob-behållaren har skapats visas den den **Blobbbehållare** mapp för det valda lagringskontot.

   ![BLOB-behållare skapas][2]

## <a name="view-a-blob-containers-contents"></a>Visa innehållet i en blob-behållare
BLOB-behållare innehåller blobbar och mappar (som kan också innehålla BLOB).

Följande steg visar hur du visar innehållet i en blobbbehållare i Lagringsutforskaren (förhandsversion):

1. Öppna Lagringsutforskaren (förhandsversion).
2. Expandera det lagringskonto som innehåller blob-behållare du vill visa i den vänstra rutan.
3. Expandera lagringskontot **Blobbbehållare**.
4. Högerklicka på blob-behållare som du vill visa, och på snabbmenyn - väljer **Öppna Redigeraren för Blob-behållaren**.
   Du kan dubbelklicka på blob-behållare som du vill visa.

   ![Öppna blob-behållaren editor snabbmenyn][19]
5. I huvudfönstret visas innehållet för blob-behållaren.

   ![Redigeraren för BLOB-behållare][3]

## <a name="delete-a-blob-container"></a>Ta bort en blob-behållare
BLOB-behållare kan enkelt skapas och tas bort efter behov. (Att se hur finns i avsnittet om du vill ta bort enskilda blobbar [hantera blobbar i en blobbbehållare](#managing-blobs-in-a-blob-container).)

Följande steg visar hur du tar bort en blobbbehållare i Lagringsutforskaren (förhandsversion):

1. Öppna Lagringsutforskaren (förhandsversion).
2. Expandera det lagringskonto som innehåller blob-behållare du vill visa i den vänstra rutan.
3. Expandera lagringskontot **Blobbbehållare**.
4. Högerklicka på blob-behållare som du vill ta bort, och på snabbmenyn - väljer **ta bort**.
   Du kan även trycka **ta bort** att ta bort markerade blob-behållaren.

   ![Ta bort snabbmenyn för blob-behållare][4]
5. Välj **Ja** i bekräftelsedialogen.

   ![Ta bort blobben behållare bekräftelse][5]

## <a name="copy-a-blob-container"></a>Kopiera en blob-behållare
Lagringsutforskaren (förhandsversion) kan du kopiera en blob-behållare till Urklipp och klistra in den blob-behållaren i ett annat lagringskonto. (Se hur du kopiera enskilda blobbar, finns i avsnittet [hantera blobbar i en blobbbehållare](#managing-blobs-in-a-blob-container).)

Följande steg visar hur du kopierar en blob-behållare från ett lagringskonto till en annan.

1. Öppna Lagringsutforskaren (förhandsversion).
2. I den vänstra rutan, expanderar du lagringskontot som innehåller blob-behållare du vill kopiera.
3. Expandera lagringskontot **Blobbbehållare**.
4. Högerklicka på blob-behållare som du vill kopiera, och på snabbmenyn - väljer **kopiera Blob-behållaren**.

   ![Kopiera blob-behållaren snabbmenyn][6]
5. Högerklicka på önskat ”mål” storage-konto som du vill klistra in blob-behållare, och på snabbmenyn - väljer **klistra in Blob-behållaren**.

   ![Klistra in snabbmenyn för blob-behållare][7]

## <a name="get-the-sas-for-a-blob-container"></a>Hämta SAS för en blobbbehållare
En [signatur för delad åtkomst (Shared Access Signature, SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) ger delegerad åtkomst till resurser på ditt lagringskonto.
Det innebär att du kan ge en klient begränsad behörighet till objekt på ditt lagringskonto under en angiven tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela nycklarna för åtkomst till ditt konto.

Följande steg visar hur du skapar en SAS för en blob-behållare:

1. Öppna Lagringsutforskaren (förhandsversion).
2. I den vänstra rutan, expanderar du lagringskontot som innehåller blob-behållare som du vill hämta en SAS.
3. Expandera lagringskontot **Blobbbehållare**.
4. Högerklicka på den önskade blobbehållaren, och på snabbmenyn - väljer **hämta signatur för delad åtkomst**.

   ![Hämta SAS snabbmenyn][8]
5. I dialogrutan **Signatur för delad åtkomst** anger du princip, start- och förfallodatum, tidszon och åtkomstnivåer som du vill använda för resursen.

   ![Hämta SAS-alternativ][9]
6. När du är klar med att ange SAS-alternativen väljer du **Skapa**.
7. En andra **signatur för delad åtkomst** dialogrutan därefter visas med en lista över blob-behållaren tillsammans med URL och QueryStrings som du kan använda för att komma åt storage-resurs.
   Välj **Kopiera** bredvid den URL som du vill kopiera till Urklipp.

   ![Kopiera SAS-URL: er][10]
8. När du är klar väljer du **Stäng**.

## <a name="manage-access-policies-for-a-blob-container"></a>Hantera principer för åtkomst för en blob-behållare
Nedan visas hur du hanterar (lägga till och ta bort) åtkomstprinciper för en blob-behållare:

1. Öppna Lagringsutforskaren (förhandsversion).
2. I den vänstra rutan, expanderar du lagringskontot som innehåller blob-behållaren vars åtkomstprinciper som du vill hantera.
3. Expandera lagringskontot **Blobbbehållare**.
4. Välj önskad blob-behållaren och - snabbmenyn - **hantera åtkomstprinciper**.

   ![Snabbmeny för hantering av åtkomstprinciper][11]
5. Den **åtkomstprinciper** dialogrutan visar en lista över alla åtkomstprinciper som redan har skapats för valda blob-behållaren.

   ![Alternativ för åtkomstprincipen][12]        
6. Gör följande beroende på åtkomstprincipens hanteringsuppgift:

   * **Lägg till en ny åtkomstprincip** – Välj **Lägg till**. När åtkomstprinciepn har skapats visas den som nyligen tillagd i dialogen **Åtkomstprinciper** (med standardinställningarna).
   * **Redigera en åtkomstprincip** - göra alla önskade ändringar, och välj **spara**.
   * **Ta bort en åtkomstprincip** – Välj **Ta bort** bredvid den åtkomstprincip som du vill ta bort.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Att ställa in allmän åtkomst för en blob-behållare
Varje blob-behållaren är som standard ”ingen offentlig åtkomst”.

Följande steg visar hur du anger en offentlig åtkomstnivån för en blob-behållare.

1. Öppna Lagringsutforskaren (förhandsversion).
2. I den vänstra rutan, expanderar du lagringskontot som innehåller blob-behållaren vars åtkomstprinciper som du vill hantera.
3. Expandera lagringskontot **Blobbbehållare**.
4. Välj önskad blob-behållaren och - snabbmenyn - **ange offentliga åtkomstnivå**.

   ![Ange allmän åtkomst på snabbmenyn][13]
5. I den **ange behållaren offentliga åtkomstnivå** dialogrutan Ange önskad åtkomstnivå.

   ![Ställa in allmän åtkomst][14]
6. Välj **Använd**.

## <a name="managing-blobs-in-a-blob-container"></a>Hantera blobbar i en blobbbehållare
När du har skapat en blob-behållare kan du ladda upp en blobb till att blob-behållare, ladda ned en blob till den lokala datorn, öppna en blob på den lokala datorn och mycket mer.

Följande steg visar hur du hanterar blobbar (och mappar) i en blobbbehållare.

1. Öppna Lagringsutforskaren (förhandsversion).
2. I den vänstra rutan, expanderar du lagringskontot som innehåller blob-behållare du vill hantera.
3. Expandera lagringskontot **Blobbbehållare**.
4. Dubbelklicka på blob-behållare som du vill visa.
5. I huvudfönstret visas innehållet för blob-behållaren.

   ![Visa blob-behållare][3]
6. I huvudfönstret visas innehållet för blob-behållaren.
7. Gör följande beroende på vilken aktivitet du vill utföra:

   * **Ladda upp filer till en blob-behållare**

     1. Gå till verktygsfältet i huvudfönstret och välj **Överför**, och sedan **Överför filer** i den nedrullningsbara menyn.

        ![Menyn Ladda upp filer][15]
     2. I dialogen **Överför filer** dialogrutan klickar du på knappen med tre punkter (**...** ) på höger sida av textrutan **Filer** och markerar den eller de filer du vill överföra.

        ![Ladda upp filer alternativ][16]
     3. Ange vilken typ av **Blob typen**. Se [skapa behållaren och ange behörigheter](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) för mer information.
     4. Du kan också ange målmapp som de markerade filerna laddas upp. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Ladda upp en mapp till en blob-behållare**

     1. Gå till verktygsfältet i huvudfönstret och klicka på **Överför**, och sedan på **Överför mapp** i den nedrullningsbara menyn.

        ![Menyn för mappöverföring][17]
     2. I dialogen **Överför mapp** klickar du på knappen med tre punkter (**...** ) på höger sida av textrutan **Mapp** och väljer den mapp vars innehåll du vill överföra.

        ![Överför Mappalternativ][18]
     3. Ange vilken typ av **Blob typen**. Se [skapa behållaren och ange behörigheter](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) för mer information.
     4. Du kan även ange en målmapp som den markerade mappens innehåll ska överföras till. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Hämta en blobb till den lokala datorn**

     1. Välj blob som du vill hämta.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
     3. I den **ange var du vill spara den hämta blobben** dialogrutan, ange den plats där du vill blob hämtas och du vill ge den namnet.  
     4. Välj **Spara**.
   * **Öppna en blob på den lokala datorn**

     1. Välj blob som du vill öppna.
     2. Gå till verktygsfältet i huvudfönstret och välj **Öppna**.
     3. Blob kommer att hämtas och öppnas med hjälp av programmet som är associerade med den blob underliggande filtyp.
   * **Kopiera en blobb till Urklipp**

     1. Välj blob som du vill kopiera.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Kopiera**.
     3. Navigera till en annan blob-behållaren i den vänstra rutan, och dubbelklicka på den om du vill visa i huvudfönstret.
     4. I huvudfönstret i verktygsfältet väljer **klistra in** att skapa en kopia av blobben.
   * **Ta bort en blobb**

     1. Välj blob som du vill ta bort.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Ta bort**.
     3. Välj **Ja** i bekräftelsedialogen.

## <a name="next-steps"></a>Nästa steg
* Visa [ viktig information och videor för den senaste Storage Explorer-versionen (förhandsutgåva)](http://www.storageexplorer.com).
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
