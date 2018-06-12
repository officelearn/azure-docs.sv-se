---
title: Databasen application utvecklingsöversikt för Azure-databas för MySQL
description: Introducerar designöverväganden som utvecklare bör följa när du skriver programkod för att ansluta till Azure-databas för MySQL
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b733468d41afacb616c95f0628e7bad6b0c837f0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264171"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Översikt över utveckling program för Azure-databas för MySQL 
Den här artikeln beskrivs överväganden vid utformning av som utvecklare bör följa när du skriver programkod för att ansluta till Azure-databas för MySQL. 

> [!TIP]
> En självstudiekurs visar hur du skapar en server, skapa en serverbaserad brandvägg, Visa serveregenskaper, skapa databas, och ansluta och fråga med hjälp av arbetsstationen och mysql.exe finns [utforma din första Azure-databas för MySQL-databas](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Språk och plattform
Det finns kodexempel för olika programmeringsspråk och plattformar. Du hittar länkar till kodexempel på: [anslutning bibliotek som används för att ansluta till Azure-databas för MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Verktyg
Azure MySQL-databas som använder MySQL community version, kompatibel med MySQL vanliga hanteringsverktyg, till exempel arbetsstationen eller MySQL verktyg, till exempel mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql), med mera. Du kan också använda Azure-portalen, Azure CLI och REST API: er för att interagera med databastjänsten.

## <a name="resource-limitations"></a>Resursbegränsningar
Azure-databas för MySQL hanterar resurser som är tillgängliga för en server med hjälp av två olika metoder: 
- Styrning av resurser.
- Tillämpning av gränser.

## <a name="security"></a>Säkerhet
Azure-databas för MySQL innehåller resurser för att begränsa åtkomst, skyddar data, konfigurera användare och roller och övervakningsaktiviteter på en MySQL-databas.

## <a name="authentication"></a>Autentisering
Azure MySQL-databas har stöd för server-autentisering av användare och inloggningar.

## <a name="resiliency"></a>Återhämtning
När ett tillfälligt fel uppstår vid anslutning till en MySQL-databas ska koden gör anropet. Vi rekommenderar att logik för omprövning använder undantagsläge logik så att den inte överväldigande SQL-databas med flera klienter som du försöker samtidigt.

- Kodexempel: kodexempel som visar försök logik, se exempel för språket i ditt val på: [anslutning bibliotek som används för att ansluta till Azure-databas för MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Hantera anslutningar
Databasanslutningar är en begränsad resurs, så vi rekommenderar sensible användning av anslutningar vid åtkomst till din MySQL-databas för att få bättre prestanda.
- Åtkomst till databasen med hjälp av anslutningspooler eller beständiga anslutningar.
- Åtkomst till databasen med hjälp av anslutning kort livslängd. 
- Använd logik i ditt program vid anslutningsförsöket att upptäcka fel som uppstår till följd av samtidiga anslutningar har nått det högsta tillåtna. Ange en kort fördröjning i logik för omprövning och sedan vänta tills en slumpmässig tid innan ytterligare anslutningsförsök.