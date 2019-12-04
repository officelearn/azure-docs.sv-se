---
title: Program utveckling – Azure Database for MySQL
description: Introducerar design överväganden som en utvecklare bör följa när program koden skrivs för att ansluta till Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 74abf680223d562522a11ecb8999fedb37de9907
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770279"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Översikt över program utveckling för Azure Database for MySQL 
Den här artikeln beskriver design överväganden som en utvecklare bör följa när de skriver program kod för att ansluta till Azure Database for MySQL. 

> [!TIP]
> En själv studie kurs som visar hur du skapar en server, skapar en serverbaserad brand vägg, visar Server egenskaper, skapar databas och ansluter och frågar med hjälp av Workbench och MySQL. exe finns i [utforma din första Azure Database for MySQL databas](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Språk och plattform
Det finns kodexempel för olika programmeringsspråk och plattformar. Du kan hitta länkar till kod exempel på: [anslutnings bibliotek som används för att ansluta till Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Verktyg
Azure Database for MySQL använder MySQL community-versionen som är kompatibel med MySQLs vanliga hanterings verktyg som Workbench eller MySQL-verktyg som MySQL. exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)och andra. Du kan också använda Azure Portal, Azure CLI och REST-API: er för att interagera med databas tjänsten.

## <a name="resource-limitations"></a>Resursbegränsningar
Azure Database for MySQL hanterar resurserna som är tillgängliga för en server med hjälp av två olika metoder: 
- Resurser styrning.
- Tillämpning av gränser.

## <a name="security"></a>Säkerhet
Azure Database for MySQL ger resurser för att begränsa åtkomst, skydda data, konfigurera användare och roller och övervaka aktiviteter på en MySQL-databas.

## <a name="authentication"></a>Autentisering
Azure Database for MySQL stöder serverautentisering för användare och inloggningar.

## <a name="resiliency"></a>Elasticitet
När ett tillfälligt fel uppstår vid anslutning till en MySQL-databas, bör din kod försöka anropa igen. Vi rekommenderar att logiken för omprövning använder en annan logik så att den inte överbelastar SQL-databasen med flera klienter och försöker igen samtidigt.

- Kod exempel: för kod exempel som illustrerar logik för omprövning, se exempel för det språk som du väljer på: [anslutnings bibliotek som används för att ansluta till Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Hantera anslutningar
Databas anslutningar är en begränsad resurs, så vi rekommenderar lämpliga användning av anslutningar vid åtkomst till MySQL-databasen för att uppnå bättre prestanda.
- Få åtkomst till databasen med hjälp av anslutningspoolen eller permanenta anslutningar.
- Få åtkomst till databasen med hjälp av kort tids längd för anslutning. 
- Använd logiken för omprövning i programmet vid tidpunkten för anslutnings försöket för att fånga fel som uppstår till följd av samtidiga anslutningar har nått det högsta tillåtna antalet. I logiken för omförsök anger du en kort fördröjning och väntar sedan en slumpmässig tid innan ytterligare anslutnings försök görs.