---
title: "Konfigurera Postman för Azure Media Services REST API-anrop"
description: "Lär dig hur du konfigurerar Postman för Media Services REST API-anrop."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: juliako
ms.openlocfilehash: a24b73a93bddbeb5b56ddfdf604fa99faccea442
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurera Postman för Media Services REST API-anrop

Den här kursen visar hur du konfigurerar **Postman** så att den kan användas för att anropa Azure Media Services (AMS) REST API: er. Kursen får du veta hur du importerar miljö och samling filerna till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure Media Services (AMS) REST API: er. Filen miljö innehåller variabler som används av samlingen.

Den här miljön och samling används i artiklar som visar hur du utför olika uppgifter med Azure Media Services REST API: er.

## <a name="prerequisites"></a>Förutsättningar

- Installera den [Postman](https://www.getpostman.com/) REST-klient för att köra REST API: er visas i några av följande självstudiekurser för AMS REST. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämplig. Andra alternativ är: **Visual Studio Code** med plugin-programmet för REST eller **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurera miljön 

1. Skapa en JSON-fil som innehåller miljövariabler som används i AMS självstudier. Namn på filen **AzureMediaServices.postman_environment.json**. Öppna filen och klistra in koden som definierar Postman miljön från [den här koden](postman-environment.md). 
2. Öppna den **Postman**.
3. Till höger på skärmen, Välj den **hantera miljön** alternativet.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-env.png)
4. Från den **hantera miljön** dialogrutan klickar du på **importera**.
5. Bläddra och välj den **AzureMediaServices.postman_environment.json** fil.
6. Den **AzureMedia** miljö har lagts till.
7. Stäng dialogrutan.
8. Välj den **AzureMedia** miljö.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurera samlingen

1. Skapa en JSON-fil som innehåller den **Postman** samling med alla åtgärder som behövs för att överföra en fil till Media Services. Namn på filen **AzureMediaServicesOperations.postman_collection.json**. Öppna filen och klistra in koden som definierar den **Postman** samling från [den här koden](postman-collection.md).
2. Klicka på **importera** importeras samling.
3. Välj den **AzureMediaServicesOperations.postman_collection.json** fil.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Nästa steg

Kolla in den [Överför tillgångar](media-services-rest-upload-files.md) artikel.  
