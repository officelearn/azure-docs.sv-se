---
title: Överföra filer till ett Azure Media Services-konto med hjälp av REST | Microsoft Docs
description: Lär dig hur du hämtar medieinnehåll till Media Services genom att skapa och ladda upp tillgångar.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: juliako
ms.openlocfilehash: f7615911d39ea66cdcf5ed9913ac420f2d1c9de9
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636202"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Överföra filer till ett Media Services-konto med hjälp av REST

I Media Services överför du dina digitala filer till en blobbehållare som är associerade med en tillgång. Den [tillgången](https://docs.microsoft.com/rest/api/media/operations/asset) entiteten kan innehålla video, ljud, bilder, miniatyrsamlingar, textspår och dold textning filer (och metadata om dessa filer). När filerna överförs till den tillgången behållare, lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

Den här artikeln visar hur du laddar upp en lokal fil suing REST.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i det här avsnittet måste ha du till:

- Granska [tillgången konceptet](assets-concept.md).
- [Konfigurera Postman för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).
    
    Se till att följa det sista steget i avsnittet [hämta Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Skapa en tillgång

Det här avsnittet visar hur du skapar en ny tillgång.

1. Välj **tillgångar** -> **skapa eller uppdatera en tillgång**.
2. Tryck på **Skicka**.

    ![Skapa en tillgång](./media/upload-files/postman-create-asset.png)

Du ser den **svar** med information om nyskapad tillgång.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Få en SAS-URL med läs-och behörigheter 

Det här avsnittet visar hur du hämtar en SAS-URL som har genererats för den skapa tillgången. SAS-Webbadressen har skapats med skrivskyddad behörighet och kan användas för att ladda upp digitala filer till tillgången behållaren.

1. Välj **tillgångar** -> **lista över URL: er för tillgången**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/upload-files/postman-create-sas-locator.png)

Du ser den **svar** med information om tillgångens URL: er. Kopiera den första URL och använda den för att överföra din fil.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Överföra en fil till blob storage med hjälp av URL

Använda Azure Storage API: er eller SDK: er (till exempel den [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Nästa steg

[Självstudier: Koda en fjärrfil baserat på URL: en och strömma video – REST](stream-files-tutorial-with-rest.md)
