---
title: Översikt över Mobile Engagement Export-API
description: Lär dig grunderna om hur du exporterar dina rådata som genererats av dina användares enheter som ska använda i dina egna verktyg
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: ''
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: c1dc8a34abf84be3d7f59d41c36d90c2a67e5d32
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="mobile-engagement-export-api-overview"></a>Översikt över Mobile Engagement Export-API
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

## <a name="introduction"></a>Introduktion
I det här dokumentet får du lära dig grunderna om hur du exporterar dina rådata som genererats av dina användares enheter som ska använda i dina egna verktyg.

## <a name="pre-requisites"></a>Förutsättningar
Exportera rådata från Mobile Engagement kräver:

* API-autentisering installationen för att kunna använda API: er (se [autentisering manuell installation](mobile-engagement-api-authentication-manual.md)),
* Använd antingen den REST API: er eller [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
* Ett Azure Storage-konto.

> [!NOTE]
> Vi rekommenderar även den utmärkt [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/), minst under utvecklingsfasen eftersom den innehåller en lättanvänd gränssnitt för att interagera med Azure Storage.
> 
> 

## <a name="what-can-be-exported"></a>Vad kan exporteras?
Mobile Engagement användarna om att samla in många typer av data och därför har flera typer av export lämpade för dessa olika datatyper.
Det finns 2 grundläggande typer av export:

* Ögonblicksbild: används vanligtvis för att exportera data som representerar ett tillstånd och där Mobile Engagement inte är en historik. Detta omfattar taggar (app-info)-tokens eller push kampanj feedback till exempel. Detta innebär att dessa typer av export inte är relaterade till ett datum.
* historiska: den här exporten typen används för data som samlas över tid, till exempel händelser eller aktiviteter till exempel.

I tabellen nedan beskrivs omfattande alla möjliga exporter:

| Exportera typ | Datatyp | Beskrivning |
| --- | --- | --- |
| Ögonblicksbild |Push |Genererar en export av Push-kampanjer feedback på grundval av per deviceid/användar-ID |
| Ögonblicksbild |Tagga |Genererar en export av taggar (app-info) som är associerade med varje enheter |
| Ögonblicksbild |Enhet |Genererar en export av de flesta uppgifter om enheter som technicals (modell, språk, tidszon,...), taggar, visas första gången... |
| Ögonblicksbild |Token |Genererar en export av de giltiga token |
| Historiska |Aktivitet |Genererar en export av alla aktiviteter för alla enheter på en viss tidsperiod |
| Historiska |Händelse |Genererar en export av alla aktiviteter för alla enheter på en viss tidsperiod |
| Historiska |Jobb |Genererar en export av alla jobb för alla enheter på en viss tidsperiod |
| Historiska |Fel |Genererar en export av alla fel för varje enheter på en viss tidsperiod |

## <a name="how-does-it-work"></a>Hur fungerar det?
Export är långa pågående aktiviteter som kan ge stora datafiler. Därför kan de inte anropas för att returnera omedelbart en fil att ladda ned.
För att exportera data från Mobile Engagement, måste du skapa en **exportera jobbet** via API anger du vanligtvis:

* Typ av export (ögonblicksbild eller tidigare)
* Datatypen
* Den **Azure Storage-behållare** (inklusive en giltig SAS med skrivbehörighet) där resultatet exporten ska skrivas.
* t.ex. exempel behållaren URL-parametern skulle bli https://[StorageAccountName].blob.core.windows.net/[ContainerName]? [SASWritePermissionsToken]  

Här är ett exempel på verkliga världen. https://testazmeexport.blob.core.windows.net/test1234azme?sv=2015-12-11&ss=b&srt=sco&sp=rwdlac&se=2016-12-17T04:59:26Z&st=2016-12-16T20:59:26Z&spr=https&sig=KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q%3D

Observera att det kan ta några minuter för jobbet startas och sedan den kan köras några sekunder för små appar till flera timmar för appar med många användare eller aktiviteten.

Det är möjligt att kontrollera statusen för att se om det fortfarande körs eller om den har slutförts när jobbet har skapats.

När jobbet lyckades, är den resulterande datafilen tillgängliga på den angivna lagringsbehållaren.

