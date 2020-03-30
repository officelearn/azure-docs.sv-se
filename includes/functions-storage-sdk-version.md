---
title: ta med fil
description: ta med fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198434"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-version i Functions 1.x

I Functions 1.x använder storage-utlösare och bindningar version 7.2.1 av Azure Storage SDK[(WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-paketet). Om du refererar till en annan version av Storage SDK och binder till en Storage SDK-typ i funktionssignaturen kan funktionskörningen rapportera att den inte kan binda till den typen. Lösningen är att se till att ditt projekt refererar till [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
