---
title: "Översikt över Azure Media Services | Microsoft Docs"
description: "Det här avsnittet ger en översikt över Azure Media Services"
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/04/2017
ms.author: juliako;anilmur
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 38c3637cc477e24ac8d056611e7fda9a4d693cdb
ms.contentlocale: sv-se
ms.lasthandoff: 07/06/2017

---
# <a name="azure-media-services-overview"></a>Översikt över Azure Media Services 

Microsoft Azure Media Services är en utökningsbar molnbaserad plattform som gör att utvecklare kan skapa skalbara program för mediehantering och medieleverans. Media Services baseras på REST API:er som gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

Du kan bygga arbetsflöden för slutpunkt till slutpunkt bara med Media Services. Du kan också välja att använda komponenter från tredje part för vissa delar av arbetsflödet. Koda till exempel med hjälp av en kodare från tredje part. Därefter kan du överföra, skydda, paketera och leverera med Media Services.

Du kan välja att strömma ditt innehåll live eller leverera innehåll på begäran. Avsnittet innehåller också länkar till andra relevanta avsnitt.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
Du kan visa sökvägar för AMS-utbildning här:

* [Arbetsflöde för AMS-liveuppspelning](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Arbetsflöde för AMS-strömning på begäran](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Krav

Om du vill börja använda Azure Media Services ska du ha följande:

* Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com).
* Ett Azure Media Services-konto. Mer information finns i [Skapa konto](media-services-portal-create-account.md).
* (Valfritt) Konfigurera utvecklingsmiljön. Välj .NET eller REST API för din utvecklingsmiljö. Mer information finns i [Ställa in miljön](media-services-dotnet-how-to-use.md).

    Lär dig även att [ansluta programmässigt till AMS API](media-services-use-aad-auth-to-access-ams-api.md).
* En standard- eller premiumslutpunkt för direktuppspelning med tillståndet Startad.  Mer information finns i [Hantera strömningsslutpunkter](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>SDK:er och verktyg

Bygg Media Services-lösningar genom att använda:

* [Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* En av de tillgängliga klient-SDK:erna:
    * [Azure Media Services SDK för .NET](https://github.com/Azure/azure-sdk-for-media-services),
    * [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Azure Media Services för Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (detta är en icke-Microsoft-version av en Node.js SDK. Den underhålls av ett community och har för närvarande inte en 100-procentig täckning av AMS API:er).
* Befintliga verktyg:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) är ett Winforms/C#-program för Windows)

## <a name="concepts-and-overview"></a>Koncept och översikt
Azure Media Services-koncepten finns i [Koncept](media-services-concepts.md).

En instruktionsserie som ger en introduktion till huvudkomponenterna i Azure Media Services finns i [Azure Media Services stegvisa självstudier](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Den här serien ger en bra översikt över koncepten och använder verktyget AMSE för att demonstrera AMS-uppgifter. AMSE-verktyget är ett verktyg i Windows. Det här verktyget stöder de flesta av de uppgifter som du kan göra programmässigt med [AMS SDK för .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java) eller  [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Scenarier som stöds och tillgänglighet för Media Services över datacenter

Detaljerad information finns i [AMS-scenarier och tillgänglighet för funktioner och tjänster över datacenter](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

* Vi garanterar 99,9 % tillgänglighet för REST API-transaktioner för Media Services Encoding.
* För Streaming svarar vi på serviceförfrågningar med 99,9 % tillgänglighet för befintligt medieinnehåll när en Standard- eller Premium-slutpunkt för direktuppspelning har köpts.
* För live-kanaler garanterar vi att kanaler som körs ska ha extern anslutning minst 99,9 % av tiden.
* När det gäller Content Protection garanterar vi att vi ska uppfyller viktiga förfrågningar minst 99,9 % av tiden.
* När det gäller Indexer ska vi utföra service för indexeringsuppgifter som bearbetas med en kodningsreserverad enhet 99,9 % av tiden.

Mer information finns i [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

Information om tillgänglighet i datacenter finns i avsnittet [tillgänglighet](scenarios-and-availability.md#availability).

## <a name="support"></a>Support

[Azure-supporten](https://azure.microsoft.com/support/options/) tillhandahåller support för Azure, inklusive Media Services.

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

