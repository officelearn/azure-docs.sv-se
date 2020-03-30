---
title: Brandväggsregler - Azure Database för MariaDB
description: Lär dig mer om hur du använder brandväggsregler för att aktivera anslutningar till din Azure-databas för MariaDB-server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 743e3f50d747993250399493d97fc2becab19319
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532050"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Azure Database för MariaDB-serverbrandväggsregler
Brandväggar förhindrar all åtkomst till databasservern tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.

Om du vill konfigurera en brandvägg skapar du brandväggsregler som anger intervall för godtagbara IP-adresser. Du kan skapa brandväggsregler på servernivå.

**Brandväggsregler:** Dessa regler gör det möjligt för klienter att komma åt hela Azure Database för MariaDB-server, det vill än alla databaser inom samma logiska server. Brandväggsregler på servernivå kan konfigureras med hjälp av Azure-portalen eller Azure CLI-kommandona. Om du vill skapa brandväggsregler på servernivå måste du vara prenumerationsägare eller prenumerationsdeltagare.

## <a name="firewall-overview"></a>Översikt över brandväggar
All databasåtkomst till din Azure-databas för MariaDB-servern blockeras som standard av brandväggen. Om du vill börja använda servern från en annan dator måste du ange en eller flera brandväggsregler på servernivå för att aktivera åtkomst till servern. Använd brandväggsreglerna för att ange vilken IP-adress som ska tillåtas från Internet. Åtkomst till själva Azure-portalwebbplatsen påverkas inte av brandväggsreglerna.

Anslutningsförsök från Internet och Azure måste först passera genom brandväggen innan de kan nå din Azure-databas för MariaDB-databas, vilket visas i följande diagram:

![Exempel på hur brandväggen fungerar](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brandväggsregler på servernivå gäller för alla databaser på Azure Database för MariaDB-servern.

Om IP-adressen för begäran ligger inom ett av de intervall som anges i brandväggsreglerna på servernivå beviljas anslutningen.

Om IP-adressen för begäran ligger utanför de intervall som anges i någon av brandväggsreglerna på databasnivå eller servernivå misslyckas anslutningsbegäran.

## <a name="connecting-from-azure"></a>Ansluta från Azure
Vi rekommenderar att du hittar den utgående IP-adressen för alla program eller tjänster och uttryckligen tillåter åtkomst till dessa enskilda IP-adresser eller intervall. Du kan till exempel hitta den utgående IP-adressen för en Azure App-tjänst eller använda en offentlig IP som är knuten till en virtuell dator eller annan resurs (se nedan för information om hur du ansluter till en virtuell dators privata IP-övertjänstslutpunkter). 

Om en fast utgående IP-adress inte är tillgänglig för din Azure-tjänst kan du överväga att aktivera anslutningar från alla AZURE-datacenter IP-adresser. Den här inställningen kan aktiveras från Azure-portalen genom att ange alternativet **Tillåt åtkomst till Azure-tjänster** till **PÅ** från **säkerhetsfönstret Anslutning** och trycka på **Spara**. Från Azure CLI, en brandvägg regelinställning med start och adress lika med 0.0.0.0 gör motsvarande. Om anslutningsförsöket inte är tillåtet når begäran inte Azure-databasen för MariaDB-servern.

> [!IMPORTANT]
> Alternativet **Tillåt åtkomst till Azure-tjänster** konfigurerar brandväggen så att alla anslutningar från Azure tillåter anslutningar från andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

![Konfigurera Tillåt åtkomst till Azure-tjänster i portalen](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Ansluta från ett virtuella nätverk
Om du vill ansluta säkert till din Azure-databas för MariaDB-server från ett virtuella nätverk kan du använda [slutpunkter för VNet-tjänsten](./concepts-data-access-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Förutom Azure-portalen kan brandväggsregler hanteras programmässigt med hjälp av Azure CLI. 

Se även [Skapa och hantera Azure Database för MariaDB-brandväggsregler med Azure CLI](./howto-manage-firewall-cli.md).

## <a name="troubleshooting-firewall-issues"></a>Felsöka brandväggsproblem
Tänk på följande punkter när åtkomsten till Microsoft Azure Database for MariaDB-servertjänsten inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har ännu inte trätt i kraft:** Det kan finnas så mycket som en fem minuters fördröjning för ändringar i Azure Database för MariaDB Server brandväggskonfiguration att börja gälla.

* **Inloggningen är inte auktoriserad eller ett felaktigt lösenord har använts:** Om en inloggning inte har behörighet på Azure Database for MariaDB-servern eller om lösenordet som används är felaktigt nekas anslutningen till Azure Database för MariaDB-servern. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser.

* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att ta dig igenom brandväggen kan du prova någon av följande lösningar:

   * Fråga internetleverantören om det IP-adressintervall som tilldelats klientdatorerna som har åtkomst till Azure Database för MariaDB-servern och lägg sedan till IP-adressintervallet som en brandväggsregel.

   * Använd statisk IP-adressering i stället för dina klientdatorer och lägg sedan till IP-adresserna som brandväggsregler.

* **Serverns IP verkar vara offentlig:** Anslutningar till Azure Database för MariaDB-servern dirigeras via en offentligt tillgänglig Azure-gateway. Den faktiska server-IP-adressen skyddas dock av brandväggen. Mer information finns i [artikeln för anslutningsarkitektur](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Nästa steg
- [Skapa och hantera Azure Database för MariaDB-brandväggsregler med Hjälp av Azure-portalen](./howto-manage-firewall-portal.md)
- [Skapa och hantera Azure Database för MariaDB-brandväggsregler med Azure CLI](./howto-manage-firewall-cli.md)
- [Slutpunkter för VNet-tjänst i Azure Database för MariaDB](./concepts-data-access-security-vnet.md)
