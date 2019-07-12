---
title: Hantera Azure Media Services v2-konton | Microsoft Docs
description: Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. Den här artikeln förklarar hur du hanterar Azure Media Services v2-konton.
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
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622048"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Hantera Azure Media Services v2-konton

Om du vill börja hantera, kryptera, kodning, analysera och strömma medieinnehåll i Azure, måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Flytta ett Media Services-konto mellan prenumerationer 

Om du behöver flytta en Media Services-konto till en ny prenumeration måste du först flytta hela resursgruppen som innehåller Media Services-kontot till den nya prenumerationen. Du måste flytta alla anslutna resurser: Azure Storage-konton, Azure CDN-profiler osv. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/resource-group-move-resources.md). Precis som med alla resurser i Azure kan resource group flyttar ta lite tid att slutföra.

Media Services v2 har inte stöd för flera innehavare modell. Om du vill flytta en Media Services-konto till en prenumeration i en ny klient kan du skapa ett nytt Azure Active Directory (Azure AD)-program i den nya innehavaren. Flytta ditt konto till prenumerationen i den nya innehavaren. Du kan börja använda Azure AD-program från den nya innehavaren att få åtkomst till Media Services-kontot med v2-API: er när klienten övergången är klar. 

> [!IMPORTANT]
> Du behöver återställa den [Azure AD-autentisering](media-services-portal-get-started-with-aad.md) information för att få åtkomst till Media Services v2 API.  
### <a name="considerations"></a>Överväganden

* Skapa säkerhetskopior av alla data i ditt konto innan du migrerar till en annan prenumeration.
* Du måste stoppa alla strömmande slutpunkter och liveuppspelning resurser. Användarna kommer inte att kunna komma åt ditt innehåll för hela gruppen resursflyttningen. 

> [!IMPORTANT]
> Starta inte slutpunkt för direktuppspelning tills flyttningen är klar.

### <a name="troubleshoot"></a>Felsöka 

Om ett Media Services-konto eller ett associerat Azure Storage-konto ”kopplas” efter grupp resursflyttningen, försöker du roterar nycklar för Lagringskonto. Om du roterar nycklar för Lagringskontot inte matchar ”frånkopplad” status för Media Services-konto, rapportera en ny supportförfrågan via den ”Support och felsökning”-menyn i Media Services-kontot.  
 
## <a name="next-steps"></a>Nästa steg

[Skapa ett konto](media-services-portal-create-account.md)
