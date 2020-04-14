---
title: Förbättra prestanda genom att komprimera filer i Azure CDN | Microsoft-dokument
description: Lär dig hur du förbättrar filöverföringshastigheten och ökar prestandan för sidbelastning genom att komprimera dina filer i Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 7124dd40d4510674014afe012a8f40dcb5bb6153
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253772"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Förbättra prestandan genom att komprimera filer i Azure CDN
Filkomprimering är en enkel och effektiv metod för att förbättra filöverföringshastigheten och öka sidbelastningens prestanda genom att minska en fils storlek innan den skickas från servern. Filkomprimering kan minska bandbreddskostnaderna och ge användarna en mer responsiv upplevelse.

Det finns två sätt att aktivera filkomprimering:

- Aktivera komprimering på ursprungsservern. I det här fallet skickar Azure CDN längs de komprimerade filerna och levererar dem till klienter som begär dem.
- Aktivera komprimering direkt på CDN POP-servrarna *(komprimering i farten).* I det här fallet komprimerar CDN filerna och tjänar dem till slutanvändarna, även om de inte komprimerades av ursprungsservern.

> [!IMPORTANT]
> Azure CDN-konfigurationsändringar kan ta lite tid att sprida sig via nätverket: 
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
> 
> Om du konfigurerar komprimering för första gången för CDN-slutpunkten kan du vänta 1-2 timmar innan du felsöker för att säkerställa att komprimeringsinställningarna har spridits till POP.If you're setting up compression for the first time for your CDN endpoint, consider waiting 1-2 hours before you troubleshoot to ensure the compression settings have propagated to the POP.

## <a name="enabling-compression"></a>Aktivera komprimering
Standard- och premium-CDN-nivåerna ger samma komprimeringsfunktioner, men användargränssnittet skiljer sig åt. Mer information om skillnaderna mellan standard- och premium-CDN-nivåer finns i [Azure CDN Overview](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Vanliga CDN-profiler 
> [!NOTE]
> Det här avsnittet gäller **Azure CDN Standard från Microsoft,** **Azure CDN Standard från Verizon**och Azure **CDN Standard från Akamai-profiler.**
> 
> 

1. På CDN-profilsidan väljer du den CDN-slutpunkt som du vill hantera.

    ![Slutpunkter för CDN-profil](./media/cdn-file-compression/cdn-endpoints.png)

    Cdn-slutpunktssidan öppnas.
2. Välj **Komprimering**.

    ![Val av CDN-komprimering](./media/cdn-file-compression/cdn-compress-select-std.png)

    Komprimeringssidan öppnas.
3. Välj **På** om du vill aktivera komprimering.

    ![Komprimeringsalternativ för CDN-filer](./media/cdn-file-compression/cdn-compress-standard.png)
4. Använd standardtyperna MIME eller ändra listan genom att lägga till eller ta bort MIME-typer.

   > [!TIP]
   > Även om det är möjligt rekommenderas det inte att använda komprimering på komprimerade format. Till exempel ZIP, MP3, MP4 eller JPG.
   > 

   > [!NOTE]
   > Det går inte att ändra standardlistan för MIME-typer i Azure CDN Standard från Microsoft.
   > 

5. När du har gjort ändringarna väljer du **Spara**.

### <a name="premium-cdn-profiles"></a>Premium CDN-profiler
> [!NOTE]
> Det här avsnittet gäller endast **Azure CDN Premium från** Verizon-profiler.
> 

1. Välj **Hantera**på CDN-profilsidan .

    ![Välj CDN-hantering](./media/cdn-file-compression/cdn-manage-btn.png)

    CDN-hanteringsportalen öppnas.
2. Hovra över fliken **HTTP Stor** och hovra sedan över den utfällbara **cacheinställningarna.** Välj **Komprimering**.

    ![Val av CDN-komprimering](./media/cdn-file-compression/cdn-compress-select.png)

    Komprimeringsalternativen visas.

    ![Komprimeringsalternativ för CDN-filer](./media/cdn-file-compression/cdn-compress-files.png)
3. Aktivera komprimering genom att välja **Komprimeringsaktiverad**. Ange de MIME-typer som du vill komprimera som en kommaavgränsad lista (inga blanksteg) i rutan **Filtyper.**

   > [!TIP]
   > Även om det är möjligt rekommenderas det inte att använda komprimering på komprimerade format. Till exempel ZIP, MP3, MP4 eller JPG.
   > 

4. När du har gjort ändringarna väljer du **Uppdatera**.

## <a name="compression-rules"></a>Komprimeringsregler

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard från Microsoft-profiler

För **Azure CDN Standard från Microsoft-profiler** komprimeras endast kvalificerade filer. För att vara berättigad till komprimering måste en fil:
- Vara av en MIME-typ som har [konfigurerats för komprimering](#enabling-compression).
- Var större än 1 KB
- Var mindre än 8 MB

Dessa profiler stöder följande komprimeringskodningar:
- gzip (GNU zip)
- brotli (brotli) 

Om begäran stöder mer än en komprimeringstyp har brotlikomprimering företräde.

När en begäran om en tillgång anger gzip-komprimering och begäran resulterar i en cachemiss, utför Azure CDN gzip-komprimering av tillgången direkt på POP-servern. Därefter visas den komprimerade filen från cacheminnet.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN från Verizon-profiler

För **Azure CDN Standard från Verizon** och Azure **CDN Premium från Verizon-profiler** komprimeras endast kvalificerade filer. För att vara berättigad till komprimering måste en fil:
- Var större än 128 byte
- Var mindre än 3 MB

Dessa profiler stöder följande komprimeringskodningar:
- gzip (GNU zip)
- Tömma
- bzip2 (på andra)
- brotli (brotli) 

Om begäran stöder mer än en komprimeringstyp har dessa komprimeringstyper företräde framför brotlikomprimering.

När en begäran om en tillgång anger brotlikomprimering (HTTP-huvudet är) `Accept-Encoding: br`och begäran resulterar i en cachemiss, utför Azure CDN brotli-komprimering av tillgången direkt på POP-servern. Därefter visas den komprimerade filen från cacheminnet.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard från Akamai-profiler

För **Azure CDN Standard från Akamai-profiler** är alla filer kvalificerade för komprimering. En fil måste dock vara av en MIME-typ som har [konfigurerats för komprimering](#enabling-compression).

Dessa profiler stöder gzip-komprimeringskodning. När en profilslutpunkt begär en gzip-kodad fil begärs den alltid från ursprunget, oavsett klientbegäran. 

## <a name="compression-behavior-tables"></a>Tabeller för komprimeringsbeteende
I följande tabeller beskrivs Azure CDN-komprimeringsbeteende för alla scenarier:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprimeringen är inaktiverad eller filen är inte berättigad till komprimering
| Klientbe efterfrågat format (via Acceptera-kodningshuvud) | Cachelagrad filformat | CDN-svaret på klienten | &nbsp; &nbsp; Anteckningar&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade | |
| Komprimerade |Okomprimerade |Okomprimerade | |
| Komprimerade |Inte cachelagrat |Komprimerad eller okomprimerad |Ursprungssvaret avgör om CDN utför en komprimering. |
| Okomprimerade |Komprimerade |Okomprimerade | |
| Okomprimerade |Okomprimerade |Okomprimerade | |
| Okomprimerade |Inte cachelagrat |Okomprimerade | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Komprimering är aktiverat och filen kan komprimera
| Klientbe efterfrågat format (via Acceptera-kodningshuvud) | Cachelagrad filformat | CDN-svar på klienten | Anteckningar |
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade |CDN-kodningar mellan format som stöds. |
| Komprimerade |Okomprimerade |Komprimerade |CDN utför en komprimering. |
| Komprimerade |Inte cachelagrat |Komprimerade |CDN utför en komprimering om ursprunget returnerar en okomprimerad fil. <br/>**Azure CDN från Verizon** skickar den okomprimerade filen på den första begäran och komprimerar och cachelagrar sedan filen för efterföljande begäranden. <br/>Filer med `Cache-Control: no-cache` sidhuvudet komprimeras aldrig. |
| Okomprimerade |Komprimerade |Okomprimerade |CDN utför en dekompression. |
| Okomprimerade |Okomprimerade |Okomprimerade | |
| Okomprimerade |Inte cachelagrat |Okomprimerade | |

## <a name="media-services-cdn-compression"></a>CDN-komprimering av Media Services
För slutpunkter som är aktiverade för CDN-direktuppspelning av Media Services är komprimering aktiverad som standard för följande MIME-typer: 
- program/vnd.ms-sstr+xml 
- program/streck+xml
- ansökan/vnd.apple.mpegurl
- program/f4m+xml 

## <a name="see-also"></a>Se även
* [Felsöka CDN-filkomprimering](cdn-troubleshoot-compression.md)    

