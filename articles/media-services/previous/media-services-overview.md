---
title: Översikt över Azure Media Services | Microsoft Docs
description: Det här avsnittet ger en översikt över Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 7a98d3acc3f45772f9e28c23a639f495b75bea0a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850059"
---
# <a name="azure-media-services-overview"></a>Översikt över Azure Media Services 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 3](../latest/media-services-overview.md)
> * [Version 2](media-services-overview.md)

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services (AMS) är en utökningsbar molnbaserad plattform som gör att utvecklare kan skapa skalbara program för mediehantering och medieleverans. Media Services baseras på REST API:er som gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

Du kan bygga arbetsflöden för slutpunkt till slutpunkt bara med Media Services. Du kan också välja att använda komponenter från tredje part för vissa delar av arbetsflödet. Koda till exempel med hjälp av en kodare från tredje part. Därefter kan du överföra, skydda, paketera och leverera med Media Services. Du kan välja att strömma ditt innehåll live eller leverera innehåll på begäran. 

## <a name="prerequisites"></a>Nödvändiga komponenter

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

> [!NOTE]
> Information om hur du hämtar den senaste versionen av Java SDK och börjar utveckla med Java finns i [Komma igång med Java-klient-SDK för Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Om du vill hämta den senaste PHP SDK för Media Services, leta efter version 0.5.7 av Microsoft/WindowAzure-paketet i [Packagist-databasen](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Kodexempel

Det finns fler kodexempel i galleriet **Azure Code Samples** (Azure-kodexempel): [Azure Media Services-kodexempel](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Begrepp

Azure Media Services-koncepten finns i [Koncept](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Scenarier som stöds och tillgänglighet för Media Services över datacenter

Detaljerad information finns i [AMS-scenarier och tillgänglighet för funktioner och tjänster över datacenter](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

* Vi garanterar 99,9 % tillgänglighet för REST API-transaktioner för Media Services Encoding.
* För Streaming svarar vi på serviceförfrågningar med 99,9 % tillgänglighet för befintligt medieinnehåll när en Standard- eller Premium-slutpunkt för direktuppspelning har köpts.
* För live-kanaler garanterar vi att kanaler som körs ska ha extern anslutning minst 99,9 % av tiden.
* För Content Protection garanterar vi att minst 99,9 % av tiden utföra viktiga begäranden.
* När det gäller Indexer ska vi utföra service för indexeringsuppgifter som bearbetas med en kodningsreserverad enhet 99,9 % av tiden.

Mer information finns i [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

Information om tillgänglighet i datacenter finns i avsnittet [Tillgänglighet](scenarios-and-availability.md#availability).

## <a name="support"></a>Support

[Azure-supporten](https://azure.microsoft.com/support/options/) tillhandahåller support för Azure, inklusive Media Services.

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
