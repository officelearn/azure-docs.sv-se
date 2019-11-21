---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230777"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Skapa en funktionsapp från Azure Portal

This topic shows you how to use Azure Functions to create a function app in the Azure portal. En funktionsapp är en container som är värd för körningen av enskilda funktioner. 

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

När du skapar en funktionsapp måste du ange ett giltigt **appnamn**. Namnet kan endast innehålla bokstäver, siffror och bindestreck. Understreck ( **_** ) är inte tillåtna.

Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Namnet på ditt lagringskonto måste vara unikt i Azure. 

När du har skapat funktionsappen kan du skapa enskilda funktioner på ett eller flera olika språk. Skapa funktioner [med hjälp av portalen](functions-create-first-azure-function.md#create-function), [kontinuerlig distribution](functions-continuous-deployment.md) eller [överföring med FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Tjänstplaner

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. Mer information finns i [Choose an Azure Functions hosting plan](functions-scale.md) (Välja en värdplan för Azure Functions).

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. Mer information finns i [JavaScript-referensen för Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Krav för lagringskonto

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Functions använder internt Storage för åtgärder som att hantera utlösare och logga funktionskörningar. Vissa lagringskonton stöder inte köer och tabeller, till exempel konton med endast bloblagring, Azure Premium Storage och allmänna lagringskonton med ZRS-replikering. Dessa konton filtreras bort från lagringskontobladet när du skapar en funktionsapp.

>[!NOTE]
>När du använder förbrukningsvärdplanen lagras funktionskoden och bindningskonfigurationsfilerna i Azure File Storage i huvudlagringskontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas.

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Nästa steg

While the Azure portal makes it easy to create and try out Functions, we recommend [local development](functions-develop-local.md). After creating a function app in the portal, you still need to add a function. 

> [!div class="nextstepaction"]
> [Add an HTTP triggered function](functions-create-first-azure-function.md#create-function)
