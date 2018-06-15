---
title: Felsöka filkomprimering i Azure CDN | Microsoft Docs
description: Felsöka problem med Azure CDN komprimering.
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
ms.openlocfilehash: 14d50cb7cac77af75dd4b7293812154d1f24e47c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765532"
---
# <a name="troubleshooting-cdn-file-compression"></a>Felsöka CDN-filkomprimering
Den här artikeln hjälper dig att felsöka problem med [CDN filkomprimering](cdn-improve-performance.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du även filen en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och på **Get Support**.

## <a name="symptom"></a>Symtom
Komprimering för din slutpunkt har aktiverats men filer returneras okomprimerade.

> [!TIP]
> Om du vill kontrollera om filerna returneras komprimerade, måste du använda ett verktyg som [Fiddler](http://www.telerik.com/fiddler) eller din webbläsare [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Kontrollera HTTP-svarshuvuden returneras med den cachelagrade CDN innehåll.  Om det finns en rubrik med namnet `Content-Encoding` med värdet **gzip**, **bzip2**, eller **deflate**, ditt innehåll komprimeras.
> 
> ![Innehållskodning sidhuvud](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Orsak
Det finns flera möjliga orsaker, bland annat:

* Det begärda innehållet är inte tillgänglig för komprimering.
* Komprimering är inte aktiverat för den begärda filen.
* HTTP-begäran innehöll inte en rubrik som begär en giltig Komprimeringstypen.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!TIP]
> Som distribuerar nya slutpunkter, ta CDN konfigurationsändringar lite tid att spridas via nätverket.  Vanligtvis tillämpas ändringar inom 90 minuter.  Om du har konfigurerat komprimering för CDN-slutpunkten bör du vänta 1 – 2 timmar att komprimering inställningar har spridits till POP. 
> 
> 

### <a name="verify-the-request"></a>Kontrollera begäran
Först ska vi göra en snabb förstånd kontroll på begäran.  Du kan använda din webbläsare [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) att se begäranden som gjorts.

* Kontrollera begäran skickas till slutpunkts-URL `<endpointname>.azureedge.net`, och inte din ursprung.
* Kontrollera begäran innehåller en **Accept-Encoding** sidhuvud och värdet för det huvudet innehåller **gzip**, **deflate**, eller **bzip2**.

> [!NOTE]
> **Azure CDN från Akamai** profiler endast stöder **gzip** kodning.
> 
> 

![CDN-huvuden för begäran](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Kontrollera inställningarna för komprimering (standard CDN profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profilen är en **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**, eller **Azure CDN Standard från Akamai** profil. 
> 
> 

Navigera till din slutpunkt i den [Azure-portalen](https://portal.azure.com) och klicka på den **konfigurera** knappen.

* Kontrollera komprimering har aktiverats.
* Kontrollera att MIME-typ för innehållet som kan komprimeras ingår i listan i komprimerat format.

![CDN-inställningarna för komprimering](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Kontrollera inställningarna för komprimering (Premium CDN profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profilen är en **Azure CDN Premium från Verizon** profil.
> 
> 

Navigera till din slutpunkt i den [Azure-portalen](https://portal.azure.com) och klicka på den **hantera** knappen.  Den kompletterande portalen öppnas.  Hovra över den **HTTP stora** och klicka sedan hovra över den **cacheinställningarna** utfällbar.  Klicka på **komprimering**. 

* Kontrollera komprimering har aktiverats.
* Kontrollera den **filtyper** listan innehåller en kommaavgränsad lista (inga blanksteg) över MIME-typer.
* Kontrollera att MIME-typ för innehållet som kan komprimeras ingår i listan i komprimerat format.

![CDN premium komprimeringsinställningar](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Kontrollera innehållet är cachelagrade (Verizon CDN profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profilen är en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil.
> 
> 

Kontrollera med din webbläsare utvecklingsverktyg svarshuvuden för att se till att filen lagras i den region där den begärs.

* Kontrollera den **Server** Svarsrubrik.  Huvudet ska ha formatet **plattform (POP-Server-ID)**, som visas i följande exempel.
* Kontrollera den **X-Cache** Svarsrubrik.  Huvudet bör du läsa **NÅDDE**.  

![CDN-svarshuvuden](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Kontrollera att filen uppfyller storlek (Verizon CDN profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profilen är en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil.
> 
> 

För att få komprimering, måste en fil uppfylla följande krav för storlek:

* Större än 128 tecken.
* Mindre än 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kontrollera begäran på den ursprungliga servern en **Via** sidhuvud
Den **Via** HTTP-huvud som anger att webbservern att begäran som skickas via en proxyserver.  Microsoft IIS-webbservrar som standard komprimera svar när begäran innehåller en **Via** huvud.  Om du vill åsidosätta detta genom att utföra följande:

* **IIS 6**: [ange egenskaperna HcNoCompressionForProxies = ”FALSE” i egenskaperna för IIS-metabasen](https://msdn.microsoft.com/library/ms525390.aspx)
* **IIS 7 och senare**: [anger både **noCompressionForHttp10** och **noCompressionForProxies** till False i serverkonfigurationen](http://www.iis.net/configreference/system.webserver/httpcompression)

