---
title: inkludera fil
description: inkludera fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77198434"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-version i functions 1. x

I funktionerna 1. x använder Storage-utlösare och bindningar version 7.2.1 av Azure Storage SDK ([windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-paketet). Om du refererar till en annan version av Storage SDK och du binder till en lagrings-SDK-typ i funktions signaturen, kan funktions körningen rapportera att den inte kan bindas till den typen. Lösningen är att se till att projektet refererar till [windowsazure. Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
