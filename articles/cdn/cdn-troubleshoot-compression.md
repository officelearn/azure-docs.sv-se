---
title: Felsöka i Azure CDN-filkomprimering | Microsoft Docs
description: Felsök problem med Azure CDN-filkomprimering.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 2a41316eadb43145628d6c625935c751bfbc6ad6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531536"
---
# <a name="troubleshooting-cdn-file-compression"></a>Felsöka CDN-filkomprimering
Den här artikeln hjälper dig att felsöka problem med [CDN-filkomprimering](cdn-improve-performance.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan alternativt kan du även skicka en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och klicka på **få Support**.

## <a name="symptom"></a>Symtom
Komprimering för din slutpunkt är aktiverat, men filer returneras okomprimerade.

> [!TIP]
> Om du vill kontrollera om dina filer returneras komprimerade, måste du använda ett verktyg som [Fiddler](https://www.telerik.com/fiddler) eller webbläsarens [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Kontrollera HTTP-svarshuvuden returneras med dina cachelagrade CDN innehåll.  Om det finns en rubrik med namnet `Content-Encoding` med värdet **gzip**, **bzip2**, eller **deflate**, ditt innehåll komprimeras.
> 
> ![Content-Encoding-huvud](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Orsak
Det finns flera tänkbara orsaker, bland annat:

* Det begärda innehållet är inte berättigade för komprimering.
* Komprimering är inte aktiverat för den begärda filtypen.
* HTTP-begäran innehöll inte en rubrik som begär en giltig Komprimeringstypen.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!TIP]
> Precis som med distribuera nya slutpunkter ta konfigurationsändringar i CDN lite tid att sprida via nätverket.  Ändringarna tillämpas vanligtvis inom 90 minuter.  Om det här är första gången du har konfigurerat komprimering för CDN-slutpunkten bör du överväga att vänta 1 – 2 timmar att komprimering inställningar har spritts till POP. 
> 
> 

### <a name="verify-the-request"></a>Kontrollera begäran
Först ska vi göra en snabb förstånd kontroll på begäran.  Du kan använda din webbläsares [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) att visa förfrågningar som görs.

* Kontrollera begäran som skickas till din slutpunkts-URL `<endpointname>.azureedge.net`, och inte ditt ursprung.
* Kontrollera begäran innehåller ett **Accept-Encoding** rubrik och värdet för det huvudet innehåller **gzip**, **deflate**, eller **bzip2**.

> [!NOTE]
> **Azure CDN från Akamai** profiler endast stöder **gzip** kodning.
> 
> 

![CDN-begärandehuvuden](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Kontrollera inställningarna för komprimering (standard CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**, eller **Azure CDN Standard från Akamai** profil. 
> 
> 

Gå till din slutpunkt i den [Azure-portalen](https://portal.azure.com) och klicka på den **konfigurera** knappen.

* Kontrollera komprimering är aktiverat.
* Kontrollera MIME-typ för innehållet som kan komprimeras ingår i listan över komprimerade format.

![CDN-inställningarna för komprimering](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Kontrollera inställningarna för komprimering (Premium CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Premium från Verizon** profil.
> 
> 

Gå till din slutpunkt i den [Azure-portalen](https://portal.azure.com) och klicka på den **hantera** knappen.  Den kompletterande portalen öppnas.  Hovra över den **HTTP stora** och sedan hovra över den **cacheinställningarna** utfällbar meny.  Klicka på **komprimering**. 

* Kontrollera komprimering är aktiverat.
* Kontrollera den **filtyper** listan innehåller en kommaavgränsad lista (utan blanksteg) över MIME-typer.
* Kontrollera MIME-typ för innehållet som kan komprimeras ingår i listan över komprimerade format.

![Komprimeringsinställningarna för CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Kontrollera innehållet är cachelagrade (Verizon CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil.
> 
> 

Kontrollera med din webbläsare utvecklarverktyg svarshuvuden för att säkerställa att filen lagras i regionen där det har begärts.

* Kontrollera den **Server** svarshuvudet.  Huvudet ska ha formatet **plattform (POP/Server-ID)**, som visas i följande exempel.
* Kontrollera den **X-Cache** svarshuvudet.  Rubriken ska läsa **når**.  

![CDN-svarshuvuden](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Kontrollera att filen uppfyller storlekskraven (Verizon CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil.
> 
> 

Ska vara kvalificerat för komprimering, måste en fil uppfylla följande krav för storlek:

* Större än 128 byte.
* Mindre än 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kontrollera begäran på den ursprungliga servern en **Via** rubrik
Den **Via** HTTP-huvud som anger till webbservern att begäran som skickas av en proxyserver.  Microsoft IIS-webbservrar som standard ska du inte komprimera svar när begäran innehåller en **Via** rubrik.  Om du vill åsidosätta detta beteende, utför du följande:

* **IIS 6**: [Ange egenskaperna HcNoCompressionForProxies = ”FALSE” i egenskaperna för IIS-metabas](https://msdn.microsoft.com/library/ms525390.aspx)
* **IIS 7 och senare**: [Ställ in både **noCompressionForHttp10** och **noCompressionForProxies** till False i serverkonfigurationen](http://www.iis.net/configreference/system.webserver/httpcompression)

