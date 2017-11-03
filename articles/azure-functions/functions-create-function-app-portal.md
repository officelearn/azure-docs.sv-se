---
title: "Skapa en funktionsapp från Azure Portal | Microsoft Docs"
description: "Skapa en ny funktionsapp i Azure App Service från portalen."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 3e4eef12c1d19be6e0f1051caaa5cf2e98626aef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Skapa en funktionsapp från Azure-portalen

Azure funktionen appar använder Azure App Service-infrastrukturen. Det här avsnittet visar hur du skapar en funktion i Azure-portalen. En funktionsapp är en behållare som är värd för körningen av enskilda funktioner. När du skapar en funktionsapp i Apptjänst som är värd för planen kan appen funktionen använda alla funktioner i Apptjänst.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

När du skapar en funktionsapp, ange en giltig **appnamn**, som kan innehålla endast bokstäver, siffror och bindestreck. Understreck (**_**) är inte tillåtna.

Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Namnet på ditt lagringskonto måste vara unikt i Azure. 

När funktionen appen har skapats kan skapa du enskilda funktioner i en eller flera olika språk. Skapa funktioner [med hjälp av portalen](functions-create-first-azure-function.md#create-function), [kontinuerlig distribution](functions-continuous-deployment.md), eller av [Överför med FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Service-planer

Azure Functions har två olika serviceplaner: förbrukning planerings- och App Service-plan. Plan för förbrukning tilldelar automatiskt datorkraft när koden körs, att skala ut som behövs för att hantera belastningen och sedan skalor in när koden inte körs. Programtjänstplanen ger din funktion appåtkomst till alla funktioner i Apptjänst. Du måste välja serviceplanen när funktionen appen skapas och den för närvarande inte kan ändras. Mer information finns i [väljer du ett Azure-funktioner som värd för planen](functions-scale.md).

Om du planerar att köra JavaScript-funktioner på en apptjänstplan, bör du välja en plan med färre kärnor. Mer information finns i [JavaScript-referens för funktioner](functions-reference-node.md#choose-single-core-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Lagringskraven för kontot

När du skapar en funktionsapp i App Service, måste du skapa eller länka till en generell Azure Storage-konto som har stöd för lagring av Blob, köer och tabellen. Internt använder funktioner lagring för åtgärder som hanterar utlösare och loggning funktionen körningar. Vissa storage-konton stöder inte köer och tabeller som endast blob storage-konton, Azure Premium-lagring och allmänna lagringskonton med ZRS replikering. Dessa konton är filtrerade av i bladet Storage-konto när du skapar en funktionsapp.

>[!NOTE]
>När du använder förbrukningen värd plan lagras konfigurationsfilerna funktionen kod och bindningen i Azure File storage i huvudsakliga storage-konto. När du tar bort huvudsakliga storage-konto raderas innehållet och kan inte återställas.

Läs mer om lagringskontotyper i [introduktion till Azure Storage-tjänster](../storage/common/storage-introduction.md#introducing-the-azure-storage-services). 

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]



