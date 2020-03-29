---
title: Hantera slutpunkter för direktuppspelning med Azure-portalen | Microsoft-dokument
description: Den här artikeln visar hur du hanterar slutpunkter för direktuppspelning med Azure-portalen.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 57ec22cb02512577f8737718cae9175403fb5603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900885"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Hantera slutpunkter för direktuppspelning med Azure-portalen 

Den här artikeln visar hur du använder Azure-portalen för att hantera slutpunkter för direktuppspelning. 

>[!NOTE]
>Se till att granska [översiktsartikeln.](media-services-streaming-endpoints-overview.md) 

Information om hur du skalar slutpunkten för direktuppspelning finns i den [här](media-services-portal-scale-streaming-endpoints.md) artikeln.

## <a name="start-managing-streaming-endpoints"></a>Börja hantera slutpunkter för direktuppspelning 

Gör följande om du vill börja hantera slutpunkter för direktuppspelning för ditt konto.

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I bladet **Inställningar** väljer du **Slutpunkter för direktuppspelning**.
   
    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Du faktureras bara när slutpunkten för direktuppspelning körs.

## <a name="adddelete-a-streaming-endpoint"></a>Lägga till/ta bort en slutpunkt för direktuppspelning

>[!NOTE]
>Det går inte att ta bort standardslutpunkten för direktuppspelning.

Så här lägger du till/tar bort slutpunkten för direktuppspelning med Azure-portalen:

1. Om du vill lägga till en slutpunkt för direktuppspelning klickar du på **slutpunkten +** högst upp på sidan. 

    Du kanske vill ha flera slutpunkter för direktuppspelning om du planerar att ha olika CDN-nätverk eller ett CDN och direktåtkomst.

2. Om du vill ta bort en slutpunkt för direktuppspelning trycker du på **Knappen Ta bort.**      
3. Klicka på **Start-knappen** för att starta slutpunkten för direktuppspelning.
   
    ![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Konfigurera slutpunkten för direktuppspelning
Med slutpunkten för direktuppspelning kan du konfigurera följande egenskaper:

* Åtkomstkontroll
* Cachekontroll
* Principer för åtkomst mellan webbplatser

Detaljerad information om dessa egenskaper finns i [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>När CDN är aktiverat kan du inte komma åt IP-åtkomst. IP-åtkomst är endast tillämpligt när du inte har CDN.

Du kan konfigurera slutpunkten för direktuppspelning genom att göra följande:

1. Välj den slutpunkt för direktuppspelning som du vill konfigurera.
2. Klicka på **Inställningar**.

En kort beskrivning av fälten följer.

![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximal cacheprincip: används för att konfigurera cachelivslängden för tillgångar som visas via den här slutpunkten för direktuppspelning. Om inget värde har angetts används standardvärdet. Standardvärdena kan också definieras direkt i Azure-lagring. Om Azure CDN är aktiverat för slutpunkten för direktuppspelning bör du inte ange cacheprincipvärdet till mindre än 600 sekunder.  
2. Tillåten IP-adresser: används för att ange IP-adresser som skulle tillåtas ansluta till den publicerade slutpunkten för direktuppspelning. Om inga IP-adresser anges kan alla IP-adresser ansluta. IP-adresser kan anges som antingen en enda IP-adress (t.ex. "10.0.0.1"), ett IP-intervall med en IP-adress och en CIDR-nätmask (till exempel "10.0.0.1/22"), eller ett IP-intervall med IP-adress och en prickad decimalnätmask (till exempel "10.0.0.1(255.255.25.0)").
3. Konfiguration för Akamai signaturhuvudautentisering: används för att ange hur begäran om autentisering av signaturhuvud från Akamai-servrar är konfigurerad. Förfallodatumet är i UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Skala slutpunkten för Premium-strömning

Mer information finns i [den här](media-services-portal-scale-streaming-endpoints.md) artikeln.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Aktivera Azure CDN-integrering

När du skapar ett nytt konto aktiveras standardintegrering av Azure CDN-cdn-cdn-integrering som standard.

Om du senare vill inaktivera/aktivera CDN måste slutpunkten för direktuppspelning vara i **stoppat** tillstånd. Det kan ta upp till två timmar för Azure CDN-integreringen att aktiveras och för att ändringarna ska vara aktiva i alla CDN-POP:er. Du kan dock starta slutpunkten för direktuppspelning och streama utan avbrott från slutpunkten för direktuppspelning och när integreringen är klar levereras strömmen från CDN. Under etableringsperioden kommer din slutpunkt för direktuppspelning att vara i **startläge** och du kan observera försämrade prestanda.

CDN-integrering är aktiverad i alla Azure-datacenter utom Kina och federala myndigheter.

När den är aktiverad inaktiveras konfigurationen **för åtkomstkontroll**, **Anpassad värdnamn och **Akamai-signaturautentisering.**
 
> [!IMPORTANT]
> Azure Media Services-integrering med Azure CDN implementeras på **Azure CDN från Verizon** för standardslutpunkter för direktuppspelning. Slutpunkter för premiumstreaming kan konfigureras med alla **Azure CDN-prisnivåer och -leverantörer**. Mer information om Azure CDN-funktioner finns i [CDN-översikten](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Annat som är bra att tänka på

* När CDN är aktiverat för en slutpunkt för direktuppspelning kan klienter inte begära innehåll direkt från ursprunget. Om du behöver möjligheten att testa ditt innehåll med eller utan CDN kan du skapa en annan slutpunkt för direktuppspelning som inte är CDN-aktiverad.
* Ditt slutpunktsvärdnamn för direktuppspelning förblir detsamma när CDN har aktiverats. Du behöver inte göra några ändringar i arbetsflödet för medietjänster när CDN har aktiverats. Om ditt slutpunktsnamn för direktuppspelning till exempel är strasbourg.streaming.mediaservices.windows.net används exakt samma värdnamn när cdn har aktiverats.
* För nya slutpunkter för direktuppspelning kan du aktivera CDN genom att helt enkelt skapa en ny slutpunkt. För befintliga slutpunkter för direktuppspelning måste du först stoppa slutpunkten och sedan aktivera/inaktivera CDN.
* Slutpunkt för standardstreaming kan endast konfigureras med **Verizon Standard CDN-provider** med Azure Classic Portal. Du kan dock aktivera andra Azure CDN-providers med REST API:er.

## <a name="configure-cdn-profile"></a>Konfigurera CDN-profil

Du kan konfigurera CDN-profilen genom att välja **knappen Hantera CDN** högst upp.

![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

