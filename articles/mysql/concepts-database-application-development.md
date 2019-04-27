---
title: Databasen översikt över programutveckling för Azure Database for MySQL
description: Introducerar designöverväganden som utvecklare bör följa när du skriver programkod för att ansluta till Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 946f7011c51b7c6844e023d03e01e4c2043d2578
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615644"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Översikt över programutveckling för Azure Database for MySQL 
Den här artikeln beskriver designöverväganden som utvecklare bör följa när du skriver programkod för att ansluta till Azure Database for MySQL. 

> [!TIP]
> Se en självstudie som visar hur du skapar en server, skapar en serverbaserad brandvägg, visar serveregenskaper, skapa databas och ansluta och fråga med hjälp av workbench och mysql.exe, [utforma din första Azure Database for MySQL-databas](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Språk och plattform
Det finns kodexempel för olika programmeringsspråk och plattformar. Du hittar länkar till kodexemplen på: [Anslutningsbibliotek som används för att ansluta till Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Verktyg
Azure Database för MySQL som använder MySQL community version, kompatibel med MySQL vanliga hanteringsverktyg, till exempel Workbench eller MySQL-verktyg, till exempel mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql), med mera. Du kan också använda Azure-portalen, Azure CLI och REST API: er för att interagera med tjänsten.

## <a name="resource-limitations"></a>Resursbegränsningar
Azure Database för MySQL hanterar resurserna som är tillgängliga för en server med hjälp av två olika sätt: 
- Resursstyrning.
- Verkställandet av gränser.

## <a name="security"></a>Säkerhet
Azure Database för MySQL innehåller resurser för att begränsa åtkomst, skydda data, konfigurera användare och roller och övervakningsaktiviteter på en MySQL-databas.

## <a name="authentication"></a>Autentisering
Azure Database för MySQL stöder server-autentisering av användare och inloggningar.

## <a name="resiliency"></a>Återhämtning
När ett tillfälligt fel uppstår vid anslutning till en MySQL-databas, bör din kod göra om anropet. Vi rekommenderar att logik för omprövning använder backoff logic så att den inte överbelastar SQL-databas med flera klienter som försöker samtidigt.

- Kodexempel: För kodexempel som illustrerar logik för omprövning, se exempel för valfritt på språk: [Anslutningsbibliotek som används för att ansluta till Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Hantera anslutningar
Databasanslutningar är en begränsad resurs, så vi rekommenderar användningstyperna användning av anslutningar vid åtkomst till din MySQL-databas för att uppnå bättre prestanda.
- Åtkomst till databasen med hjälp av anslutningspooler eller beständiga anslutningar.
- Åtkomst till databasen med hjälp av kort anslutning livslängd. 
- Använd omprövning i ditt program vid anslutningsförsöket att fånga upp fel som härrör från samtidiga anslutningar har nått det högsta tillåtna. Ange en kort fördröjning i logik för omprövning och vänta sedan en slumpmässig tid innan de ytterligare anslutningsförsök.