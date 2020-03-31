---
title: Brandväggsregler - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du använder brandväggsregler för att ansluta till Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157278"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Brandväggsregler i Azure Database för PostgreSQL - Single Server
Azure Database for PostgreSQL-serverbrandväggen förhindrar all åtkomst till databasservern tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.
Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brandväggsregler på servernivå.

**Brandväggsregler:** Dessa regler gör det möjligt för klienter att komma åt hela Din Azure-databas för PostgreSQL Server, det vill än alla databaser inom samma logiska server. Brandväggsregler på servernivå kan konfigureras med hjälp av Azure-portalen eller med Azure CLI-kommandon. Om du vill skapa brandväggsregler på servernivå måste du vara prenumerationsägare eller prenumerationsdeltagare.

## <a name="firewall-overview"></a>Översikt över brandväggar
All databasåtkomst till din Azure-databas för PostgreSQL-server blockeras av brandväggen som standard. Om du vill börja använda servern från en annan dator måste du ange en eller flera brandväggsregler på servernivå för att aktivera åtkomst till servern. Använd brandväggsreglerna för att ange vilken IP-adress som ska tillåtas från Internet. Åtkomst till själva Azure-portalwebbplatsen påverkas inte av brandväggsreglerna.
Anslutningsförsök från Internet och Azure måste först passera genom brandväggen innan de når din PostgreSQL-databas, vilket visas i följande diagram:

![Exempel på hur brandväggen fungerar](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brandväggsregler på servernivå gäller för alla databaser på samma Azure-databas för PostgreSQL-server. Om IP-adressen för begäran ligger inom ett intervall som anges i brandväggsreglerna på servernivå godkänns anslutningen.
Om IP-adressen för begäran inte ligger inom de intervall som anges i någon av brandväggsreglerna på servernivå misslyckas anslutningsbegäran.
Om ditt program till exempel ansluter till JDBC-drivrutinen för PostgreSQL kan det här felet uppstå när brandväggen blockerar anslutningen.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", databas "postgresql", SSL

## <a name="connecting-from-azure"></a>Ansluta från Azure
Vi rekommenderar att du hittar den utgående IP-adressen för alla program eller tjänster och uttryckligen tillåter åtkomst till dessa enskilda IP-adresser eller intervall. Du kan till exempel hitta den utgående IP-adressen för en Azure App-tjänst eller använda en offentlig IP som är knuten till en virtuell dator eller annan resurs (se nedan för information om hur du ansluter till en virtuell dators privata IP-övertjänstslutpunkter). 

Om en fast utgående IP-adress inte är tillgänglig för din Azure-tjänst kan du överväga att aktivera anslutningar från alla AZURE-datacenter IP-adresser. Den här inställningen kan aktiveras från Azure-portalen genom att ange alternativet **Tillåt åtkomst till Azure-tjänster** till **PÅ** från **säkerhetsfönstret Anslutning** och trycka på **Spara**. Från Azure CLI, en brandvägg regelinställning med start och adress lika med 0.0.0.0 gör motsvarande. Om anslutningsförsöket inte är tillåtet når begäran inte Azure-databasen för PostgreSQL-servern.

> [!IMPORTANT]
> Alternativet **Tillåt åtkomst till Azure-tjänster** konfigurerar brandväggen så att alla anslutningar från Azure tillåter anslutningar från andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

![Konfigurera Tillåt åtkomst till Azure-tjänster i portalen](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Ansluta från ett virtuella nätverk
Om du vill ansluta säkert till din Azure-databas för PostgreSQL-server från ett virtuella nätverk kan du använda [slutpunkter för VNet-tjänsten](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Förutom Azure-portalen kan brandväggsregler hanteras programmässigt med Azure CLI.
Se även [Skapa och hantera Azure Database för PostgreSQL-brandväggsregler med Hjälp av Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Felsöka brandväggsproblem
Tänk på följande punkter när åtkomsten till Tjänsten Microsoft Azure Database for PostgreSQL Server inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har ännu inte trätt i kraft:** Det kan finnas så mycket som en fem minuters fördröjning för ändringar i Azure-databasen för PostgreSQL Server brandväggskonfiguration att börja gälla.

* **Inloggningen är inte auktoriserad eller ett felaktigt lösenord har använts:** Om en inloggning inte har behörighet på Azure Database for PostgreSQL-servern eller om lösenordet som används är felaktigt nekas anslutningen till Azure Database för PostgreSQL-servern. Om du skapar en brandväggsinställning kan klienterna bara försöka ansluta till servern. Varje klient måste fortfarande tillhandahålla nödvändiga säkerhetsreferenser.

   Med hjälp av en JDBC-klient kan följande fel visas.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: lösenordsautentisering misslyckades för användaren "yourusername"

* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan du prova någon av följande lösningar:

   * Fråga internetleverantören om det IP-adressintervall som tilldelats klientdatorerna som har åtkomst till Azure-databasen för PostgreSQL Server och lägg sedan till IP-adressintervallet som en brandväggsregel.

   * Hämta statisk IP-adressering i stället för klientdatorerna och lägg sedan till den statiska IP-adressen som en brandväggsregel.

* **Serverns IP verkar vara offentlig:** Anslutningar till Azure Database för PostgreSQL-server dirigeras via en offentligt tillgänglig Azure-gateway. Den faktiska server-IP-adressen skyddas dock av brandväggen. Mer information finns i [artikeln för anslutningsarkitektur](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar brandväggsregler på servernivå och brandvägg på databasnivå finns i:
* [Skapa och hantera Azure Database för PostgreSQL-brandväggsregler med hjälp av Azure-portalen](howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure Database för PostgreSQL-brandväggsregler med Hjälp av Azure CLI](howto-manage-firewall-using-cli.md)
- [Slutpunkter för VNet-tjänst i Azure Database för PostgreSQL](./concepts-data-access-and-security-vnet.md)
