---
title: Optimera Azure CDN för typ av leverans av innehåll
description: Optimera Azure CDN för typ av leverans av innehåll
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: magattus
ms.openlocfilehash: 526f3522bff05618189ad4f8205fbb61afc47fdc
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320329"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optimera Azure CDN för typ av leverans av innehåll

När du levererar innehåll till en stor global publik är det viktigt att optimerad leverans av ditt innehåll. [Azure CDN Content Delivery Network ()](cdn-overview.md) kan optimera leverans upplevelsen beroende på vilken typ av innehåll som du har. Innehållet kan vara en webbplats, en direktsänd dataström, en video eller en stor fil för hämtning. När du skapar en CDN-slutpunkt kan du ange ett scenario i den **optimerade för** alternativet. Ditt val avgör vilka optimering tillämpas på innehållet som levereras från CDN-slutpunkten.

Optimeringsalternativ är utformade för att använda bästa praxis beteenden för att förbättra prestanda för leverans av innehåll och avlastning för bättre ursprung. Valen scenario påverkar prestanda genom att ändra konfigurationer för partiella cachelagring, objektet storlekar och återförsöksprincipen ursprung fel. 

Den här artikeln innehåller en översikt över olika optimering funktioner och när du ska använda dem. Mer information om funktioner och begränsningar finns i respektive artiklar på varje enskild optimeringstyp.

> [!NOTE]
> När du skapar en CDN-slutpunkt i **optimerade för** alternativen kan variera beroende på vilken typ av slutpunkten har skapats i profilen. Azure CDN-leverantörer gäller förbättring på olika sätt, beroende på scenario. 

## <a name="provider-options"></a>Leverantörsalternativ

**Azure CDN Standard från Microsoft** profiler stöder följande optimeringar:

* [Allmän webbleverans](#general-web-delivery). Den här metoden används också för direktuppspelning och hämta stora filer.


**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** profiler stöder följande optimeringar:

* [Allmän webbleverans](#general-web-delivery). Den här metoden används också för direktuppspelning och hämta stora filer.

* [Acceleration av dynamisk webbplats](#dynamic-site-acceleration) 


**Azure CDN Standard från Akamai** profiler stöder följande optimeringar:

* [Allmän webbleverans](#general-web-delivery) 

* [Allmän mediedirektuppspelning](#general-media-streaming)

* [Direktuppspelning av video på begäran](#video-on-demand-media-streaming)

* [Nedladdning av stora filer](#large-file-download)

* [Acceleration av dynamisk webbplats](#dynamic-site-acceleration) 

Microsoft rekommenderar att du testar prestanda variationer mellan olika leverantörer att välja den optimala providern för ditt leveransschema.

## <a name="select-and-configure-optimization-types"></a>Välj och konfigurera optimeringstyper

När du skapar en CDN-slutpunkt, Välj en typ av lagringsoptimering som bäst passar scenariot och typ av innehåll som du vill att slutpunkten att leverera. **Allmän webbleverans** är standardvalet. För befintliga **Azure CDN Standard från Akamai** slutpunkter, kan du uppdatera optimeringsalternativ när som helst. Den här ändringen avbryta inte leverans från Azure CDN. 

1. I en **Azure CDN Standard från Akamai** profil, väljer du en slutpunkt.

    ![Val av slutpunkt ](./media/cdn-optimization-overview/01_Akamai.png)

2. Välj under inställningar **optimering**. Välj en typ från den **optimerade för** listrutan.

    ![Val av optimering och typ](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimering för specifika scenarier

Du kan optimera CDN-slutpunkten för en av dessa scenarier. 

### <a name="general-web-delivery"></a>Allmän webbleverans

Allmän webbleverans är det vanligaste alternativet för optimering. Den är utformad för allmänna web content optimering, till exempel webbsidor och webbprogram. Denna optimering kan också användas för fil och laddar ned video.

En typisk webbplats innehåller statiskt och dynamiskt innehåll. Statiskt innehåll innehåller bilder, JavaScript-bibliotek och formatmallar som kan cachelagras och levereras till olika användare. Dynamiskt innehåll anpassas för en enskild användare, till exempel news-objekt som är anpassade efter en användarprofil. Dynamiskt innehåll, till exempel i kundvagnen innehåll, cachelagras inte på grund av att det är unikt för varje användare. Allmän webbleverans kan optimera hela webbplatsen. 

> [!NOTE]
> Om du använder en **Azure CDN Standard från Akamai** profilen, Välj den här typen av slutpunktsoptimering om genomsnittliga filstorleken är mindre än 10 MB. Annars, om genomsnittliga filstorleken är större än 10 MB väljer **nedladdning av stora filer** från den **optimerade för** listrutan.

### <a name="general-media-streaming"></a>Allmän mediedirektuppspelning

Om du vill använda slutpunkten för liveströmning och video på begäran för direktuppspelning, välj Allmänt strömmande optimering medietyp.

Direktuppspelning av Media är tidskänslig, eftersom paket som kommer sent på klienten, till exempel ofta buffras, kan orsaka tittarens upplevelse. Optimering av mediedirektuppspelning minskar svarstiden för medieinnehållet levereras och ger en jämn direktuppspelning för användare. 

Det här scenariot är vanligt för Azure media service-kunder. När du använder Azure media services kan få du en enda slutpunkt för direktuppspelning som kan användas för direktuppspelning live och på begäran. Med det här scenariot behöver kunder inte växla till en annan slutpunkt när de ändrar från live att strömma på begäran. Allmän optimering av mediedirektuppspelning har stöd för den här typen av scenario.

För **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**, och **Azure CDN Premium från Verizon**, använda optimeringstypen allmän leverans till leverera Allmänt strömmande medieinnehåll.

Läs mer om optimering av mediedirektuppspelning [optimering av mediedirektuppspelning](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Direktuppspelning av video på begäran

Video på begäran optimering av mediedirektuppspelning förbättrar strömmande videoinnehåll. Använd det här alternativet om du använder en slutpunkt för direktuppspelning av video på begäran.

För **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**, och **Azure CDN Premium från Verizon** profiler kan använda leveransoptimering allmän Skriv för att leverera video på begäran strömmande medieinnehåll.

Läs mer om optimering av mediedirektuppspelning [optimering av mediedirektuppspelning](cdn-media-streaming-optimization.md).

> [!NOTE]
> Om CDN-slutpunkten har främst videoinnehåll, Använd den här typen av slutpunktsoptimering. Den största skillnaden mellan den här typen av slutpunktsoptimering och allmän mediedirektuppspelning optimeringstypen är timeout för återförsök. Timeout-värde är mycket kortare ska fungera med live direktuppspelning scenarier.
>

### <a name="large-file-download"></a>Nedladdning av stora filer

För **Azure CDN Standard från Akamai** användarprofiler, stora filer nedladdningar är optimerade för innehåll som är större än 10 MB. Om din genomsnittliga filstorleken är mindre än 10 MB, Använd allmän webbleverans. Om din genomsnittliga filstorlekar är konsekvent större än 10 MB, kan det vara mer effektivt att skapa en separat slutpunkt för stora filer. Inbyggd programvara eller programuppdateringar är till exempel vanligtvis stora filer. Optimering för nedladdning av stora filer krävs för att leverera filer större än 1,8 GB.

För **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon**, och **Azure CDN Premium från Verizon** profiler kan använda leveransoptimering allmän Skriv för att leverera innehåll för nedladdning av stora filer. Det finns ingen begränsning för filstorlek för hämtning.

Läs mer om optimering av stora filer, [optimering av stora filer](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Acceleration av dynamisk webbplats

 Acceleration av dynamisk webbplats (DSA) är tillgänglig för **Azure CDN Standard från Akamai**, **Azure CDN Standard från Verizon**, och **Azure CDN Premium från Verizon** profiler. Denna optimering omfattar en ytterligare avgift ska användas. Mer information finns i [prissättningen för CDN](https://azure.microsoft.com/pricing/details/cdn/).

DSA innehåller olika tekniker som dra svarstid och prestanda för dynamiskt innehåll. Tekniken omfattar rutten och nätverket optimering, optimering för TCP med mera. 

Du kan använda denna optimering för att påskynda en webbapp som innehåller ett stort antal svar som inte är komma. Exempel är sökresultat, Kolla in transaktioner eller data i realtid. Du kan fortsätta att använda cachelagring kärnfunktioner för Azure CDN för statiska data. 

Läs mer om acceleration av dynamisk webbplats [acceleration av dynamisk webbplats](cdn-dynamic-site-acceleration.md).



