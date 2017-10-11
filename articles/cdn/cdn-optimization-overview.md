---
title: "Optimera Azure content delivery för ditt scenario"
description: "Hur du optimerar leverans av ditt innehåll för specifika scenarier"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 98941c49b057380b3ef9164515bcc2a63ccb56ce
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>Optimera Azure content delivery för ditt scenario

När du levererar innehåll till en stor global målgrupp är det viktigt att kontrollera optimerade leveransen av ditt innehåll. Azure innehåll Delivery Network kan optimera leverans upplevelsen baserat på typ av innehåll som du har. Innehållet kan vara en webbplats, en direktsänd dataström, en video eller en stor fil för hämtning. När du skapar en slutpunkt för content delivery network (CDN) måste du ange ett scenario i den **optimerade för** alternativet. Ditt val avgör vilka optimering tillämpas på innehåll från CDN-slutpunkten.

Optimering alternativ är utformade för att använda metodtips beteenden för att förbättra prestanda för leverans av innehåll och bättre ursprung-avlastning. Dina val för scenariot påverkar prestanda genom att ändra konfigurationer för partiellt cachelagring, objektet högoptimerat och försök felprincipen ursprung. 

Den här artikeln innehåller en översikt över olika optimering funktioner och när du ska använda dem. Mer information om funktioner och begränsningar finns i respektive artiklar på varje typ av enskilda optimering.

> [!NOTE]
> Din **optimerade för** alternativen kan variera beroende på den provider du väljer. CDN-providers gäller förbättring på olika sätt beroende på scenario. 

## <a name="provider-options"></a>Providern alternativ

Azure Content Delivery Network från Akamai stöder:

* Allmän web leverans 

* Allmän direktuppspelning

* Video-on-demand-direktuppspelning

* Hämtning av stora filer

* Dynamiska acceleration 

Azure Content Delivery Network från Verizon stöder endast Internet-leverans. Det kan användas för video på begäran och hämtning av stora filer. Du behöver inte ange optimering.

Vi rekommenderar starkt att du testar variationer mellan olika providrar för att välja den optimala providern för leveransen.

## <a name="select-and-configure-optimization-types"></a>Välj och konfigurera optimering typer

Välj en typ av optimering som bäst passar scenariot och typ av innehåll som du vill att leverera slutpunkten för att skapa en ny slutpunkt. **Allmän webben** är standardvalet. Du kan uppdatera alternativet optimering för alla befintliga Akamai slutpunkten när som helst. Den här ändringen avbryta inte leverans från CDN. 

1. Välj en slutpunkt i en profil för Standard Akamai.

    ![Val av slutpunkten ](./media/cdn-optimization-overview/01_Akamai.png)

2. Under **inställningar**väljer **optimering**. Välj en typ från den **optimerade för** listrutan.

    ![Optimering och typ](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimering för specifika scenarier

Du kan optimera CDN-slutpunkten för en av följande scenarier. 

### <a name="general-web-delivery"></a>Allmän web leverans

Allmän webben är alternativet vanligaste optimering. Den är avsedd för allmän web content optimering, till exempel webbplatser och webbprogram. Denna optimering kan också användas för filen och hämtar video.

En typisk webbplats innehåller statiska och dynamiska innehåll. Statiskt innehåll innehåller bilder, JavaScript-bibliotek och formatmallar som kan cachelagras och levereras till olika användare. Dynamiskt innehåll anpassas för en enskild användare, till exempel artiklar som skräddarsys efter en användarprofil. Dynamiskt innehåll är inte cachelagras på grund av det är unikt för varje användare, till exempel i kundvagn innehållet. Allmän web leverans kan optimera hela webbplatsen. 

> [!NOTE]
> Om du använder Azure Content Delivery Network från Akamai kanske du vill använda denna optimering om genomsnittliga filstorleken är mindre än 10 MB. Om din Genomsnittlig filstorlek är större än 10 MB väljer **stora Filhämtning** från den **optimerade för** listrutan.

### <a name="general-media-streaming"></a>Allmän direktuppspelning

Om du behöver använda slutpunkten för direktsänd strömning och video-on-demand strömning rekommenderar vi allmänna direktuppspelning optimering.

Direktuppspelning är skiftlägeskänslig, tid eftersom paket som anländer sent på klienten kan orsaka en försämrad visa upplevelse, till exempel ofta buffring av videoinnehåll. Direktuppspelning optimering minskar svarstiden för leverans av innehåll för medier och ger en smooth streaming-upplevelse för användare. 

Det här scenariot är gemensamt för Azure media-kunder. När du använder Azure media services kan få du en strömmande slutpunkt som kan användas för strömning live och på begäran. Med det här scenariot behöver kunder inte växla till en annan slutpunkt när de ändras från aktiv till strömning på begäran. Allmän media strömmande optimering har stöd för den här typen av scenario.

Azure innehåll Delivery Network från Verizon använder Internet optimering Leveranstyp för att leverera strömmande media-innehåll.

Läs mer om direktuppspelning optimering i [direktuppspelning optimering](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Video-on-demand-direktuppspelning

Video-on-demand media strömmande optimering förbättrar video-on-demand liveströmmat innehåll. Om du använder en slutpunkt för video-on-demand strömning kanske du vill använda det här alternativet.

Azure innehåll Delivery Network från Verizon använder Internet optimering Leveranstyp för att leverera strömmande media-innehåll.

Läs mer om direktuppspelning optimering i [direktuppspelning optimering](cdn-media-streaming-optimization.md).

> [!NOTE]
> Om slutpunkten används främst för video-on-demand-innehåll, kan du använda den här typen av optimering. Den största skillnaden mellan denna optimering och allmänna optimering för direktuppspelning är gör timeout. Tidsgränsen är mycket kortare du arbetar med direktsänd strömning scenarier.

### <a name="large-file-download"></a>Hämtning av stora filer

Om du använder Azure Content Delivery Network från Akamai, måste du använda stora Filhämtning för att leverera filer som är större än 1,8 GB. Azure Content Delivery Network från Verizon har inte en begränsning på filen filstorlek i dess allmänna web leveransoptimering.

Om du använder Azure innehåll Delivery Network från Akamai är hämtning av stora filer optimerade för innehåll som är större än 10 MB. Om din genomsnittlig storlek är mindre än 10 MB kanske du vill använda allmänna web leverans. Om din Genomsnittlig filstorlek är konsekvent större än 10 MB, kan det vara mer effektivt att skapa en separat slutpunkt för stora filer. Inbyggd programvara eller programuppdateringar normalt exempelvis stora filer.

Azure innehåll Delivery Network från Verizon använder Internet optimering Leveranstyp för att leverera strömmande media-innehåll.

Läs mer om optimering för stora filer i [optimering för stora filer](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Dynamiska acceleration

 Dynamiska acceleration är tillgänglig från både Akamai och Verizon innehållsleveransnätverk profiler. Denna optimering innebär en avgift som ska användas. Se prissättningssidan för mer information.

Dynamiska acceleration innehåller olika tekniker som omfattas svarstid och prestanda för dynamiskt innehåll. Tekniken omfattar flödes- och optimering, optimering för TCP med mera. 

Du kan använda denna optimering för att påskynda en webbapp som innehåller flera svar som inte är Cacheable ställs. Exempel är sökresultat, utcheckningen transaktioner eller realtidsdata. Du kan fortsätta att använda cache grundfunktionerna för CDN för statiska data. 



