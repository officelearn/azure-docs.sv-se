---
title: Hantera slutpunkter för direktuppspelning med Azure portal | Microsoft Docs
description: Det här avsnittet visar hur du hanterar slutpunkter för direktuppspelning med Azure-portalen.
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
ms.openlocfilehash: a023cb2ea1cf74c8029cc207c9b899631facb929
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018272"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Hantera slutpunkter för direktuppspelning med Azure portal

Den här artikeln visar hur du använder Azure-portalen för att hantera slutpunkter för direktuppspelning. 

>[!NOTE]
>Se till att granska den [översikt](media-services-streaming-endpoints-overview.md) artikeln. 

Information om hur du skalar slutpunkten för direktuppspelning finns i [detta](media-services-portal-scale-streaming-endpoints.md) artikeln.

## <a name="start-managing-streaming-endpoints"></a>Börja hantera slutpunkter för direktuppspelning 

Börja hantera strömningsslutpunkter för ditt konto genom att göra följande.

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I den **inställningar** bladet väljer **slutpunkter för direktuppspelning**.
   
    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Du debiteras endast när din slutpunkt för direktuppspelning som är i körningstillstånd.

## <a name="adddelete-a-streaming-endpoint"></a>Lägg till/ta bort en slutpunkt för direktuppspelning

>[!NOTE]
>Standardslutpunkten för direktuppspelning kan inte tas bort.

Om du vill lägga till/ta bort slutpunkten för direktuppspelning med Azure portal, gör du följande:

1. Lägg till en slutpunkt för direktuppspelning, klicka på den **+ slutpunkt** överst på sidan. 

    Om du planerar att ha olika CDN-nät eller en CDN och direktåtkomst kanske du vill flera slutpunkter för direktuppspelning.

2. Om du vill ta bort en slutpunkt för direktuppspelning, trycker du på **ta bort** knappen.      
3. Klicka på den **starta** för att starta slutpunkten för direktuppspelning.
   
    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Konfigurera slutpunkten för direktuppspelning
Slutpunkt för direktuppspelning kan du konfigurera följande egenskaper:

* Åtkomstkontroll
* Cachekontroll
* Mellan platsen åtkomstprinciper

Detaljerad information om de här egenskaperna finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>När CDN är aktiverat kan du inte åtkomst till IP-åtkomst. IP-åtkomst gäller endast när du inte har CDN.

Du kan konfigurera slutpunkten för direktuppspelning genom att göra följande:

1. Välj den strömningsslutpunkt som du vill konfigurera.
2. Klicka på **inställningar**.

En kort beskrivning av fälten nedan.

![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximal cachestorlek princip: används för att konfigurera livslängd för cachelagring för tillgångar som delas ut via den här slutpunkten för direktuppspelning. Om inget värde anges används standardvärdet. Standardvärdena kan också definieras direkt i Azure storage. Om Azure CDN är aktiverat för slutpunkten för direktuppspelning, bör du inte ange principvärdet cache till mindre än 600 sekunder.  
2. Tillåtna IP-adresser: används för att ange IP-adresser som skulle kunna ansluta till publicerade slutpunkten för direktuppspelning. Om inga IP-adresser som har angetts, skulle IP-adresser kunna ansluta. IP-adresser kan anges som en IP-adress (exempelvis 10.0.0.1), ett IP-intervall med en IP-adress och en CIDR-nätmask (exempelvis 10.0.0.1/22) eller ett IP-intervall med IP-adress och en undernätsmask med punktavgränsad decimalform (exempelvis 10.0.0.1 ' () 255.255.255.0) ”).
3. Konfiguration för autentisering med Akamai signatur rubrik: används för att ange hur signatur rubrik autentiseringsbegäran från Akamai-servrar konfigureras. Förfallotid är i UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Skala din Premiumslutpunkt för direktuppspelning

Mer information finns i [den här artikeln](media-services-portal-scale-streaming-endpoints.md).

## <a id="enable_cdn"></a>Aktivera Azure CDN-integrering

När du skapar ett nytt konto är standard strömmande slutpunkten Azure CDN-integreringen aktiverat som standard.

Om du senare vill aktivera/inaktivera CDN slutpunkten för direktuppspelning måste finnas i den **stoppats** tillstånd. Det kan ta upp till två timmar för Azure CDN-integreringen ska aktiveras och för att ändringarna ska vara aktiva för alla innehållsleveransnätverk. Dock kan starta din strömmande slutpunkt och stream utan avbrott från slutpunkten för direktuppspelning och när integrationen är slutförd, dataströmmen levereras från CDN. Under etablering blir slutpunkten för direktuppspelning i **startar** tillstånd och du kan se försämrade prestanda.

CDN-integreringen har aktiverats i alla Azure-Datacenter förutom Kina och federala myndigheter regioner.

När den har aktiverats i **Access Control**, ** anpassat värdnamn och **autentisering med Akamai signatur** konfigurationen hämtar inaktiverad.
 
> [!IMPORTANT]
> Azure Media Services-integrering med Azure CDN har implementerats på **Azure CDN från Verizon** för standard slutpunkter för direktuppspelning. Premium-slutpunkter för direktuppspelning kan konfigureras med alla **Azure CDN priser nivåerna och providers**. Mer information om Azure CDN-funktioner finns i den [översikt över CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Annat som är bra att tänka på

* När CDN är aktiverat för en slutpunkt för direktuppspelning, det går inte att klienter begär innehåll direkt från ursprunget. Om du behöver kunna testa ditt innehåll med eller utan CDN kan skapa du en annan slutpunkt för direktuppspelning som inte är CDN har aktiverats.
* Strömmande slutpunktens värdnamn detsamma när du aktiverar CDN. Du behöver inte göra ändringar i ditt arbetsflöde för media services när CDN har aktiverats. Om strömmande slutpunktens värdnamn är strasbourg.streaming.mediaservices.windows.net, när du har aktiverat CDN, används till exempel exakt samma värdnamn.
* Ny för slutpunkter, för direktuppspelning kan du aktivera CDN helt enkelt genom att skapa en ny slutpunkt; för befintliga slutpunkter för direktuppspelning måste du först stoppa slutpunkten och sedan aktivera/inaktivera CDN.
* Standard-slutpunkt för direktuppspelning kan bara konfigureras med hjälp av **Verizon Standard CDN-leverantör** med klassiska Azure-portalen. Men kan du aktivera andra Azure CDN-leverantörer med hjälp av REST API: er.

## <a name="configure-cdn-profile"></a>Konfigurera CDN-profil

Du kan konfigurera CDN-profil genom att välja den **hantera CDN** knappen högst upp.

![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

