---
title: Optimera Azure CDN för typ av innehållsleverans
description: Optimera Azure CDN för typ av innehållsleverans
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 473636dc95d96ea348a42ec0f1090029bf3a7728
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260472"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optimera Azure CDN för typ av innehållsleverans

När du levererar innehåll till en stor global publik är det viktigt att säkerställa en optimerad leverans av ditt innehåll. [Cdn (Azure Content Delivery Network)](cdn-overview.md) kan optimera leveransupplevelsen baserat på vilken typ av innehåll du har. Innehållet kan vara en webbplats, en livestream, en video eller en stor fil för nedladdning. När du skapar en CDN-slutpunkt anger du ett scenario i alternativet **Optimerad för.** Ditt val avgör vilken optimering som tillämpas på innehållet som levereras från CDN-slutpunkten.

Optimeringsalternativ är utformade för att använda beteenden med bästa praxis för att förbättra innehållsleveransprestanda och bättre avlastning av ursprung. Dina scenarioval påverkar prestanda genom att ändra konfigurationer för partiell cachelagring, objektsegmentering och principen om återförsöksförsök för ursprungsfel. 

Den här artikeln innehåller en översikt över olika optimeringsfunktioner och när du ska använda dem. Mer information om funktioner och begränsningar finns i respektive artiklar om varje enskild optimeringstyp.

> [!NOTE]
> När du skapar en CDN-slutpunkt kan alternativen **Optimerad för** variera beroende på vilken typ av profil slutpunkten skapas i. Azure CDN-providers tillämpar förbättringar på olika sätt, beroende på scenariot. 

## <a name="provider-options"></a>Alternativ för leverantör

**Azure CDN Standard från Microsoft-profiler** stöder följande optimeringar:

* [Allmän webbleverans](#general-web-delivery). Denna optimering används också för media streaming och stor fil nedladdning.

> [!NOTE]
> Dynamisk webbplatsacceleration från Microsoft erbjuds via [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon-profiler** stöder följande optimeringar:

* [Allmän webbleverans](#general-web-delivery). Denna optimering används också för media streaming och stor fil nedladdning.

* [Dynamisk webbplatsacceleration](#dynamic-site-acceleration) 


**Azure CDN Standard från Akamai-profiler** stöder följande optimeringar:

* [Allmän webbleverans](#general-web-delivery) 

* [Allmän medieströmning](#general-media-streaming)

* [Video-on-demand media streaming](#video-on-demand-media-streaming)

* [Stor fil nedladdning](#large-file-download)

* [Dynamisk webbplatsacceleration](#dynamic-site-acceleration) 

Microsoft rekommenderar att du testar prestandavariationer mellan olika leverantörer för att välja den optimala leverantören för din leverans.

## <a name="select-and-configure-optimization-types"></a>Välja och konfigurera optimeringstyper

När du skapar en CDN-slutpunkt väljer du en optimeringstyp som bäst matchar scenariot och typen av innehåll som du vill att slutpunkten ska leverera. **Allmän webbleverans** är standardvalet. Endast för befintliga **Azure CDN Standard från Akamai-slutpunkter** kan du uppdatera optimeringsalternativet när som helst. Den här ändringen avbryter inte leveransen från Azure CDN. 

1. Välj en slutpunkt i en **Azure CDN Standard från Akamai-profil.**

    ![Markering av slutpunkt](./media/cdn-optimization-overview/01_Akamai.png)

2. Under INSTÄLLNINGAR väljer du **Optimering**. Välj sedan en typ i **listrutan Optimerad för.**

    ![Optimering och typval](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimering för specifika scenarier

Du kan optimera CDN-slutpunkten för ett av dessa scenarier. 

### <a name="general-web-delivery"></a>Allmän webbleverans

Allmän webbleverans är det vanligaste optimeringsalternativet. Den är utformad för allmän optimering av webbinnehåll, till exempel webbsidor och webbapplikationer. Denna optimering kan också användas för fil- och videonedladdningar.

En typisk webbplats innehåller statiskt och dynamiskt innehåll. Statiskt innehåll innehåller bilder, JavaScript-bibliotek och formatmallar som kan cachelagras och levereras till olika användare. Dynamiskt innehåll anpassas för en enskild användare, till exempel nyhetsobjekt som är anpassade till en användarprofil. Dynamiskt innehåll, till exempel kundvagnsinnehåll, cachelagras inte eftersom det är unikt för varje användare. Allmän webbleverans kan optimera hela din webbplats. 

> [!NOTE]
> Om du använder en **Azure CDN Standard från Akamai-profil** väljer du den här optimeringstypen om den genomsnittliga filstorleken är mindre än 10 MB. Annars väljer du **Stor filhämtning** i **listrutan Optimerad för den nedrullningsbara** listan Om den genomsnittliga filstorleken är större än 10 MB.

### <a name="general-media-streaming"></a>Allmän medieströmning

Om du behöver använda slutpunkten för direktuppspelning och direktuppspelning av video på begäran väljer du den allmänna optimeringstypen för direktuppspelning av media.

Mediestreaming är tidskänsligt, eftersom paket som kommer sent på klienten, till exempel frekvent buffring av videoinnehåll, kan orsaka en försämrad visningsupplevelse. Optimering av medieinnehåll minskar latensen för leverans av medieinnehåll och ger användarna en smidig direktuppspelningsupplevelse. 

Det här scenariot är vanligt för Azure media servicekunder. När du använder Azure-medietjänster får du en enda slutpunkt för direktuppspelning som kan användas för både direktuppspelning och direktuppspelning på begäran. I det här scenariot behöver kunderna inte växla till en annan slutpunkt när de ändras från direktuppspelning på begäran. Allmän optimering av medieströmning stöder den här typen av scenario.

För **Azure CDN Standard från Microsoft,** **Azure CDN Standard från Verizon**och Azure **CDN Premium från Verizon**använder du den allmänna optimeringstypen för webbleverans för att leverera allmänt strömmande medieinnehåll.

Mer information om optimering av direktuppspelning av media finns i [Optimering av direktuppspelning](cdn-media-streaming-optimization.md)av media .

### <a name="video-on-demand-media-streaming"></a>Video-on-demand media streaming

Optimering av direktuppspelning av video på begäran förbättrar direktuppspelade innehåll för video på begäran. Om du använder en slutpunkt för direktuppspelning på begäran använder du det här alternativet.

För **Azure CDN Standard från Microsoft,** **Azure CDN Standard från Verizon**och Azure **CDN Premium från Verizon-profiler** använder du den allmänna optimeringstypen för webbleverans för att leverera direktmediainnehåll som är video på begäran.

Mer information om optimering av direktuppspelning av media finns i [Optimering av direktuppspelning](cdn-media-streaming-optimization.md)av media .

> [!NOTE]
> Om CDN-slutpunkten i första hand betjänar innehåll på begäran använder du den här optimeringstypen. Den största skillnaden mellan den här optimeringstypen och den allmänna optimeringstypen för direktuppspelning av media är time-outen för återförsök. Time-out är mycket kortare att arbeta med live streaming scenarier.
>

### <a name="large-file-download"></a>Stor fil nedladdning

För **Azure CDN Standard från Akamai-profiler** är stora filhämtningar optimerade för innehåll som är större än 10 MB. Om den genomsnittliga filstorleken är mindre än 10 MB använder du allmän webbleverans. Om dina genomsnittliga filstorlekar är genomgående större än 10 MB kan det vara mer effektivt att skapa en separat slutpunkt för stora filer. Till exempel är firmware eller programuppdateringar vanligtvis stora filer. För att leverera filer som är större än 1,8 GB krävs optimering av den stora filhämtningen.

För **Azure CDN Standard från Microsoft,** **Azure CDN Standard från Verizon**och Azure **CDN Premium från Verizon-profiler** använder du den allmänna optimeringstypen för webbleverans för att leverera stort filhämtningsinnehåll. Det finns ingen begränsning för filhämtningsstorleken.

Mer information om optimering av stora filer finns i [Stor filoptimering](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Acceleration av dynamisk webbplats

 DSA (Dynamic site acceleration) är tillgänglig för **Azure CDN Standard från Akamai,** **Azure CDN Standard från Verizon**och Azure **CDN Premium från** Verizon-profiler. Denna optimering innebär en extra avgift att använda; Mer information finns i [priser för Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Dynamisk webbplatsacceleration från Microsoft erbjuds via [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) som är en global [anycast-tjänst](https://en.wikipedia.org/wiki/Anycast) som utnyttjar Microsofts privata globala nätverk för att leverera dina apparbetsbelastningar.

DSA innehåller olika tekniker som gynnar svarstiden och prestanda för dynamiskt innehåll. Tekniker inkluderar rutt- och nätverksoptimering, TCP-optimering med mera. 

Du kan använda den här optimeringen för att påskynda en webbapp som innehåller många svar som inte kan cachelagras. Exempel är sökresultat, utcheckningstransaktioner eller realtidsdata. Du kan fortsätta att använda grundläggande Azure CDN-cachelagringsfunktioner för statiska data. 

Mer information om dynamisk platsacceleration finns i [Dynamisk platsacceleration](cdn-dynamic-site-acceleration.md).



