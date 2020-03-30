---
title: Hantera Azure Blob Storage-resurser med Storage Explorer | Microsoft-dokument
description: Hantera Azure Blob-behållare och blobbar med Storage Explorer
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
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244399"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Hantera Azure Blob Storage-resurser med Storage Explorer

## <a name="overview"></a>Översikt

[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) är en tjänst för lagring av stora mängder ostrukturerade data, till exempel text eller binära data, som kan nås var som helst i världen via HTTP eller HTTPS.
Du kan använda Blob Storage för att exponera data offentligt eller lagra programdata privat. I den här artikeln får du lära dig hur du använder Storage Explorer för att arbeta med blob-behållare och blobbar.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra stegen i den här artikeln:

* [Hämta och installera Storage Explorer](https://www.storageexplorer.com)
* [Ansluta till ett Azure-lagringskonto eller -tjänst](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Skapa en blobcontainer

Alla blobbar måste finnas i en blob-behållare, vilket helt enkelt är en logisk gruppering av blobbar. Ett konto kan innehålla ett obegränsat antal behållare och varje behållare kan lagra ett obegränsat antal blobbar.

Följande steg illustrerar hur du skapar en blob-behållare i Storage Explorer.

1. Öppna Storage Explorer.
2. I den vänstra fönsterrutan expanderar du det lagringskonto där du vill skapa blob-containern.
3. Högerklicka på **Blob Containers**och - på snabbmenyn - välj **Skapa Blob Container**.

   ![Snabbmeny för att skapa blob-containrar][0]
4. En textruta visas under mappen **Blob Containers.** Ange namnet på blob-containern. Se [Skapa en behållare](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) för information om regler och begränsningar för namngivning av blob-behållare.

   ![Skapa textruta för blobbehållare][1]
5. Tryck på **Retur** när du är klar så att blob-containern skapas eller på **Esc** om du vill avbryta. När blob-behållaren har skapats visas den under mappen **Blob Containers** för det valda lagringskontot.

   ![Blob Container har skapats][2]

## <a name="view-a-blob-containers-contents"></a>Visa innehållet i en blob-behållare

Blob-behållare innehåller blobbar och mappar (som också kan innehålla blobbar).

Följande steg illustrerar hur du visar innehållet i en blob-behållare i Storage Explorer:

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller den blob-behållare som du vill visa i den vänstra rutan.
3. Expandera lagringskontots **Blob-behållare**.
4. Högerklicka på den blob-behållare som du vill visa och välj **Öppna Blob Container Editor**på snabbmenyn .
   Du kan också dubbelklicka på den blob-behållare som du vill visa.

   ![Öppna snabbmenyn för snabbmeny för behållare med blobbehållare][19]
5. Huvudfönstret visar blob-behållarens innehåll.

   ![Blob-behållareredigeraren][3]

## <a name="delete-a-blob-container"></a>Ta bort en blob-behållare

Blob-behållare kan enkelt skapas och tas bort efter behov. (Mer information om hur du tar bort enskilda blobbar finns i avsnittet [Hantera blobbar i en blob-behållare](#managing-blobs-in-a-blob-container).)

Följande steg illustrerar hur du tar bort en blob-behållare i Storage Explorer:

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller den blob-behållare som du vill visa i den vänstra rutan.
3. Expandera lagringskontots **Blob-behållare**.
4. Högerklicka på den blob-behållare som du vill ta bort och välj **Ta bort**på snabbmenyn .
   Du kan också trycka på **Ta bort** för att ta bort den markerade blob-behållaren.

   ![Ta bort snabbmeny för blob-behållare][4]
5. Välj **Ja** i bekräftelsedialogen.

   ![Ta bort bekräftelse av blob-behållare][5]

## <a name="copy-a-blob-container"></a>Kopiera en blob-behållare

Med Storage Explorer kan du kopiera en blob-behållare till Urklipp och sedan klistra in blob-behållaren i ett annat lagringskonto. (Mer information om hur du kopierar enskilda blobbar finns i avsnittet [Hantera blobbar i en blob-behållare](#managing-blobs-in-a-blob-container).)

Följande steg illustrerar hur du kopierar en blob-behållare från ett lagringskonto till ett annat.

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller den blob-behållare som du vill kopiera i den vänstra rutan.
3. Expandera lagringskontots **Blob-behållare**.
4. Högerklicka på den blob-behållare som du vill kopiera och välj **Kopiera blob-behållare**på snabbmenyn .

   ![Snabbmeny för kopia av blob-behållare][6]
5. Högerklicka på önskat "målkonto" där du vill klistra in blob-behållaren och välj Klistra **in Blob Container**på snabbmenyn .

   ![Snabbmeny för inklistringsbehållare][7]

## <a name="get-the-sas-for-a-blob-container"></a>Hämta SAS för en blobcontainer

En [signatur för delad åtkomst (Shared Access Signature, SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) ger delegerad åtkomst till resurser på ditt lagringskonto.
Det innebär att du kan ge en klient begränsad behörighet till objekt på ditt lagringskonto under en angiven tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela nycklarna för åtkomst till ditt konto.

Följande steg illustrerar hur du skapar en SAS för en blob-behållare:

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren som du vill hämta en SAS för i den vänstra rutan.
3. Expandera lagringskontots **Blob-behållare**.
4. Högerklicka på önskad blob-behållare och - på snabbmenyn - välj **Hämta signatur för delad åtkomst**.

   ![Snabbmenyn för SAS][8]
5. I dialogrutan **Signatur för delad åtkomst** anger du princip, start- och förfallodatum, tidszon och åtkomstnivåer som du vill använda för resursen.

   ![Hämta SAS-alternativ][9]
6. När du är klar med att ange SAS-alternativen väljer du **Skapa**.
7. En andra dialogruta för signature för **delad åtkomst** visas sedan som visar blob-behållaren tillsammans med den URL och Frågesträngar som du kan använda för att komma åt lagringsresursen.
   Välj **Kopiera** bredvid den URL som du vill kopiera till Urklipp.

   ![Kopiera SAS-url:er][10]
8. När du är klar väljer du **Stäng**.

## <a name="manage-access-policies-for-a-blob-container"></a>Hantera åtkomstprinciper för en blob-behållare

Följande steg illustrerar hur du hanterar (lägger till och tar bort) åtkomstprinciper för en blob-behållare:

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren i den vänstra rutan.
3. Expandera lagringskontots **Blob-behållare**.
4. Välj önskad blob-behållare och välj - på snabbmenyn - **Hantera åtkomstprinciper**.

   ![Snabbmeny för hantering av åtkomstprinciper][11]
5. I dialogrutan **Åtkomstprinciper** visas alla åtkomstprinciper som redan har skapats för den valda blob-behållaren.

   ![Alternativ för åtkomstprincip][12]
6. Gör följande beroende på åtkomstprincipens hanteringsuppgift:

   * **Lägg till en ny åtkomstprincip** – Välj **Lägg till**. När åtkomstprinciepn har skapats visas den som nyligen tillagd i dialogen **Åtkomstprinciper** (med standardinställningarna).
   * **Redigera en åtkomstprincip** - Gör önskade ändringar och välj **Spara**.
   * **Ta bort en åtkomstprincip** – Välj **Ta bort** bredvid den åtkomstprincip som du vill ta bort.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Ange offentlig åtkomstnivå för en blob-behållare

Som standard är varje blob-behållare inställd på "Ingen offentlig åtkomst".

Följande steg illustrerar hur du anger en offentlig åtkomstnivå för en blob-behållare.

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller blob-behållaren i den vänstra rutan.
3. Expandera lagringskontots **Blob-behållare**.
4. Välj önskad blob-behållare och välj - på snabbmenyn - **Ange offentlig åtkomstnivå**.

   ![Ange snabbmeny på nivån för offentlig åtkomst][13]
5. Ange önskad åtkomstnivå i dialogrutan **Ange offentlig åtkomstnivå** för behållare.

   ![Ange alternativ för nivån för offentlig åtkomst][14]
6. Välj **Använd**.

## <a name="managing-blobs-in-a-blob-container"></a>Hantera blobbar i en blob-behållare

När du har skapat en blob-behållare kan du ladda upp en blob till den blob-behållaren, hämta en blob till din lokala dator, öppna en blob på den lokala datorn och mycket mer.

Följande steg illustrerar hur du hanterar blobbar (och mappar) i en blob-behållare.

1. Öppna Storage Explorer.
2. Expandera lagringskontot som innehåller den blob-behållare som du vill hantera i den vänstra rutan.
3. Expandera lagringskontots **Blob-behållare**.
4. Dubbelklicka på den blob-behållare som du vill visa.
5. Huvudfönstret visar blob-behållarens innehåll.

   ![Visa blob-behållare][3]
6. Huvudfönstret visar blob-behållarens innehåll.
7. Gör följande beroende på vilken aktivitet du vill utföra:

   * **Ladda upp filer till en blob-behållare**

     1. Gå till verktygsfältet i huvudfönstret och välj **Överför**, och sedan **Överför filer** i den nedrullningsbara menyn.

        ![Menyn Ladda upp filer][15]
     2. I dialogen **Överför filer** dialogrutan klickar du på knappen med tre punkter (**... **) på höger sida av textrutan **Filer** och markerar den eller de filer du vill överföra.

        ![Alternativ för ladda upp filer][16]
     3. Ange typ av **Blob-typ**. Mer information finns i [Skapa en behållare.](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)
     4. Du kan också ange en målmapp där de valda filerna ska överföras. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Ladda upp en mapp till en blob-behållare**

     1. Gå till verktygsfältet i huvudfönstret och klicka på **Överför**, och sedan på **Överför mapp** i den nedrullningsbara menyn.

        ![Menyn för mappöverföring][17]
     2. I dialogen **Överför mapp** klickar du på knappen med tre punkter (**... **) på höger sida av textrutan **Mapp** och väljer den mapp vars innehåll du vill överföra.

        ![Alternativ för ladda upp mapp][18]
     3. Ange typ av **Blob-typ**. Mer information finns i [Skapa en behållare.](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)
     4. Du kan även ange en målmapp som den markerade mappens innehåll ska överföras till. Om målmappen inte finns skapas den.
     5. Välj **Överför**.
   * **Ladda ned en blob till din lokala dator**

     1. Välj den blob som du vill hämta.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
     3. I dialogrutan **Ange var du vill spara den nedladdade blob-dialogrutan** anger du den plats där du vill att bloben ska hämtas och vilket namn du vill ge den.  
     4. Välj **Spara**.
   * **Öppna en blob på den lokala datorn**

     1. Välj den blob som du vill öppna.
     2. Gå till verktygsfältet i huvudfönstret och välj **Öppna**.
     3. Blobben hämtas och öppnas med hjälp av programmet som är associerat med blobens underliggande filtyp.
   * **Kopiera en blob till Urklipp**

     1. Välj den blob som du vill kopiera.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Kopiera**.
     3. I den vänstra rutan navigerar du till en annan blob-behållare och dubbelklickar på den för att visa den i huvudfönstret.
     4. I verktygsfältet i huvudfönstret väljer du **Klistra in** om du vill skapa en kopia av bloben.
   * **Ta bort en blob**

     1. Välj den blob som du vill ta bort.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Ta bort**.
     3. Välj **Ja** i bekräftelsedialogen.

## <a name="next-steps"></a>Nästa steg

* Visa [viktig information och videor för den senaste Storage Explorer-versionen](https://www.storageexplorer.com).
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
