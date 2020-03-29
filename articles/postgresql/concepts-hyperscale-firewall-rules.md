---
title: Brandväggsregler - Hyperskala (Citus) - Azure-databas för PostgreSQL
description: I den här artikeln beskrivs brandväggsregler för Azure Database for PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975575"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Brandväggsregler i Azure Database för PostgreSQL - Hyperskala (Citus)
Azure Database for PostgreSQL-serverbrandväggen förhindrar all åtkomst till cus-koordinatornoden Hyperscale (Citus) tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.
Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brandväggsregler på servernivå.

**Brandväggsregler:** Dessa regler gör det möjligt för klienter att komma åt din citus-koordinatornod (Hyperscale), det vill än alla databaser på samma logiska server. Brandväggsregler på servernivå kan konfigureras med hjälp av Azure-portalen. Om du vill skapa brandväggsregler på servernivå måste du vara prenumerationsägare eller prenumerationsdeltagare.

## <a name="firewall-overview"></a>Översikt över brandväggar
All databasåtkomst till koordinatornoden blockeras av brandväggen som standard. Om du vill börja använda servern från en annan dator måste du ange en eller flera brandväggsregler på servernivå för att aktivera åtkomst till servern. Använd brandväggsreglerna för att ange vilken IP-adress som ska tillåtas från Internet. Åtkomst till själva Azure-portalwebbplatsen påverkas inte av brandväggsreglerna.
Anslutningsförsök från Internet och Azure måste först passera genom brandväggen innan de når din PostgreSQL-databas, vilket visas i följande diagram:

![Exempel på hur brandväggen fungerar](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Ansluta från Internet och från Azure

En citus-brandvägg för hyperskala (Citus) som kan ansluta till gruppens koordinatornod. Brandväggen bestämmer åtkomst genom att konsultera en konfigurerbar lista med regler. Varje regel är en IP-adress, eller adressintervall, som är tillåtna i.

När brandväggen blockerar anslutningar kan det orsaka programfel. Om du använder PostgreSQL JDBC-drivrutinen, till exempel, uppstår ett fel som detta:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

Se [Skapa och hantera brandväggsregler](howto-hyperscale-manage-firewall-using-portal.md) för att lära dig hur reglerna definieras.

## <a name="troubleshooting-the-database-server-firewall"></a>Felsöka brandväggen för databasservern
När åtkomst till Tjänsten Microsoft Azure Database for PostgreSQL - Hyperscale (Citus) inte fungerar som förväntat bör du tänka på följande:

* **Ändringar i listan över tillåtna har ännu inte trätt i kraft:** Det kan finnas så mycket som en fem minuters fördröjning för ändringar i hyperskala (Citus) brandväggen konfiguration att träda i kraft.

* **Användaren är inte auktoriserad eller ett felaktigt lösenord har använts:** Om en användare inte har behörighet på servern eller om lösenordet som används är felaktigt nekas anslutningen till servern. Om du skapar en brandväggsinställning kan klienterna bara försöka ansluta till servern. Varje klient måste fortfarande tillhandahålla nödvändiga säkerhetsreferenser.

Med hjälp av en JDBC-klient kan följande fel visas.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: lösenordsautentisering misslyckades för användaren "yourusername"

* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan du prova någon av följande lösningar:

* Fråga Internet-leverantören (ISP) om det IP-adressintervall som tilldelats klientdatorerna som har åtkomst till cituskoordinatornoden (Hyperscale) och lägg sedan till IP-adressintervallet som en brandväggsregel.

* Hämta statisk IP-adressering i stället för klientdatorerna och lägg sedan till den statiska IP-adressen som en brandväggsregel.

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar brandväggsregler på servernivå och brandvägg på databasnivå finns i:
* [Skapa och hantera Azure Database för PostgreSQL-brandväggsregler med hjälp av Azure-portalen](howto-hyperscale-manage-firewall-using-portal.md)
