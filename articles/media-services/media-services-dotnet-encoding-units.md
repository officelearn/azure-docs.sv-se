---
title: "Skala bearbetning genom att lägga till kodning enheter – Azure media |  Microsoft Docs"
description: "Lär dig hur du hur du lägger till kodning enheter med .NET"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 72a8729d22a9e76c8076d7a3347619a2163e4f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Hur du skalar kodning med .NET SDK:n
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Översikt
> [!IMPORTANT]
> Se till att granska den [översikt](media-services-scale-media-processing-overview.md) avsnittet om du vill ha mer information om skalning media bearbetning av avsnittet.
> 
> 

Om du vill ändra typ av enhet och antalet kodningsreserverade enheter med hjälp av .NET SDK, gör du följande:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Öppna ett supportärende
Som standard kan alla Media Services-konto skala upp till 25 kodning och 5 på begäran reserverade enheter för strömning. Du kan begära en högre gräns genom att öppna ett supportärende.

### <a name="open-a-support-ticket"></a>Öppna ett supportärende
Om du vill öppna en supportbegäran biljetten gör du följande:

1. Klicka på [få Support](https://manage.windowsazure.com/?getsupport=true). Om du inte är inloggad uppmanas du att ange dina autentiseringsuppgifter.
2. Välj din prenumeration.
3. Välj ”Technical” under typ av stöd.
4. Klicka på ”Skapa biljett”.
5. Välj ”Azure Media Services” i produktlistan över visas på nästa sida.
6. Välj ”typ” som passar ditt problem.
7. Klicka på Fortsätt.
8. Följ instruktionerna på nästa sida och anger sedan information om problemet.
9. Klicka på Skicka för att öppna biljetten.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

