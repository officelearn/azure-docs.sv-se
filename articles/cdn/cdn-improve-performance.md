---
title: Förbättra prestanda genom att komprimera filer i Azure CDN | Microsoft Docs
description: Lär dig att förbättra hastigheten för överföring av filen och öka läsa in sidan prestanda genom att komprimera filer i Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: magattus
ms.openlocfilehash: c3a20bd4fa1cccdca7cba0de52620f09fe01abc5
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42056752"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Förbättra prestanda genom att komprimera filer i Azure CDN
Filkomprimering är en enkel och effektiv metod för att förbättra hastigheten för överföring av filen och öka läsa in sidan prestanda genom att minska storleken på en fil innan den skickas från servern. Filkomprimering kan minska kostnader för bandbredd och tillhandahålla en mer gensvar upplevelse för användarna.

Det finns två sätt att aktivera filkomprimering:

- Aktivera komprimering på ursprungsservern. I det här fallet Azure CDN skickar med komprimerade filer och skickar dem till klienter som begär dem.
- Aktivera komprimering direkt på CDN POP-servrar (*komprimering direkt*). I det här fallet CDN komprimerar filerna och fungerar för slutanvändarna, även om de inte komprimerats av den ursprungliga servern.

> [!IMPORTANT]
> Azure CDN-konfigurationsändringar kan ta lite tid att sprida via nätverket: 
- För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
- För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
- För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
>
> Överväg att vänta 1 till 2 timmar innan du felsöka för att säkerställa att komprimeringsinställningarna har spritts till POP om du lägger upp komprimering för första gången för CDN-slutpunkten.
> 
> 

## <a name="enabling-compression"></a>Aktivera komprimering
CDN-nivåerna standard och premium ger samma komprimering funktioner, men användargränssnittet skiljer sig åt. Mer information om skillnaderna mellan standard och premium CDN-nivåer finns i [översikt över Azure CDN](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standard CDN-profiler 
> [!NOTE]
> Det här avsnittet gäller för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**, och **Azure CDN Standard från Akamai** profiler.
> 
> 

1. Välj CDN-slutpunkten som du vill hantera CDN-profilsidan.
   
    ![CDN-slutpunkter för profilen](./media/cdn-file-compression/cdn-endpoints.png)
   
    CDN-slutpunkt-sida öppnas.
2. Välj **komprimering**.

    ![Val av CDN-komprimering](./media/cdn-file-compression/cdn-compress-select-std.png)
   
    Då öppnas sidan komprimering.
3. Välj **på** aktivera komprimering.
   
    ![CDN Filalternativ för komprimering](./media/cdn-file-compression/cdn-compress-standard.png)
4. Använd standard-MIME-typer eller ändra listan genom att lägga till eller ta bort MIME-typer.
   
   > [!TIP]
   > Även om det är möjligt, rekommenderas det inte gäller komprimering för komprimerade format. Till exempel ZIP, MP3, MP4- eller JPG.
   > 
   
   > [!NOTE]
   > Ändra listan över MIME-typer stöds för närvarande inte i Azure CDN Standard från Microsoft.
   > 
 
5. När du har gjort ändringarna, Välj **spara**.

### <a name="premium-cdn-profiles"></a>Premium CDN-profiler
> [!NOTE]
> Det här avsnittet gäller endast **Azure CDN Premium från Verizon** profiler.
> 

1. CDN-profilsidan väljer **hantera**.
   
    ![Välj CDN hantera](./media/cdn-file-compression/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **HTTP stora** och sedan hovra över den **cacheinställningarna** utfällbar meny. Välj **komprimering**.

    ![Val av CDN-komprimering](./media/cdn-file-compression/cdn-compress-select.png)
   
    Komprimeringsalternativ visas.
   
    ![CDN Filalternativ för komprimering](./media/cdn-file-compression/cdn-compress-files.png)
3. Aktivera komprimering genom att välja **komprimering aktiverat**. Ange MIME-typer som du vill komprimera som en kommaavgränsad lista (utan blanksteg) i den **filtyper** box.
   
   > [!TIP]
   > Även om det är möjligt, rekommenderas det inte gäller komprimering för komprimerade format. Till exempel ZIP, MP3, MP4- eller JPG.
   > 
    
4. När du har gjort ändringarna, Välj **uppdatering**.

## <a name="compression-rules"></a>Regler för komprimering

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard från Microsoft-profiler

För **Azure CDN Standard från Microsoft** profiler, alla filer som är kvalificerade för komprimering. Men en fil måste vara av en MIME-typ som har varit [konfigurerats för komprimering](#enabling-compression).

Dessa profiler stöder följande komprimering skriver:
- gzip (GNU zip)
- brotli 
 
Om begäran har stöd för fler än en typ av komprimering, åsidosätter dessa komprimeringstyper brotli komprimering.

När en begäran för en tillgång anger gzip komprimering och begäran resultaten i en cachemiss, utför Azure CDN gzip-komprimering av tillgången direkt på POP-servern. Därefter hämtas den komprimerade filen från cachen.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN från Verizon-profiler

För **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** profiler kan bara filer komprimeras. Ska vara kvalificerat för komprimering, måste en fil
- Vara större än 128 byte
- Vara mindre än 1 MB
 
Dessa profiler stöder följande komprimering skriver:
- gzip (GNU zip)
- SMAL
- bzip2
- brotli 
 
Om begäran har stöd för fler än en typ av komprimering, åsidosätter dessa komprimeringstyper brotli komprimering.

När en begäran för en tillgång anger brotli komprimering (HTTP-huvud är `Accept-Encoding: br`) och begäran resulterar i en cachemiss, Azure CDN utför brotli komprimering av tillgången direkt på POP-servern. Därefter hämtas den komprimerade filen från cachen.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard från Akamai-profiler

För **Azure CDN Standard från Akamai** profiler, alla filer som är kvalificerade för komprimering. Men en fil måste vara av en MIME-typ som har varit [konfigurerats för komprimering](#enabling-compression).

De här profilerna stöd för gzip komprimering bara kodning. När en profil slutpunkt begär en gzip-kodad fil, är det alltid begäras från ursprunget oavsett klientens begäran. 

## <a name="compression-behavior-tables"></a>Komprimering beteende tabeller
Följande tabeller beskriver Azure CDN komprimering beteendet för varje scenario:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprimering är inaktiverad eller filen är inte berättigade för komprimering
| Klienten har begärt format (via Accept-Encoding rubrik) | Cachelagrade filformat | CDN-svar till klienten | Anteckningar&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade | |
| Komprimerade |Okomprimerade |Okomprimerade | |
| Komprimerade |Inte cachelagras |Komprimerad eller okomprimerad |Det ursprungliga svaret avgör om CDN utför en komprimering. |
| Okomprimerade |Komprimerade |Okomprimerade | |
| Okomprimerade |Okomprimerade |Okomprimerade | |
| Okomprimerade |Inte cachelagras |Okomprimerade | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Komprimering är aktiverat och filen är berättigade för komprimering
| Klienten har begärt format (via Accept-Encoding rubrik) | Cachelagrade filformat | CDN-svar till klienten | Anteckningar |
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade |CDN omkodar mellan de format som stöds. |
| Komprimerade |Okomprimerade |Komprimerade |CDN utför en komprimering. |
| Komprimerade |Inte cachelagras |Komprimerade |CDN utför en komprimering om ursprunget returnerar en okomprimerad fil. <br/>**Azure CDN från Verizon** skickar okomprimerad fil på den första begäran och sedan komprimerar och cachelagrar filen för efterföljande förfrågningar. <br/>Filer med den `Cache-Control: no-cache` rubrik komprimeras aldrig. |
| Okomprimerade |Komprimerade |Okomprimerade |CDN utför en dekomprimering. |
| Okomprimerade |Okomprimerade |Okomprimerade | |
| Okomprimerade |Inte cachelagras |Okomprimerade | |

## <a name="media-services-cdn-compression"></a>Media Services-CDN-komprimering
Aktiverat för Media Services CDN strömmande slutpunkter, är komprimering aktiverat som standard för följande MIME-typer: 
- program/vnd.ms-sstr + xml 
- program/dash + xml
- application/vnd.Apple.mpegurl
- program/f4m + xml 

## <a name="see-also"></a>Se också
* [Felsöka CDN-filkomprimering](cdn-troubleshoot-compression.md)    

