---
title: Optimera frågetiden genom att använda lagringsstrategi för popup-tabell i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver hur du optimerar Frågetid med lagringsstrategi för popup-tabell på en Azure Database för PostgreSQL – enskild Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65066981"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optimera Frågetid med lagringsstrategi för popup-tabell 
Den här artikeln beskriver hur du optimerar frågetider med lagringsstrategi för lagring av stora attribut tekniken (popup) tabell.

## <a name="toast-table-storage-strategies"></a>Strategier för popup table storage
Fyra olika strategier för att spara kolumner på disken som kan använda popup-meddelande. De representerar olika kombinationer mellan komprimering och out of line storage. Strategin kan ställas in på nivå av datatypen och på kolumnnivå.
- **Vanlig** förhindrar komprimering eller out of line-lagring. Det inaktiverar användningen av en byte-huvuden för varlena typer. Oformaterad är endast möjlig strategi för kolumner med datatyper som inte kan använda popup-meddelande.
- **Utökade** kan både komprimering och out of line-lagring. Utökad är standardinställningen för de flesta datatyper som kan använda popup-meddelande. Komprimering prövas först. Out of line storage görs om raden fortfarande är för stor.
- **Externa** tillåter out of line lagring men inte komprimering. Användningen av externa gör delsträngen åtgärder på breda text och bytea kolumner som är snabbare. Snabbheten levereras med särskilda avgifter ökad lagringsutrymme. De här åtgärderna är optimerade för att hämta bara delarna av out of line-värdet som krävs när den inte komprimeras.
- **Main** tillåter komprimering men inte out of line-lagring. Out of line lagring utförs fortfarande för sådana kolumner, men endast som en sista utväg. Det inträffar när det inte finns något annat sätt att göra raden tillräckligt liten för att få plats på en sida.

## <a name="use-toast-table-storage-strategies"></a>Använda strategier för popup table storage
Om dina frågor komma åt datatyper som kan använda popup-meddelande måste du använda Main-strategi istället för standardalternativet för utökat för att minska frågetider. Main utesluta inte out of line-lagring. Om frågorna inte komma åt datatyper som kan använda popup-meddelande, kan det vara fördelaktigt att behålla med alternativet Utökad. En större del av raderna i tabellen huvudsakliga passar i delade buffert cacheminne, vilket hjälper till att prestanda.

Om du har en arbetsbelastning som använder ett schema med breda tabeller och räkna hög tecken kan du använda PostgreSQL popup-tabeller. Ett exempel kundtabell hade större än 350 kolumner med flera kolumner som erbjöds 255 tecken. När den har konverterats till tabellen popup Main strategi kan minskas benchmark fråga tiden från 4203 sekunder till 467 sekunder. Det är en förbättring av 89 procent.

## <a name="next-steps"></a>Nästa steg
Granska din arbetsbelastning för de föregående egenskaperna. 

Granska följande PostgreSQL-dokumentation: 
- [Kapitel 68, fysiska databaslagring](https://www.postgresql.org/docs/current/storage-toast.html) 