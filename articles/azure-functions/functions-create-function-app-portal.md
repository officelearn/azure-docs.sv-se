---
title: Skapa en funktionsapp från Azure Portal | Microsoft Docs
description: Skapa en ny funktionsapp i Azure App Service från portalen.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: quickstart
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: eb362ec652b306a12a41e7e96dcbc86638369c17
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085893"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Skapa en funktionsapp från Azure Portal

Azure-funktionsappar använder Azure App Service-infrastrukturen. I det här avsnittet visar vi hur du skapar en funktionsapp i Azure Portal. En funktionsapp är en container som är värd för körningen av enskilda funktioner. När du skapar en funktionsapp i App Service-värdplanen kan din funktionsapp använda alla funktioner i App Service.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

När du skapar en funktionsapp måste du ange ett giltigt **appnamn**. Namnet kan endast innehålla bokstäver, siffror och bindestreck. Understreck ( **_** ) är inte tillåtna.

Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Namnet på ditt lagringskonto måste vara unikt i Azure. 

När du har skapat funktionsappen kan du skapa enskilda funktioner på ett eller flera olika språk. Skapa funktioner [med hjälp av portalen](functions-create-first-azure-function.md#create-function), [kontinuerlig distribution](functions-continuous-deployment.md) eller [överföring med FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Tjänstplaner

Azure Functions har två olika tjänstplaner: Förbrukningsplan och App Service-plan. Med förbrukningsplanen allokeras beräkningskraften automatiskt när koden körs. Utskalning sker när det behövs för att hantera belastningen och sedan görs en nedskalning när koden inte körs. Med App Service-planen får din funktionsapp tillgång till alla funktioner i App Service. Du måste välja tjänstplan när du skapar funktionsappen, och för närvarande kan du inte att ändra ditt val. Mer information finns i [Choose an Azure Functions hosting plan](functions-scale.md) (Välja en värdplan för Azure Functions).

Om du planerar att köra JavaScript-funktioner i en App Service-plan bör du välja en plan med färre kärnor. Mer information finns i [JavaScript-referensen för Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Krav för lagringskonto

När du skapar en funktionsapp i App Service måste du skapa eller länka till ett allmänt Azure Storage-konto som stöder Blob Storage, Queue Storage och Table Storage. Functions använder internt Storage för åtgärder som att hantera utlösare och logga funktionskörningar. Vissa lagringskonton stöder inte köer och tabeller, till exempel konton med endast bloblagring, Azure Premium Storage och allmänna lagringskonton med ZRS-replikering. Dessa konton filtreras bort från lagringskontobladet när du skapar en funktionsapp.

>[!NOTE]
>När du använder förbrukningsvärdplanen lagras funktionskoden och bindningskonfigurationsfilerna i Azure File Storage i huvudlagringskontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas.

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Nästa steg

Medan Azure Portal gör det enkelt att skapa och testa funktioner, rekommenderar vi [lokal utveckling](functions-develop-local.md). När du har skapat en Function-app i portalen måste du fortfarande lägga till en funktion. 

> [!div class="nextstepaction"]
> [Lägg till en HTTP-utlöst funktion](functions-create-first-azure-function.md#create-function)
