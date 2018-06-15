---
title: Hantera strömningsslutpunkter med Azure-portalen | Microsoft Docs
description: Det här avsnittet visar hur du hanterar strömningsslutpunkter med Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: cfowler
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: e6f551a7970f226ba40753009b24bd4c5eeb67fb
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
ms.locfileid: "27702962"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Hantera strömningsslutpunkter med Azure-portalen

Den här artikeln visar hur du använder Azure-portalen för att hantera strömningsslutpunkter. 

>[!NOTE]
>Se till att granska den [översikt](media-services-streaming-endpoints-overview.md) artikel. 

Information om hur du skalar den strömmande slutpunkten finns [detta](media-services-portal-scale-streaming-endpoints.md) artikel.

## <a name="start-managing-streaming-endpoints"></a>Börja hantera strömningsslutpunkter 

Gör följande om du vill börja hantera strömningsslutpunkter för ditt konto.

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I den **inställningar** bladet väljer **Strömningsslutpunkter**.
   
    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Du debiteras endast när din Strömningsslutpunkt är i körningstillstånd.

## <a name="adddelete-a-streaming-endpoint"></a>Lägg till/ta bort en strömmande slutpunkt

>[!NOTE]
>Standard strömmande slutpunkten kan inte tas bort.

Om du vill lägga till/ta bort strömmande slutpunkt med hjälp av Azure portal, gör du följande:

1. Lägg till en strömningsslutpunkt, klicka på den **+ Endpoint** överst på sidan. 

    Om du planerar att ha olika CDN-nät eller en CDN och direktåtkomst kanske du vill flera Strömningsslutpunkter.

2. Ta bort en strömmande slutpunkt, trycka på **ta bort** knappen.      
3. Klicka på den **starta** för att starta den strömmande slutpunkten.
   
    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Konfigurera strömmande slutpunkt
Strömmande slutpunkten kan du konfigurera följande egenskaper:

* Åtkomstkontroll
* Cache-kontroll
* Mellan åtkomstprinciper för platsen

Detaljerad information om de här egenskaperna finns [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>När CDN är aktiverat kan du inte kommer åt IP-åtkomst. IP-åtkomst kan bara användas när du inte har CDN.

Du kan konfigurera strömningsslutpunkt genom att göra följande:

1. Välj den strömningsslutpunkt som du vill konfigurera.
2. Klicka på **inställningar**.

En kort beskrivning av fälten följer.

![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximal cachestorlek princip: används för att konfigurera cache-livstid för tillgångar som hanteras via den här strömmande slutpunkten. Om inget värde har angetts används standardvärdet. Standardvärdena kan också definieras direkt i Azure-lagring. Om Azure CDN aktiveras för den strömmande slutpunkten kan ställas inte in principvärdet cache på mindre än 600 sekunder.  
2. Tillåtna IP-adresser: används för att ange IP-adresser som skulle kunna ansluta till den publicerade strömningsslutpunkt. Om inga angivna IP-adresser, skulle IP-adresser kunna ansluta. IP-adresser kan anges som en IP-adress (t.ex, 10.0.0.1), en IP-intervall med IP-adress och nätmask CIDR (till exempel 10.0.0.1/22) eller en IP-intervall med IP-adress och en prickad decimal nätmask (till exempel ”10.0.0.1 () 255.255.255.0)').
3. Konfiguration för autentisering med Akamai signatur-huvud: används för att ange hur signatur sidhuvud autentiseringsbegäran från Akamai servrar har konfigurerats. Förfallodatum är i UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Skala din Premium strömmande slutpunkten

Mer information finns i [den här artikeln](media-services-portal-scale-streaming-endpoints.md).

## <a id="enable_cdn"></a>Aktivera Azure CDN-integrering

När du skapar ett nytt konto är standard strömmande slutpunkten Azure CDN-integrering aktiverad som standard.

Om du senare vill Aktiverar/inaktiverar CDN strömmande slutpunkten måste vara i den **stoppats** tillstånd. Det kan ta upp till två timmar för Azure CDN-integrering för att hämta aktiverad och för att ändringarna ska vara aktiva för alla de CDN POP. Dock kan starta strömmande slutpunkt och dataströmmen utan avbrott från strömningsslutpunkt och när integrationen är slutförd, dataströmmen levereras från CDN. Under etablering strömmande slutpunkten kommer att ha **startar** tillstånd och du kan se degredad prestanda.

CDN-integrering är aktiverat i Azure-Datacenter utom Kina och federala myndigheter regioner.

När den har aktiverats på **åtkomstkontroll**, ** anpassade värdnamnet och **autentisering med Akamai signatur** konfigurationen hämtar inaktiverad.
 
> [!IMPORTANT]
> Azure Media Services-integrering med Azure CDN implementeras på **Azure CDN från Verizon** standard strömningsslutpunkter. Premium strömningsslutpunkter kan konfigureras med hjälp av alla **Azure CDN priser nivåer och providers**. Mer information om Azure CDN-funktioner finns i [CDN-översikt](../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Annat som är bra att tänka på

* När CDN är aktiverad för en strömmande slutpunkten, det går inte att klienter begär innehåll direkt från ursprunget. Om du behöver kunna testa ditt innehåll med eller utan CDN kan skapa du en annan strömningsslutpunkt som inte är aktiverat CDN.
* Din strömmande slutpunktens värdnamn är densamma när du har aktiverat CDN. Du behöver inte göra ändringar i media services arbetsflödet efter CDN har aktiverats. Om din strömmande slutpunktens värdnamn är strasbourg.streaming.mediaservices.windows.net, efter att aktivera CDN, använda exakt samma värdnamn.
* För nya strömningsslutpunkter kan du aktivera CDN genom att skapa en ny slutpunkt; för befintliga strömningsslutpunkter behöver du först stoppa slutpunkten och sedan aktivera/inaktivera CDN.
* Standard strömmande slutpunkten kan bara konfigureras med hjälp av **Verizon Standard CDN-providern** med klassiska Azure-portalen. Du kan dock aktivera andra leverantörer av Azure CDN med hjälp av REST API: er.

## <a name="configure-cdn-profile"></a>Konfigurera CDN-profilen

Du kan konfigurera CDN-profilen genom att välja den **hantera CDN** knappen från överkanten.

![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

