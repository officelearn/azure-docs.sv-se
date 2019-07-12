---
title: Hantera konton för Azure Media Services v3 | Microsoft Docs
description: Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. Den här artikeln förklarar hur du hanterar Azure Media Services v3-konton.
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
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670690"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Hantera konton för Azure Media Services v3

Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Mer information finns i [lagringskonton](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Flytta ett Media Services-konto mellan prenumerationer 

Om du behöver flytta en Media Services-konto till en ny prenumeration måste du först flytta hela resursgruppen som innehåller Media Services-kontot till den nya prenumerationen. Du måste flytta alla anslutna resurser: Azure Storage-konton, Azure CDN-profiler osv. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/resource-group-move-resources.md). Precis som med alla resurser i Azure kan resource group flyttar ta lite tid att slutföra.

> [!NOTE]
> Media Services v3 har stöd för flera innehavare modellen.

### <a name="considerations"></a>Överväganden

* Skapa säkerhetskopior av alla data i ditt konto innan du migrerar till en annan prenumeration.
* Du måste stoppa alla strömmande slutpunkter och liveuppspelning resurser. Användarna kommer inte att kunna komma åt ditt innehåll för hela gruppen resursflyttningen. 

> [!IMPORTANT]
> Starta inte slutpunkt för direktuppspelning tills flyttningen är klar.

### <a name="troubleshoot"></a>Felsöka 

Om ett Media Services-konto eller ett associerat Azure Storage-konto ”kopplas” efter grupp resursflyttningen, försöker du roterar nycklar för Lagringskonto. Om du roterar nycklar för Lagringskontot inte matchar ”frånkopplad” status för Media Services-konto, rapportera en ny supportförfrågan via den ”Support och felsökning”-menyn i Media Services-kontot.  

## <a name="next-steps"></a>Nästa steg

[Skapa ett konto](create-account-cli-quickstart.md)
