---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608393"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-versionen i Functions 1.x

I funktioner 1.x, Storage-utlösare och bindningar använda version 7.2.1 av Azure Storage SDK ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-paketet). Om du refererar till en annan version av Storage SDK, och du binder till en typ av Storage SDK i din funktionssignatur, kan Functions-körning rapporterar att den inte kan bindas till den typen. Lösningen är att se till att projektreferenserna [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
