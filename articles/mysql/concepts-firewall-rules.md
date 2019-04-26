---
title: Azure Database for MySQL-serverbrandväggsregler
description: Beskriver brandväggsregler för din Azure Database for MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a7016b8ca43abee9c3f346c6dec55a101ce4020a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60528333"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL-serverbrandväggsregler
Förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.

Skapa brandväggsregler som anger intervall med godkända IP-adresser om du vill konfigurera en brandvägg. Du kan skapa brandväggsregler på servernivå.

**Brandväggsregler:** Dessa regler gör att klienterna kan komma åt hela Azure Database för MySQL-server, det vill säga alla databaser i samma logiska server. Brandväggsregler på servernivå kan konfigureras med hjälp av Azure-portalen eller Azure CLI-kommandon. Du måste vara prenumerationsägare eller prenumerationsdeltagare för att skapa brandväggsregler på servernivå.

## <a name="firewall-overview"></a>Översikt över brandväggar
Alla databasåtkomst till din Azure Database for MySQL-server är som standard som blockerats av brandväggen. Om du vill börja använda din server från en annan dator, måste du ange en eller flera-brandväggsregler på servernivå för att aktivera åtkomst till din server. Använd brandväggsregler för att ange vilka IP-adressintervall från Internet för att tillåta. Åtkomst till Azure-portalen webbplatsen själva påverkas inte av brandväggsreglerna.

Anslutningsförsök från Internet och Azure måste först passera brandväggen innan de kan nå din Azure Database for MySQL-databas som du ser i följande diagram:

![Exempelflöde för hur brandväggen fungerar](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brandväggsregler på servernivå gäller för alla databaser på Azure Database for MySQL-server.

Om IP-adressen för begäran är inom ett intervall som anges i brandväggsreglerna på servernivå, godkänns anslutningen.

Om IP-adressen för begäran är utanför de intervall som angetts i brandväggsreglerna på databasnivå eller på servernivå, misslyckas anslutningsbegäran.

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure att ansluta till din Azure Database for MySQL-server, vara Azure-anslutningar aktiverade. Till exempel att vara värd för ett Azure Web Apps-program eller ett program som körs i en Azure virtuell dator eller för att ansluta från en Azure Data Factory data management gateway. Resurser behöver inte finnas i samma virtuella nätverk (VNet) eller resursgruppen för brandväggsregeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns flera olika metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Du kan också ange den **Tillåt åtkomst till Azure-tjänster** alternativet att **på** på portalen från den **anslutningssäkerhet** rutan och trycker på **spara**. Om anslutningsförsöket inte tillåts kommer begäran inte att nå Azure Database for MySQL-server.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

![Konfigurera Tillåt åtkomst till Azure-tjänster i portalen](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Utöver Azure portal hanteras brandväggsregler via programmering med hjälp av Azure CLI. Se även [skapa och hantera Azure Database för MySQL-brandväggsregler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Felsöka databasbrandväggen
Tänk på följande när åtkomst till Microsoft Azure Database för MySQL server-tjänsten inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har inte börjat gälla ännu:** Det kan finnas så mycket som fem minuter innan ändringarna till Azure Database för MySQL-Server brandväggskonfiguration ska börja gälla.

* **Inloggningen har inte behörighet eller ett felaktigt lösenord använts:** Om en inloggning inte har behörighet på Azure Database for MySQL-server eller det lösenord som används är felaktig, nekas anslutningen till Azure Database for MySQL-server. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser.

* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan prova du något av följande lösningar:

* Be din Internet Service Provider (ISP) för IP-adressintervall som tilldelats klientdatorer som har åtkomst till Azure Database for MySQL-server och sedan lägga till IP-adressintervallet som en brandväggsregel.

* Använd statisk IP-adressering i stället för dina klientdatorer och lägg sedan till IP-adresserna som brandväggsregler.

## <a name="next-steps"></a>Nästa steg

[Skapa och hantera Azure Database för MySQL-brandväggsregler med hjälp av Azure-portalen](./howto-manage-firewall-using-portal.md)
[skapa och hantera Azure Database för MySQL-brandväggsregler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
