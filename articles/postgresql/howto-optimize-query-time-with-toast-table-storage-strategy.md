---
title: Optimera frågetiden med hjälp av guiden TOAST-tabelllagringsstrategi i Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du optimerar frågetiden med TOAST-tabelllagringsstrategin på en Azure-databas för PostgreSQL - Single Server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65066981"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optimera frågetid med strategin för lagring av TOAST-tabell 
I den här artikeln beskrivs hur du optimerar frågetider med den överdimensionerade skålen-strategin (Table Storage Strategy).

## <a name="toast-table-storage-strategies"></a>STRATEGIER FÖR lagring av skål för bord
Fyra olika strategier används för att lagra kolumner på disk som kan använda TOAST. De representerar olika kombinationer mellan komprimering och out-of-line lagring. Strategin kan ställas in på datatypsnivå och på kolumnnivå.
- **Plain** förhindrar antingen komprimering eller out-of-line lagring. Det inaktiverar användningen av enbyteshuvuden för varlenatyper. Plain är den enda möjliga strategin för kolumner med datatyper som inte kan använda TOAST.
- **Utökad** möjliggör både komprimering och out-of-line lagring. Extended är standard för de flesta datatyper som kan använda TOAST. Komprimering görs först. Out-of-line lagring görs om raden fortfarande är för stor.
- **Extern** tillåter out-of-line lagring men inte komprimering. Användning av Externa gör delsträngsåtgärder på breda text- och bytekolumner snabbare. Denna hastighet kommer med straffet för ökat lagringsutrymme. Dessa åtgärder är optimerade för att hämta endast de nödvändiga delarna av out-of-line-värdet när det inte är komprimerat.
- **Main** tillåter komprimering men inte out-of-line lagring. Out-of-line lagring utförs fortfarande för sådana kolumner, men bara som en sista utväg. Det inträffar när det inte finns något annat sätt att göra raden tillräckligt liten för att få plats på en sida.

## <a name="use-toast-table-storage-strategies"></a>Använda STRATEGIER FÖR lagring av TOAST-bord
Om dina frågor får åtkomst till datatyper som kan använda TOAST bör du överväga att använda huvudstrategin i stället för standardalternativet Utökad för att minska frågetiderna. Main utesluter inte out-of-line lagring. Om dina frågor inte kommer åt datatyper som kan använda TOAST kan det vara fördelaktigt att behålla alternativet Utökad. En större del av raderna i huvudtabellen får plats i den delade buffertcachen, vilket hjälper prestanda.

Om du har en arbetsbelastning som använder ett schema med breda tabeller och höga teckenantal kan du använda PostgreSQL TOAST-tabeller. En exempelkundtabell hade större än 350 kolumner med flera kolumner som sträckte sig över 255 tecken. När den har konverterats till TOAST-tabellen Main-strategin minskade deras referensfrågetid från 4203 sekunder till 467 sekunder. Det är en förbättring på 89 procent.

## <a name="next-steps"></a>Nästa steg
Granska din arbetsbelastning för de tidigare egenskaperna. 

Läs följande PostgreSQL-dokumentation: 
- [Kapitel 68, Fysisk lagring av databasen](https://www.postgresql.org/docs/current/storage-toast.html) 