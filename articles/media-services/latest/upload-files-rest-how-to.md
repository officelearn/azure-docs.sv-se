---
title: Ladda upp filer till ett Azure Media Services v3-konto med REST | Microsoft-dokument
description: Läs om hur du får in medieinnehåll i Media Services genom att skapa och ladda upp resurser.
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
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705776"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Ladda upp filer till ett Media Services v3-konto med REST

I Media Services laddar du upp dina digitala filer till en blob-behållare som är associerad med en tillgång. [Asset-entiteten](https://docs.microsoft.com/rest/api/media/operations/asset) kan innehålla video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text (och metadata om dessa filer). När filerna har överförts till tillgångens behållare lagras ditt innehåll säkert i molnet för vidare bearbetning och direktuppspelning.

I den här artikeln visas hur du laddar upp en lokal fil med REST.

## <a name="prerequisites"></a>Krav

För att kunna utföra stegen som beskrivs i det här avsnittet måste du:

- Granska [tillgångskoncept](assets-concept.md).
- [Konfigurera Postman för REST API-anrop för Azure Media Services](media-rest-apis-with-postman.md).
    
    Se till att följa det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Skapa en tillgång

I det här avsnittet visas hur du skapar en ny tillgång.

1. Välj **Tillgångar** -> **Skapa eller uppdatera en tillgång**.
2. Tryck på **Skicka**.

    ![Skapa en tillgång](./media/upload-files/postman-create-asset.png)

Du ser **svaret** med information om nyskapade tillgång.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Få en SAS-URL med läs-skrivbehörighet 

Det här avsnittet visar hur du skaffar en SAS-URL som genererades för den skapade tillgången. SAS-URL:en skapades med läs-skrivbehörighet och kan användas för att överföra digitala filer till tillgångsbehållaren.

1. Välj **Tillgångar** -> **Lista resursadresserna**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/upload-files/postman-create-sas-locator.png)

Du ser **svaret** med informationen om tillgångens webbadresser. Kopiera den första webbadressen och använd den för att ladda upp filen.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Ladda upp en fil till blob-lagring med hjälp av överförings-URL:en

Använd Azure Storage API:er eller SDK:er (till exempel [REST-API:et för lagring](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Nästa steg

[Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - REST](stream-files-tutorial-with-rest.md)
