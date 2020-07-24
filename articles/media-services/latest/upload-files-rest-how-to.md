---
title: Ladda upp filer till ett Azure Media Services v3-konto med REST | Microsoft Docs
description: Lär dig hur du hämtar medie innehåll i Media Services genom att skapa och ladda upp till gångar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 57839c82af71f7cedfe1b3c77095210d273be4a1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091852"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Ladda upp filer till ett Media Services v3-konto med REST

I Media Services laddar du upp dina digitala filer till en BLOB-behållare som är kopplad till en till gång. [Till gångs](/rest/api/media/operations/asset) enheten kan innehålla video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning (samt metadata om dessa filer). När filerna har överförts till till gångens behållare lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

Den här artikeln visar hur du laddar upp en lokal fil med hjälp av REST.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i det här avsnittet måste du:

- Granska [till gångs koncept](assets-concept.md).
- [Konfigurera PostMan för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).
    
    Kontrol lera att du följer det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Skapa en tillgång

I det här avsnittet visas hur du skapar en ny till gång.

1. Välj **till gångar**  ->  **skapa eller uppdatera en till gång**.
2. Tryck på **Skicka**.

    ![Skapa en tillgång](./media/upload-files/postman-create-asset.png)

**Svaret** visas med information om nyligen skapade till gångar.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Hämta en SAS-URL med Läs-och Skriv behörighet 

I det här avsnittet visas hur du hämtar en SAS-URL som har skapats för den skapade till gången. SAS-URL: en skapades med Läs-och Skriv behörighet och kan användas för att överföra digitala filer till till gångs behållaren.

1. Välj **till gångar**  ->  **listar URL: erna till till**gångar.
2. Tryck på **Skicka**.

    ![Ladda upp en fil](./media/upload-files/postman-create-sas-locator.png)

Du ser **svaret** med informationen om till gångens URL: er. Kopiera den första URL: en och Använd den för att ladda upp filen.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Ladda upp en fil till Blob Storage med uppladdnings-URL

Använd Azure Storage-API: er eller SDK: er (till exempel [lagrings REST API](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Nästa steg

[Självstudier: Koda en fjärrfil baserat på URL och strömma videon – REST](stream-files-tutorial-with-rest.md)
