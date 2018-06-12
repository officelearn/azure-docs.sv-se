---
title: Förbättra prestanda genom att komprimera filerna i Azure CDN | Microsoft Docs
description: Lär dig att förbättra hastighet för överföring av filen och öka sidinläsning prestanda genom att komprimera filerna i Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: bdff57275cf123079004ada732fe782d98399d71
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260404"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Förbättra prestanda genom att komprimera filerna i Azure CDN
Komprimering är ett enkelt och effektivt sätt att filen överföringen snabbare och öka sidinläsning prestanda genom att minska storleken på en fil innan den skickas från servern. Komprimering kan minska kostnader för bandbredd och tillhandahålla snabbare för dina användare.

Det finns två sätt att aktivera komprimering:

- Aktivera komprimering på ursprungsservern. I det här fallet Azure CDN skickar med komprimerade filer och skickar dem till klienter som begär dem.
- Aktivera komprimering direkt på CDN POP-servrar (*komprimering direkt*). I det här fallet CDN komprimerar filerna och fungerar för slutanvändarna, även om de inte har komprimerats med den ursprungliga servern.

> [!IMPORTANT]
> Ändringar av Azure CDN-konfiguration kan ta lite tid att spridas via nätverket: 
- För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
- För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
- För **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** profiler, spridningen vanligtvis är klar på 10 minuter. 
>
> Om du ställer in komprimering för första gången för CDN-slutpunkten, Överväg att vänta 1 till 2 timmar innan du felsöka för att säkerställa komprimeringsinställningarna har spridits till POP.
> 
> 

## <a name="enabling-compression"></a>Aktivera komprimering
CDN-nivåerna standard och premium ger samma komprimering funktioner, men användargränssnittet skiljer sig åt. Mer information om skillnaderna mellan standard och premium CDN nivåer finns [översikt över Azure CDN](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standard CDN-profiler 
> [!NOTE]
> Det här avsnittet gäller **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**, och **Azure CDN Standard från Akamai** profiler.
> 
> 

1. Välj CDN-slutpunkt som du vill hantera från sidan CDN-profil.
   
    ![CDN-profil-slutpunkter](./media/cdn-file-compression/cdn-endpoints.png)
   
    CDN-slutpunkten sidan öppnas.
2. Välj **komprimering**.

    ![Val av CDN-komprimering](./media/cdn-file-compression/cdn-compress-select-std.png)
   
    Sidan komprimering öppnas.
3. Välj **på** aktivera komprimering.
   
    ![Komprimeringsalternativ för CDN-fil](./media/cdn-file-compression/cdn-compress-standard.png)
4. Använd standard-MIME-typer eller ändra listan genom att lägga till eller ta bort MIME-typer.
   
   > [!TIP]
   > Det rekommenderas inte att tillämpa komprimering i komprimerat format även om det är möjligt. Till exempel ZIP, MP3, MP4- eller JPG.
   > 
 
5. När du har gjort ändringarna, Välj **spara**.

### <a name="premium-cdn-profiles"></a>Premium CDN-profiler
> [!NOTE]
> Det här avsnittet gäller enbart för **Azure CDN Premium från Verizon** profiler.
> 

1. Välj sidan CDN-profil **hantera**.
   
    ![CDN hantera select](./media/cdn-file-compression/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **HTTP stora** och klicka sedan hovra över den **cacheinställningarna** utfällbar. Välj **komprimering**.

    ![Val av CDN-komprimering](./media/cdn-file-compression/cdn-compress-select.png)
   
    Komprimeringsalternativ visas.
   
    ![Komprimeringsalternativ för CDN-fil](./media/cdn-file-compression/cdn-compress-files.png)
3. Aktivera komprimering genom att välja **komprimering aktiverat**. Ange MIME-typer som du vill komprimera som en kommaavgränsad lista (inga blanksteg) i den **filtyper** rutan.
   
   > [!TIP]
   > Det rekommenderas inte att tillämpa komprimering i komprimerat format även om det är möjligt. Till exempel ZIP, MP3, MP4- eller JPG.
   > 
    
4. När du har gjort ändringarna, Välj **uppdatering**.

## <a name="compression-rules"></a>Regler för komprimering

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard från Microsoft-profiler

För **Azure CDN Standard från Microsoft** profiler, alla filer är tillgängliga för komprimering. Men en fil måste vara av en MIME-typ som har varit [konfigurerats för komprimering](#enabling-compression).

Dessa profiler har stöd för följande komprimering kodningar:
- gzip (GNU zip)
- brotli 
 
Om begäran har stöd för fler än en typ av komprimering, åsidosätter de komprimeringstyperna brotli komprimering.

När en begäran för en tillgång anger gzip komprimering och begäran resultat i en cache-miss, utför Azure CDN gzip-komprimering av tillgången direkt på POP-servern. Därefter kan hämtas komprimerad fil från cachen.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN från Verizon profiler

För **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** profiler, endast filer komprimeras. En fil måste vara uppfyllda för komprimering:
- Är större än 128 byte
- Vara mindre än 1 MB
 
Dessa profiler har stöd för följande komprimering kodningar:
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 
 
Om begäran har stöd för fler än en typ av komprimering, åsidosätter de komprimeringstyperna brotli komprimering.

När en begäran för en tillgång anger brotli komprimering (HTTP-huvudet är `Accept-Encoding: br`) och begäran resulterar i en cache-miss Azure CDN utför brotli komprimering av tillgången direkt på POP-servern. Därefter kan hämtas komprimerad fil från cachen.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard från Akamai profiler

För **Azure CDN Standard från Akamai** profiler, alla filer är tillgängliga för komprimering. Men en fil måste vara av en MIME-typ som har varit [konfigurerats för komprimering](#enabling-compression).

De här profilerna stöder gzip komprimering endast kodning. När en profil slutpunkt begär en gzip-kodad fil, begäran alltid från ursprung, oavsett klientbegäran. 

## <a name="compression-behavior-tables"></a>Komprimering beteende tabeller
I följande tabeller beskrivs Azure CDN komprimering beteendet för varje scenario:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprimering är inaktiverat eller filen är inte tillgänglig för komprimering
| Klienten begärde format (kopplingshuvud Accept-Encoding) | Cachelagrade filformat | CDN-svar till klienten | Anteckningar&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade | |
| Komprimerade |Okomprimerad |Okomprimerad | |
| Komprimerade |Inte cachelagras |Komprimerad eller okomprimerad |Ursprung svaret avgör om CDN utför en komprimering. |
| Okomprimerad |Komprimerade |Okomprimerad | |
| Okomprimerad |Okomprimerad |Okomprimerad | |
| Okomprimerad |Inte cachelagras |Okomprimerad | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Komprimering är aktiverat och filen är berättigade för komprimering
| Klienten begärde format (kopplingshuvud Accept-Encoding) | Cachelagrade filformat | CDN-svar till klienten | Anteckningar |
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade |CDN transcodes mellan de format som stöds. |
| Komprimerade |Okomprimerad |Komprimerade |CDN utför en komprimering. |
| Komprimerade |Inte cachelagras |Komprimerade |CDN utför en komprimering om ursprung returnerar en okomprimerad fil. <br/>**Azure CDN från Verizon** skickar okomprimerad fil på den första begäranden och sedan komprimeras och cachelagrar filen för efterföljande förfrågningar. <br/>Filer med den `Cache-Control: no-cache` huvud komprimeras aldrig. |
| Okomprimerad |Komprimerade |Okomprimerad |CDN utför en dekomprimering. |
| Okomprimerad |Okomprimerad |Okomprimerad | |
| Okomprimerad |Inte cachelagras |Okomprimerad | |

## <a name="media-services-cdn-compression"></a>Media Services CDN komprimering
Komprimering är aktiverat som standard för följande MIME-typer för slutpunkter som aktiverats för strömning av Media Services CDN: 
- program/vnd.ms-sstr + xml 
- program/dash + xml
- application/vnd.Apple.mpegurl
- program/f4m + xml 

## <a name="see-also"></a>Se också
* [Felsöka CDN-filkomprimering](cdn-troubleshoot-compression.md)    

