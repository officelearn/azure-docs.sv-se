---
title: Skapa en funktionsapp från Azure Portal
description: Skapa en ny funktionsapp i Azure från portalen.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 086a543e75d083094d4dfa789e71afaba5da6d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368799"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Skapa en funktionsapp från Azure Portal

Det här avsnittet visar hur du använder Azure Functions för att skapa en funktionsapp i Azure-portalen. En funktionsapp är en container som är värd för körningen av enskilda funktioner. 

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

När du har skapat funktionsappen kan du skapa enskilda funktioner på ett eller flera olika språk. Skapa funktioner [med hjälp av portalen](functions-create-first-azure-function.md#create-function), [kontinuerlig distribution](functions-continuous-deployment.md) eller [överföring med FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Tjänstplaner

Azure Functions har tre olika serviceplaner: Förbrukningsplan, Premium-plan och Dedikerad (App Service) plan. Du måste välja serviceplan när funktionsappen skapas och den kan inte ändras senare. Mer information finns i [Choose an Azure Functions hosting plan](functions-scale.md) (Välja en värdplan för Azure Functions).

Om du planerar att köra JavaScript-funktioner på ett dedikerat (App Service)-abonnemang bör du välja ett abonnemang med färre kärnor. Mer information finns i [JavaScript-referensen för Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Krav för lagringskonto

När du skapar en funktionsapp måste du skapa eller länka till ett Azure Storage-konto med allmänt syfte som stöder Blob-, kö- och Tabelllagring. Functions använder internt Storage för åtgärder som att hantera utlösare och logga funktionskörningar. Vissa lagringskonton stöder inte köer och tabeller, till exempel konton med endast bloblagring, Azure Premium Storage och allmänna lagringskonton med ZRS-replikering. Dessa konton filtreras bort från lagringskontobladet när du skapar en funktionsapp.

>[!NOTE]
>När du använder förbrukningsvärdplanen lagras funktionskoden och bindningskonfigurationsfilerna i Azure File Storage i huvudlagringskontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas.

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Nästa steg

Azure-portalen gör det enkelt att skapa och prova funktioner, men vi rekommenderar [lokal utveckling](functions-develop-local.md). När du har skapat en funktionsapp i portalen måste du fortfarande lägga till en funktion. 

> [!div class="nextstepaction"]
> [Lägga till en HTTP-utlöst funktion](functions-create-first-azure-function.md#create-function)
