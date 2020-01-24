---
title: Konfigurera PostMan för Azure Media Services REST API-anrop
description: I den här artikeln beskrivs hur du konfigurerar Postman för Media Services REST API samtal.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 11c9c26e7c0f36e1e3dba732e90a6aef95e6ee14
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694998"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Konfigurera PostMan för Media Services v2 REST API anrop  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [vägledning för migrering från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Den här självstudien visar hur du konfigurerar **Postman** så att den kan användas för att anropa Azure Media Services REST-API: er för (AMS). I självstudien visas hur du importerar miljö-och samlings filer till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure Media Services (AMS) REST-API: er. Miljöfilen innehåller variabler som används av samlingen.

Den här miljön och samlingen används i artiklar som visar hur du uppnår olika uppgifter med Azure Media Services REST-API: er.

## <a name="prerequisites"></a>Krav

- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med plugin-programmet för REST eller **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurera miljön 

1. Skapa en. JSON-fil som innehåller miljövariabler som används i AMS-självstudier. Namnge filen (till exempel **AzureMediaServices. postman_environment. JSON**). Öppna filen och klistra in koden som definierar Postman-miljön från [den här kod listan](postman-environment.md). 
2. Öppna **Postman**.
3. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
5. Bläddra och välj filen **AzureMediaServices. postman_environment. JSON** .
6. **AzureMedia** -miljön har lagts till.
7. Stäng dialogrutan.
8. Välj **AzureMedia** -miljön.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurera samlingen

1. Skapa en. JSON-fil som innehåller **Postman** -samlingen med alla åtgärder som behövs för att ladda upp en fil till Media Services. Namnge filen (till exempel **AzureMediaServicesOperations. postman_collection. JSON**). Öppna filen och klistra in koden som definierar **Postman** -samlingen från [den här kod listan](postman-collection.md).
2. Klicka på **Importera** för att importera samlingsfilen.
3. Välj filen **AzureMediaServicesOperations. postman_collection. JSON** .

    ![Överför en fil](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Nästa steg

Ta en titt på artikeln [upload assets](media-services-rest-upload-files.md) .  
