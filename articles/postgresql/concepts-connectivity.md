---
title: Hantera tillfälliga anslutningsfel - Azure Database for PostgreSQL - Single Server
description: Lär dig hur du hanterar tillfälliga anslutningsfel för Azure Database för PostgreSQL - Single Server.
keywords: postgresql-anslutning,anslutningssträng,anslutningsproblem,tillfälligt fel,anslutningsfel
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8138512dfc893f3523c5ad78a93aef1bcdedfe70
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768037"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Hantering av tillfälliga anslutningsfel för Azure Database for PostgreSQL - enskild server

I den här artikeln beskrivs hur du hanterar tillfälliga fel som ansluter till Azure Database för PostgreSQL.

## <a name="transient-errors"></a>Tillfälliga fel

Ett tillfälligt fel, även kallat transientfel, är ett fel som löser sig själv. Oftast visas dessa fel som en anslutning till databasservern som tas bort. Det går inte heller att öppna nya anslutningar till en server. Tillfälliga fel kan uppstå till exempel när maskinvaru- eller nätverksfel inträffar. En annan orsak kan vara en ny version av en PaaS-tjänst som håller på att rullas ut. De flesta av dessa händelser mildras automatiskt av systemet på mindre än 60 sekunder. En bästa praxis för att utforma och utveckla program i molnet är att förvänta sig tillfälliga fel. Anta att de kan hända i någon komponent när som helst och att ha lämplig logik på plats för att hantera dessa situationer.

## <a name="handling-transient-errors"></a>Hantera tillfälliga fel

Tillfälliga fel bör hanteras med hjälp av logik för återförsök. Situationer som måste beaktas:

* Ett fel uppstår när du försöker öppna en anslutning
* En inaktiv anslutning tas bort på serversidan. När du försöker utfärda ett kommando kan det inte köras
* En aktiv anslutning som för närvarande kör ett kommando tas bort.

Det första och andra fallet är ganska rakt fram för att hantera. Försök öppna anslutningen igen. När du lyckas har det tillfälliga felet mildrats av systemet. Du kan använda din Azure-databas för PostgreSQL igen. Vi rekommenderar att du väntar innan du försöker ansluta igen. Backa om de första försöken misslyckas. På så sätt kan systemet använda alla tillgängliga resurser för att lösa felsituationen. Ett bra mönster att följa är:

* Vänta i 5 sekunder innan du försöker igen.
* För varje efterföljande återförsök ökar väntetiden exponentiellt, upp till 60 sekunder.
* Ange ett maximalt antal försök vid vilken tidpunkt ditt program anser att åtgärden misslyckades.

När en anslutning till en aktiv transaktion misslyckas är det svårare att hantera återställningen korrekt. Det finns två fall: Om transaktionen var skrivskyddad till sin natur är det säkert att öppna anslutningen igen och att försöka igen transaktionen. Om transaktionen var också att skriva till databasen måste du bestämma om transaktionen återställdes eller om den lyckades innan det tillfälliga felet inträffade. I så fall kanske du bara inte har fått bekräftelse från databasservern.

Ett sätt att göra detta är att generera ett unikt ID på klienten som används för alla försök. Du skickar det här unika ID:t som en del av transaktionen till servern och lagrar det i en kolumn med en unik begränsning. På så sätt kan du säkert försöka transaktionen igen. Det kommer att lyckas om den tidigare transaktionen återställdes och klienten genererade unikt ID ännu inte finns i systemet. Det misslyckas med att ange en dubblettnyckelfel om det unika ID:t tidigare lagrats eftersom den tidigare transaktionen slutfördes.

När ditt program kommunicerar med Azure Database för PostgreSQL via mellanprogram från tredje part frågar du leverantören om mellanmaterialet innehåller logik för återförsök för tillfälliga fel.

Se till att testa att du försöker logiken igen. Försök till exempel att köra koden medan du skalar upp eller ned beräkningsresurserna för dig Azure Database för PostgreSQL-server. Ditt program bör hantera den korta driftstopp som påträffas under den här åtgärden utan problem.

## <a name="next-steps"></a>Nästa steg

* [Felsöka anslutningsproblem till Azure Database for PostgreSQL](howto-troubleshoot-common-connection-issues.md)
