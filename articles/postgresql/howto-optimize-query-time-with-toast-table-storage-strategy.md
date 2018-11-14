---
title: Optimera fråga körs i Azure Database för PostgreSQL-server med lagringsstrategi för popup-tabell
description: Den här artikeln beskriver hur du optimerar Frågetid med lagringsstrategi för popup-tabellen i en Azure Database for PostgreSQL-server.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: dee8aaaef4b1998a7234a88d07ad5efbc79d050b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629301"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>Optimera Frågetid med lagringsstrategi för popup-tabell 
Den här artikeln beskriver hur du optimerar frågetider med lagringsstrategi för popup-tabellen.

## <a name="toast-table-storage-strategies"></a>Strategier för popup table storage
Det finns fyra olika strategier för att lagra popup-kan kolumner på disk som representerar olika kombinationer mellan komprimering och out of line storage. Strategin kan ställas in på nivå av datatypen och på kolumnnivå.
- **Vanlig** förhindrar att komprimera eller lagring för out of line; dessutom den inaktiverar användningen av en byte-huvuden för varlena typer. **Vanlig** är den enda möjliga strategin för kolumner i kan för popup-datatyper.
- **Utökade** kan både komprimering och out of line-lagring. **Utökade** är standard för de flesta popup-kan datatyper. Komprimering kommer att försök först sedan out of line storage om raden fortfarande är för stor.
- **Externa** tillåter out of line lagring men inte komprimering. Användning av **externa** skapa delsträngen åtgärder på wide text och bytea kolumner snabbare, på särskilda avgifter för ökad lagringsutrymme, eftersom de här åtgärderna är optimerade för att hämta det obligatoriska delar av out of line egenskapen när den komprimeras inte.
- **Main** tillåter komprimering men inte out of line-lagring. Out of line storage kommer att utföras för sådana kolumner, men endast som en sista utväg när det inte finns något annat sätt att göra raden tillräckligt liten för att få plats på en sida.

## <a name="using-toast-table-storage-strategies"></a>Använda strategier för popup table storage
Om dina frågor komma åt popup-kan datatyper, bör du använda **Main** istället för standardvärdet **Extended** alternativet för att minska frågetider. **Main** utesluter inte out of line-lagring. Å andra sidan, om frågorna inte kommer åt popup-kan datatyper, kan det vara fördelaktigt att behålla den **Extended** alternativet. En större del av raderna i tabellen huvudsakliga får så plats i cacheminnet delade buffert hjälper prestanda.

Om du har en arbetsbelastning med ett schema med breda tabeller och räkna hög tecken kan du använda PostgreSQL popup-tabeller. Ett exempel kundtabell hade större än 350 kolumner med flera kolumner utsträckning 255 tecken. Benchmark-fråga tiden minskas från 4203 sekunder till 467 sekunder, en förbättring av 89 procent, när du har konverterat popup-strategi för **Main**.

## <a name="next-steps"></a>Nästa steg
Granska din arbetsbelastning för ovanstående egenskaper. 

Granska följande PostgreSQL-dokumentation: [kapitel 68, fysiska databaslagring](https://www.postgresql.org/docs/current/storage-toast.html) 