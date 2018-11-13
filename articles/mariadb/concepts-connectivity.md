---
title: Hantering av tillfälliga anslutningsfel för Azure Database for MariaDB | Microsoft Docs
description: Lär dig hur du hanterar tillfälliga anslutningsfel för Azure Database for MariaDB.
keywords: MySQL-anslutningen, anslutningssträngen, problem med nätverksanslutningen, tillfälligt fel, anslutningsfel
services: mariadb
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 203401e3842912169371f315048f6930c8dc80eb
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568105"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Hantering av tillfälliga anslutningsfel för Azure Database for MariaDB

Den här artikeln beskriver hur du hanterar tillfälliga fel som ansluter till Azure Database for MariaDB.

## <a name="transient-errors"></a>Tillfälliga fel

Ett tillfälligt fel, även kallat ett tillfälligt fel uppstår ett fel som kommer att åtgärdas automatiskt. De här felen visas normalt som en anslutning till databasservern håller på att släppas. Också kan nya anslutningar till en server inte öppnas. Tillfälliga fel kan inträffa till exempel när maskinvaru- eller programvarufel. En annan orsak kan vara en ny version av en PaaS-tjänst som distribueras. De flesta av dessa händelser begränsas automatiskt av systemet på mindre än 60 sekunder. Bästa praxis för att utforma och utveckla program i molnet är att förvänta sig tillfälliga fel. Anta att de kan inträffa i någon komponent när som helst och ha logiken som är lämpliga att hantera dessa strategier.

## <a name="handling-transient-errors"></a>Hantering av tillfälliga fel

Tillfälliga fel bör hanteras med hjälp av logik för omprövning. Situationer som måste beaktas:

* Ett fel inträffar vid försök att öppna en anslutning
* En inaktiv anslutning har släppts på serversidan. Det går inte att köras vid försök att utfärda ett kommando
* En aktiv anslutning som för närvarande kör ett kommando har släppts.

Det första och det andra fallet är ganska enkelt att hantera. Försök att öppna anslutningen igen. När du lyckas har tillfälliga fel blivit minimera av systemet. Du kan använda din Azure Database for MariaDB igen. Vi rekommenderar att du har väntar innan du försöker anslutningen. Säkerhetskopiering om de första försök misslyckas. Det här sättet systemet kan använda alla resurser som är tillgängliga för att lösa felsituationen. Ett bra mönster följa är:

* Vänta 5 sekunder innan din första återförsöket.
* För varje följande återförsök ökningen väntan exponentiellt, upp till 60 sekunder.
* Ange ett högsta antal återförsök då programmet tar hänsyn till åtgärden misslyckades.

När en anslutning med en aktiv transaktion misslyckas, är det svårare att utföra återställning på rätt sätt. Det finns två fall: Om transaktionen var skrivskyddad sin natur, är det säkert att öppna anslutningen igen och försök transaktionen igen. Om men om transaktionen också skrivs till databasen, du måste bestämma om transaktionen återställdes, eller om den har slutförts innan du tillfälligt fel har inträffat. I så fall kan kanske du inte har fått commit-bekräftelse från databasservern.

Ett sätt att göra detta, är att generera ett unikt ID för klienten som används för alla nya försök. Du kan skicka den här unika ID som en del av transaktionen till servern och spara den i en kolumn med en unik begränsning. Det här sättet du kan på ett säkert sätt göra om transaktionen. Den lyckas om tidigare transaktionen återställdes och klienten som genereras unikt ID finns inte ännu i systemet. Det kommer att misslyckas som anger en duplicerad felet om det unika ID: T fanns tidigare eftersom föregående transaktionen har slutförts.

När ditt program kommunicerar med Azure Database for MariaDB via tredje parts mellanprogram, be leverantören om mellanprogrammet innehåller logik för omprövning av tillfälliga fel.

Se till att testa logik för omprövning. Prova till exempel att köra din kod vid skala upp eller ned beräkningsresurser på din Azure Database for MariaDB-server. Ditt program bör hantera kort driftstopp som påträffas under den här åtgärden utan problem.

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem med anslutningen till Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)
