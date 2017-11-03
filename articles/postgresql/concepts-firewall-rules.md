---
title: "Azure-databas för PostgreSQL serverbrandväggsreglerna | Microsoft Docs"
description: "Beskriver brandväggsregler för din Azure-databas för PostgreSQL-servern."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: a8e1980900b430e56b0c01a4446dc525d25698da
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Azure-databas för PostgreSQL serverbrandväggsreglerna
Azure-databas för PostgreSQL serverbrandvägg förhindrar all åtkomst till databasservern förrän du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till servern, baserat på den ursprungliga IP-adressen för varje begäran.
Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brandväggsregler på servernivå.

**Regler i brandväggen:** reglerna att klienter ska kunna komma åt Azure hela databasen för PostgreSQL-Server som är alla databaser inom samma logiska server. Brandväggsregler på servernivå kan konfigureras med hjälp av Azure-portalen eller med Azure CLI-kommandona. Du måste vara prenumeration ägare eller deltagare i prenumeration för att skapa brandväggsregler på servernivå.

## <a name="firewall-overview"></a>Översikt över brandväggar
Alla databasåtkomst till din Azure-databas för PostgreSQL-servern har blockerats av brandväggen som standard. Du måste ange en eller flera servernivå brandväggsregler för att ge åtkomst till servern om du vill börja använda din server från en annan dator. Använd brandväggsreglerna för att ange vilka IP-adressintervall från Internet för att tillåta. Åtkomst till Azure-portalen webbplatsen själva påverkas inte av brandväggsreglerna.
Anslutningsförsök från Internet och Azure måste först passera genom brandväggen innan de kan komma åt PostgreSQL-databasen, som visas i följande diagram:

![Exempel flödet av hur brandväggen fungerar](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brandväggsregler på servernivå gäller för alla databaser på samma Azure-databas för PostgreSQL-servern. Om IP-adressen för begäran ligger inom ett intervall som anges i brandväggsreglerna på servernivå godkänns anslutningen.
Om IP-adressen för begäran inte är inom det intervall som anges i någon av servernivå brandväggsreglerna inte anslutningsbegäran.
Om ditt program ansluter med JDBC-drivrutinen för PostgreSQL, kan du stöta felet försöker ansluta när brandväggen blockerar anslutningen.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: allvarligt: inga pg\_hba.conf post för värd ”123.45.67.890”, ”adminuser” databas ”postgresql”, SSL

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Utöver Azure portal kan brandväggsregler hanteras via programmering med Azure CLI.
Se även [skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Felsökning av databasen server brandväggen
Tänk på följande när åtkomst till Microsoft Azure-databas för PostgreSQL Server-tjänsten inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har inte börjat gälla ännu:** kan det vara så mycket som en fem-minuters fördröjning för ändringar i Azure-databasen för PostgreSQL Server brandväggskonfigurationen ska börja gälla.

* **Inloggningen har inte behörighet eller ett felaktigt lösenord användes:** om en inloggning har inte behörighet för Azure-databas för PostgreSQL-server eller lösenordet som används är felaktig, anslutningen till Azure-databas för PostgreSQL server nekas. Skapa en brandväggsinställningen endast ger klienterna möjlighet att försöka ansluta till servern. varje klient måste fortfarande Ange säkerhetsreferenserna som behövs.

Till exempel kan använder en JDBC-klient kan följande fel visas.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: allvarligt: autentisering misslyckades för användaren ”användarnamn”

* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan du prova någon av följande lösningar:

* Be Internet Service Provider (ISP) för IP-adressintervall som tilldelats till klientdatorer som har åtkomst till Azure-databasen för PostgreSQL-servern och sedan lägga till IP-adressintervall som en brandväggsregel.

* Hämta statiska IP-adresser i stället för dina klientdatorer och lägger sedan till den statiska IP-adressen som en brandväggsregel.

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar brandväggsregler på servernivå finns:
* [Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure portal](howto-manage-firewall-using-portal.md).
* [Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md).
