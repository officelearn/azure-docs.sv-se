---
title: Hantera Azure Media Services v3-konton | Microsoft-dokument
description: Om du vill börja hantera, kryptera, koda, analysera och strömma medieinnehåll i Azure måste du skapa ett Media Services-konto. I den här artikeln beskrivs hur du hanterar Azure Media Services v3-konton.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 08579f7ba952bb4ebcba1595508612affb852528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980371"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Hantera Azure Media Services v3-konton

Om du vill börja hantera, kryptera, koda, analysera och strömma medieinnehåll i Azure måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Mer information finns i [Lagringskonton](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Flytta ett Media Services-konto mellan prenumerationer 

Om du behöver flytta ett Media Services-konto till en ny prenumeration måste du först flytta hela resursgruppen som innehåller Media Services-kontot till den nya prenumerationen. Du måste flytta alla kopplade resurser: Azure Storage-konton, Azure CDN-profiler osv. Mer information finns i [Flytta resurser till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Precis som med alla resurser i Azure kan det ta lite tid att slutföra resursgruppsflyttningar.

> [!NOTE]
> Media Services v3 stöder flera innehavare modell.

### <a name="considerations"></a>Överväganden

* Skapa säkerhetskopior av alla data i ditt konto innan du migrerar till en annan prenumeration.
* Du måste stoppa alla slutpunkter för direktuppspelning och direktuppspelningsresurser. Användarna kan inte komma åt ditt innehåll under resursgruppens flyttning. 

> [!IMPORTANT]
> Starta inte slutpunkten för direktuppspelning förrän flytten har slutförts.

### <a name="troubleshoot"></a>Felsöka 

Om ett Media Services-konto eller ett associerat Azure Storage-konto blir "frånkopplat" efter resursgruppens flytt kan du prova att rotera lagringskontonycklarna. Om det inte går att lösa statusen "frånkopplad" för Media Services-kontot om du roterar nycklarna till lagringskontot, lämnar du in en ny supportbegäran från menyn "Support + felsökning" i Media Services-kontot.  

## <a name="next-steps"></a>Nästa steg

[Skapa ett konto](create-account-cli-quickstart.md)
