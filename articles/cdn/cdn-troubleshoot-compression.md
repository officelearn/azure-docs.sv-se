---
title: Felsöka filkomprimering i Azure CDN | Microsoft-dokument
description: Felsöka problem med Azure CDN-filkomprimering.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: aff2dadee365fcdc7e14070714aa1d2cbba901ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476431"
---
# <a name="troubleshooting-cdn-file-compression"></a>Felsöka CDN-filkomprimering
Den här artikeln hjälper dig att felsöka problem med [CDN-filkomprimering](cdn-improve-performance.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Alternativt kan du också arkivera en Azure-supportincident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/) och klicka på **Hämta support**.

## <a name="symptom"></a>Symptom
Komprimering för slutpunkten är aktiverat, men filer returneras okomprimerade.

> [!TIP]
> Om du vill kontrollera om dina filer returneras komprimerade måste du använda ett verktyg som [Fiddler](https://www.telerik.com/fiddler) eller webbläsarens [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Kontrollera http-svarshuvudena som returneras med det cachelagrade CDN-innehållet.  Om det finns `Content-Encoding` ett sidhuvud med värdet **gzip**, **bzip2**eller **töms**komprimeras innehållet.
> 
> ![Rubrik för innehållskodning](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Orsak
Det finns flera möjliga orsaker, inklusive:

* Det begärda innehållet kan inte komprimering.
* Komprimering är inte aktiverat för den begärda filtypen.
* HTTP-begäran innehöll inte ett huvud som begärde en giltig komprimeringstyp.
* Origin skickar segmenterat innehåll.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!TIP]
> Precis som med distribution av nya slutpunkter tar det lite tid att sprida cdn-konfigurationsändringar genom nätverket.  Vanligtvis tillämpas ändringar inom 90 minuter.  Om det är första gången du konfigurerar komprimering för CDN-slutpunkten bör du överväga att vänta 1-2 timmar för att vara säker på att komprimeringsinställningarna har spridits till POP.If this is the first time you've set up compression for your CDN endpoint, you should consider waiting 1-2 hours to be sure the compression settings have propagated to the POPs. 
> 
> 

### <a name="verify-the-request"></a>Verifiera begäran
Först bör vi göra en snabb förståndskontroll på begäran.  Du kan använda webbläsarens [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) för att visa de begäranden som görs.

* Kontrollera att begäran skickas till slutpunkts-URL:en `<endpointname>.azureedge.net`och inte ditt ursprung.
* Kontrollera att begäran innehåller ett **Acceptera-kodningshuvud** och värdet för det huvudet innehåller **gzip**, **deflate**eller **bzip2**.

> [!NOTE]
> **Azure CDN från Akamai-profiler** stöder endast **gzip-kodning.**
> 
> 

![HUVUDEN för CDN-begäran](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Verifiera komprimeringsinställningar (vanliga CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Standard från Microsoft,** **Azure CDN Standard från Verizon**eller Azure **CDN Standard från Akamai-profilen.** 
> 
> 

Navigera till slutpunkten i [Azure-portalen](https://portal.azure.com) och klicka på knappen **Konfigurera.**

* Kontrollera att komprimeringen är aktiverad.
* Kontrollera MIME-typen för innehållet som ska komprimeras finns med i listan över komprimerade format.

![Inställningar för CDN-komprimering](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Verifiera komprimeringsinställningar (Premium CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Premium från** Verizon-profil.
> 
> 

Navigera till slutpunkten i [Azure-portalen](https://portal.azure.com) och klicka på knappen **Hantera.**  Den kompletterande portalen öppnas.  Hovra över fliken **HTTP Stor** och hovra sedan över den utfällbara **cacheinställningarna.**  Klicka på **Komprimering**. 

* Kontrollera att komprimeringen är aktiverad.
* Kontrollera att listan **Filtyper** innehåller en kommaavgränsad lista (inga blanksteg) av MIME-typer.
* Kontrollera MIME-typen för innehållet som ska komprimeras finns med i listan över komprimerade format.

![Inställningar för CDN premiumkomprimering](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Kontrollera att innehållet cachelagras (Verizon CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Standard från Verizon** eller Azure **CDN Premium från** Verizon-profilen.
> 
> 

Kontrollera svarsrubrikerna med hjälp av webbläsarens utvecklarverktyg för att säkerställa att filen cachelagras i den region där den begärs.

* Kontrollera **serversvarshuvudet.**  Rubriken ska ha formatet **Platform (POP/Server ID),** vilket visas i följande exempel.
* Kontrollera svarshuvudet för **X-Cache.**  Rubriken ska läsa **HIT**.  

![CDN-svarshuvuden](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Kontrollera att filen uppfyller storlekskraven (Verizon CDN-profiler)
> [!NOTE]
> Det här steget gäller endast om din CDN-profil är en **Azure CDN Standard från Verizon** eller Azure **CDN Premium från** Verizon-profilen.
> 
> 

För att vara berättigad till komprimering måste en fil uppfylla följande storlekskrav:

* Större än 128 byte.
* Mindre än 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kontrollera begäran på ursprungsservern för ett **Via-huvud**
**Via** HTTP-huvudet anger för webbservern att begäran skickas av en proxyserver.  Microsoft IIS-webbservrar komprimerar som standard inte **Via** svar när begäran innehåller ett Via-huvud.  Så här åsidosätter du detta:

* **IIS 6**: [Ställ in HcNoCompressionForProxies="FALSE" i IIS-metabasegenskaperna](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 och up**: [Ställ in både **noCompressionForHttp10** och **noCompressionForProxies** till False i serverkonfigurationen](https://www.iis.net/configreference/system.webserver/httpcompression)

