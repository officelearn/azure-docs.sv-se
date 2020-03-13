---
title: Skapa ett Azure Media Services-konto med Azure-portalen
description: Den här självstudien går igenom hur du skapar ett Azure Media Services-konto med Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5b05cd31a1747da0170556003e7a8534752e2fde
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137202"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Använd Azure Portal för att skapa ett Media Services konto

Azure Portal är ett sätt att snabbt skapa ett Azure Media Services-konto. Du kan använda ditt konto för att få åtkomst till Media Services för att lagra, kryptera, koda, hantera och strömma medieinnehåll i Azure.

För närvarande kan du använda [Azure Portal](https://portal.azure.com/) för att:

* Hantera Media Services v3 [Live-händelser](live-events-outputs-concept.md), 
* Visa (inte hantera) v3- [till gångar](assets-concept.md), 
* [Hämta information om att komma åt API: er](access-api-portal.md). 

För alla andra hanterings uppgifter (t. ex. [transformationer och jobb](transforms-jobs-concept.md) och [innehålls skydd](content-protection-overview.md)) använder du [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.

I den här artikeln beskrivs hur man skapar ett Media Services-konto i Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Klicka på **+ skapa en resurs** > **medie** > **Media Services**.
1. I avsnittet **skapa ett Media Services konto** anger du de värden som krävs.
    
    | Namn | Beskrivning |
    | ---|---|
    |**Kontonamn**|Ange namnet på det nya Media Services kontot. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.|
    |**Prenumeration**|Om du har mer än en prenumeration väljer du en i listan med Azure-prenumerationer som du har åtkomst till.|
    |**Resursgrupp**|Välj en ny eller befintlig resurs. En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Lär dig mer [här](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Plats**|Välj den geografiska region som ska användas för att lagra media-och metadata-poster för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. |
    |**Lagringskonto**|Välj ett lagrings konto för att tillhandahålla Blob Storage för medie innehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett nytt lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.<br/><br/>Du måste ha ett **primärt** lagringskonto, men du kan även ha flera **sekundära** lagringskonton associerade med ditt Media Services-konto. Du kan använda Azure Portal för att lägga till sekundära lagrings konton. Mer information finns i [Azure Storage konton med Azure Media Services-konton](storage-account-concept.md).<br/><br/>Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.|
    
1. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
1. Klicka på **Skapa** längst ned i formuläret.

    När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av **standardtyp** till i kontot med tillståndet **Stoppad**. Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och [dynamisk kryptering](content-protection-overview.md), måste den strömmande slut punkten från vilken du vill strömma innehåll vara i **Kör** tillstånd. 

## <a name="next-steps"></a>Nästa steg

Om du planerar att komma åt Media Services API program mässigt, se [åtkomst till Azure Media Services-API med Azure AD-autentisering](access-api-portal.md).

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

