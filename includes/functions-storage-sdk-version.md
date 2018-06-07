---
title: ta med fil
description: ta med fil
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675283"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-versionen i funktioner 1.x

I funktioner 1.x, lagring utlösare och bindningar använder version 7.2.1 av Azure Storage SDK: N ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-paketet). Om du refererar till en annan version av Storage SDK: N, och du binder till en Storage SDK: N-typ i en funktionssignaturen, kan Functions-runtime rapportera att den inte kan bindas till den typen. Lösningen är att kontrollera projektreferenserna [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
