---
title: Hantera Azure Media Services v2-konton | Microsoft-dokument
description: Om du vill börja hantera, kryptera, koda, analysera och strömma medieinnehåll i Azure måste du skapa ett Media Services-konto. I den här artikeln beskrivs hur du hanterar Azure Media Services v2-konton.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981934"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Hantera Azure Media Services v2-konton

Om du vill börja hantera, kryptera, koda, analysera och strömma medieinnehåll i Azure måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Flytta ett Media Services-konto mellan prenumerationer 

Om du behöver flytta ett Media Services-konto till en ny prenumeration måste du först flytta hela resursgruppen som innehåller Media Services-kontot till den nya prenumerationen. Du måste flytta alla kopplade resurser: Azure Storage-konton, Azure CDN-profiler osv. Mer information finns i [Flytta resurser till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Precis som med alla resurser i Azure kan det ta lite tid att slutföra resursgruppsflyttningar.

Media Services v2 stöder inte flera innehavare modell. Om du behöver flytta ett Media Services-konto till en prenumeration i en ny klientorganisation skapar du ett nytt Azure Active Directory -program (Azure AD) i den nya klienten. Flytta sedan ditt konto till prenumerationen i den nya klienten. När klientflytten är klar kan du börja använda ett Azure AD-program från den nya klienten för att komma åt Media Services-kontot med hjälp av v2 API:er. 

> [!IMPORTANT]
> Du måste återställa [Azure AD-autentiseringsinformationen](media-services-portal-get-started-with-aad.md) för att komma åt Media Services v2 API.  
### <a name="considerations"></a>Överväganden

* Skapa säkerhetskopior av alla data i ditt konto innan du migrerar till en annan prenumeration.
* Du måste stoppa alla slutpunkter för direktuppspelning och direktuppspelningsresurser. Användarna kan inte komma åt ditt innehåll under resursgruppens flyttning. 

> [!IMPORTANT]
> Starta inte slutpunkten för direktuppspelning förrän flytten har slutförts.

### <a name="troubleshoot"></a>Felsöka 

Om ett Media Services-konto eller ett associerat Azure Storage-konto blir "frånkopplat" efter resursgruppens flytt kan du prova att rotera lagringskontonycklarna. Om det inte går att lösa statusen "frånkopplad" för Media Services-kontot om du roterar nycklarna till lagringskontot, lämnar du in en ny supportbegäran från menyn "Support + felsökning" i Media Services-kontot.  
 
## <a name="next-steps"></a>Nästa steg

[Skapa ett konto](media-services-portal-create-account.md)
