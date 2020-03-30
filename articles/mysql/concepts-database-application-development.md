---
title: Programutveckling - Azure Database för MySQL
description: Introducerar designöverväganden som en utvecklare bör följa när du skriver programkod för att ansluta till Azure Database för MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 02ce6b00b6555f849d162b9f3b381c0ab358d712
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532849"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Översikt över programutveckling för Azure Database för MySQL 
I den här artikeln beskrivs designöverväganden som en utvecklare bör följa när du skriver programkod för att ansluta till Azure Database for MySQL. 

> [!TIP]
> En självstudiekurs som visar hur du skapar en server finns i Designa en serverbaserad brandvägg, visa serveregenskaper, skapa databas och ansluta och fråga med hjälp av workbench och mysql.exe finns i [Designa din första Azure-databas för MySQL-databas](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Språk och plattform
Det finns kodexempel för olika programmeringsspråk och plattformar. Du hittar länkar till kodexemplen på: [Anslutningsbibliotek som används för att ansluta till Azure Database för MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Verktyg
Azure Database for MySQL använder MySQL-communityversionen, kompatibel med MySQL common management tools såsom Workbench eller MySQL-verktyg som mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)och andra. Du kan också använda Azure-portalen, Azure CLI och REST API:er för att interagera med databastjänsten.

## <a name="resource-limitations"></a>Resursbegränsningar
Azure Database for MySQL hanterar de resurser som är tillgängliga för en server med hjälp av två olika mekanismer: 
- Styrning av resurser.
- Tillämpning av gränser.

## <a name="security"></a>Säkerhet
Azure Database for MySQL innehåller resurser för att begränsa åtkomst, skydda data, konfigurera användare och roller och övervaka aktiviteter i en MySQL-databas.

## <a name="authentication"></a>Autentisering
Azure Database for MySQL stöder serverautentisering av användare och inloggningar.

## <a name="resiliency"></a>Återhämtning
När ett tillfälligt fel uppstår när du ansluter till en MySQL-databas ska koden försöka samtalet igen. Vi rekommenderar att logiken för återförsök använder tillbaka logiken så att den inte överbelastar SQL-databasen med flera klienter som försöker igen samtidigt.

- Kodexempel: För kodexempel som illustrerar logiken för återförsök finns i exempel på det språk du väljer på: [Anslutningsbibliotek som används för att ansluta till Azure Database för MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Hantera anslutningar
Databasanslutningar är en begränsad resurs, så vi rekommenderar en förnuftig användning av anslutningar när du öppnar mysql-databasen för att uppnå bättre prestanda.
- Öppna databasen med hjälp av anslutningspoolning eller beständiga anslutningar.
- Öppna databasen med kort anslutningslivslängd. 
- Använd logik för återförsök i programmet vid tidpunkten för anslutningsförsöket för att fånga fel till följd av samtidiga anslutningar har nått det högsta tillåtna värdet. I logiken för återförsök ställer du in en kort fördröjning och väntar sedan en slumpmässig tid innan ytterligare anslutningsförsök.