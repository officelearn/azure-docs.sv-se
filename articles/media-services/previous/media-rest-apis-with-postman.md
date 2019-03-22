---
title: Konfigurera Postman för Azure Media Services REST API-anrop
description: Lär dig hur du konfigurerar Postman för Media Services REST API-anrop.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: da52827107fcf1672047644d2d5ac5ed2e6beed5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007758"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurera Postman för Media Services REST API-anrop  

Den här självstudien visar hur du konfigurerar **Postman** så att den kan användas för att anropa Azure Media Services (AMS) REST API: er. Kursen visas hur du importerar miljö och samling filerna till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure Media Services (AMS) REST API: er. Miljö-filen innehåller variabler som används av samlingen.

Den här miljön och samling används i artiklar som visar hur du utför olika uppgifter med Azure Media Services REST API: er.

## <a name="prerequisites"></a>Förutsättningar

- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med plugin-programmet för REST eller **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurera miljön 

1. Skapa en JSON-fil som innehåller de miljövariabler som används i AMS-självstudier. Ge filen namnet (till exempel **AzureMediaServices.postman_environment.json**). Öppna filen och klistra in koden som definierar den Postman-miljön från [den här koden](postman-environment.md). 
2. Öppna **Postman**.
3. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
5. Bläddra och välj den **AzureMediaServices.postman_environment.json** fil.
6. Den **AzureMedia** miljö har lagts till.
7. Stäng dialogrutan.
8. Välj den **AzureMedia** miljö.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurera samlingen

1. Skapa en JSON-fil som innehåller den **Postman** samling med alla åtgärder som behövs för att överföra en fil till Media Services. Ge filen namnet (till exempel **AzureMediaServicesOperations.postman_collection.json**). Öppna filen och klistra in koden som definierar den **Postman** samling från [den här koden](postman-collection.md).
2. Klicka på **Importera** för att importera samlingsfilen.
3. Välj den **AzureMediaServicesOperations.postman_collection.json** fil.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Nästa steg

Kolla in den [ladda upp resurser](media-services-rest-upload-files.md) artikeln.  
