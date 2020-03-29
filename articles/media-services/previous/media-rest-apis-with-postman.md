---
title: Konfigurera REST-API-anrop för Postman för Azure Media Services
description: I den här artikeln beskrivs hur du konfigurerar REST API-anrop för Postman för Media Services REST.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76694998"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Konfigurera Postman för Media Services v2 REST API-anrop  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Den här självstudien visar hur du konfigurerar **Postman** så att den kan användas för att anropa AZURE Media Services (AMS) REST API:er. Självstudien visar hur du importerar miljö- och samlingsfiler till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar AZURE Media Services (AMS) REST API:er. Miljöfilen innehåller variabler som används av samlingen.

Den här miljön och samlingen används i artiklar som visar hur du uppnår olika uppgifter med AZURE Media Services REST API:er.

## <a name="prerequisites"></a>Krav

- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med REST plugin eller **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurera miljön 

1. Skapa en Json-fil som innehåller de miljövariabler som används i AMS-självstudier. Namnge filen (till exempel **AzureMediaServices.postman_environment.json**). Öppna filen och klistra in koden som definierar Postman-miljön från [den här kodlistan](postman-environment.md). 
2. Öppna **Postman**.
3. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
5. Bläddra bland och välj filen **AzureMediaServices.postman_environment.json.**
6. **AzureMedia-miljön** läggs till.
7. Stäng dialogrutan.
8. Välj **AzureMedia-miljön.**

    ![Överför en fil](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurera samlingen

1. Skapa en Json-fil som innehåller **Postman-samlingen** med alla åtgärder som behövs för att överföra en fil till Media Services. Namnge filen (till exempel **AzureMediaServicesOperations.postman_collection.json**). Öppna filen och klistra in koden som definierar **Postman-samlingen** från [den här kodlistan](postman-collection.md).
2. Klicka på **Importera** för att importera samlingsfilen.
3. Välj filen **AzureMediaServicesOperations.postman_collection.json.**

    ![Överför en fil](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Nästa steg

Kolla in artikeln [överladdningstillgångar.](media-services-rest-upload-files.md)  
