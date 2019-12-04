---
title: Brand Väggs regler – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du använder brand Väggs regler för att ansluta till Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 28c8bccaf6be49b7220a32c781b79f106ad86e52
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768647"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Brand Väggs regler i Azure Database for PostgreSQL-enskild server
Azure Database for PostgreSQL Server Firewall förhindrar all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Brand väggen beviljar åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.
Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brand Väggs regler på server nivå.

**Brand Väggs regler:** Dessa regler gör det möjligt för klienter att komma åt hela Azure Database for PostgreSQL Server, det vill säga alla databaser på samma logiska Server. Brand Väggs regler på server nivå kan konfigureras med hjälp av Azure Portal eller med hjälp av Azure CLI-kommandon. Du måste vara prenumerations ägare eller en prenumerations deltagare för att skapa brand Väggs regler på server nivå.

## <a name="firewall-overview"></a>Översikt över brandväggar
All databas åtkomst till din Azure Database for PostgreSQL-Server blockeras som standard av brand väggen. Om du vill börja använda servern från en annan dator måste du ange en eller flera brand Väggs regler på server nivå för att aktivera åtkomst till servern. Använd brand Väggs reglerna för att ange vilka IP-adressintervall från Internet som ska tillåtas. Åtkomst till den Azure Portal webbplatsen påverkas inte av brand Väggs reglerna.
Anslutnings försök från Internet och Azure måste först passera brand väggen innan de kan komma åt din PostgreSQL-databas, som du ser i följande diagram:

![Exempel flöde för hur brand väggen fungerar](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brand Väggs regler på server nivå gäller för alla databaser på samma Azure Database for PostgreSQL-Server. Om IP-adressen för begäran ligger inom ett intervall som anges i brandväggsreglerna på servernivå godkänns anslutningen.
Om IP-adressen för begäran inte ligger inom de intervall som anges i brand Väggs reglerna på server nivå, så Miss lyckas anslutningsbegäran.
Om ditt program till exempel ansluter med JDBC-drivrutinen för PostgreSQL kan du stöta på det här felet som försöker ansluta när brand väggen blockerar anslutningen.
> Java. util. samtidig. ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: allvarligt: ingen PG\_HBA. conf-posten för värden "123.45.67.890", användare "adminuser", databas "postgresql", SSL

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure Database for PostgreSQL-Server måste Azure-anslutningar vara aktiverade. Till exempel, för att vara värd för ett Azure Web Apps-program eller ett program som körs på en virtuell Azure-dator eller för att ansluta från en Azure Data Factory Data Management Gateway. Resurserna behöver inte finnas i samma Virtual Network (VNet) eller resurs gruppen för brand Väggs regeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett par metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Alternativt kan du ställa in alternativet **Tillåt åtkomst till Azure-tjänster** på **i portalen** från fönstret **anslutnings säkerhet** och trycka på **Spara**. Om anslutnings försöket inte är tillåtet når begäran inte Azure Database for PostgreSQL servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

![Konfigurera Tillåt åtkomst till Azure-tjänster i portalen](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Ansluta från ett virtuellt nätverk
Överväg att använda [VNet-tjänstens slut punkter](./concepts-data-access-and-security-vnet.md)för att ansluta säkert till din Azure Database for postgresql-server från ett VNet. 

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Förutom Azure Portal kan brand Väggs regler hanteras via programmering med hjälp av Azure CLI.
Se även [skapa och hantera Azure Database for PostgreSQL brand Väggs regler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Felsöka brand Väggs problem
Tänk på följande när du förväntar dig åtkomst till Microsoft Azure databasen för PostgreSQL Server-tjänsten:

* **Ändringar i listan över tillåtna har inte börjat att fungera än:** Det kan finnas en fördröjning på fem minuter för ändringar i Azure Database for PostgreSQL serverns brand Väggs konfiguration som börjar gälla.

* **Inloggningen är inte auktoriserad eller så användes ett felaktigt lösen ord:** Om en inloggning inte har behörighet på Azure Database for PostgreSQL-servern eller om det lösen ord som används är felaktigt nekas anslutningen till Azure Database for PostgreSQL-servern. Att skapa en brand Väggs inställning ger bara klienter möjlighet att försöka ansluta till servern. varje klient måste ändå ange nödvändiga säkerhets uppgifter.

   Om du till exempel använder en JDBC-klient kan följande fel uppstå.
   > Java. util. samtidig. ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: OÅTERKALLELIG: lösenordsautentisering misslyckades för användaren "YOURUSERNAME"

* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan du prova någon av följande lösningar:

   * Be Internet leverantören (ISP) om IP-adressintervallet som har tilldelats till de klient datorer som har åtkomst till Azure Database for PostgreSQL-servern och Lägg sedan till IP-adressintervallet som en brand Väggs regel.

   * Hämta statiska IP-adresser i stället för dina klient datorer och Lägg sedan till den statiska IP-adressen som en brand Väggs regel.

* **Serverns IP-adress verkar vara offentlig:** Anslutningar till Azure Database for PostgreSQL-servern dirigeras via en offentligt tillgänglig Azure-Gateway. Den faktiska server-IP-adressen skyddas dock av brandväggen. Mer information finns i [artikeln om anslutningsarkitektur](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar brand Väggs regler på server nivå och databas nivå finns i:
* [Skapa och hantera Azure Database for PostgreSQL brand Väggs regler med hjälp av Azure Portal](howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure Database for PostgreSQL brand Väggs regler med hjälp av Azure CLI](howto-manage-firewall-using-cli.md)
- [VNet-tjänstens slut punkter i Azure Database for PostgreSQL](./concepts-data-access-and-security-vnet.md)
