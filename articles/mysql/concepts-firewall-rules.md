---
title: "Azure-databas för MySQL serverbrandväggsreglerna | Microsoft Docs"
description: "Beskriver brandväggsregler för din Azure-databas för MySQL-servern."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 1/20/2018
ms.openlocfilehash: 3cc879fb568965af98997889437c6348e0d341e1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure-databas för MySQL serverbrandväggsreglerna
Brandväggar förhindrar all åtkomst till databasservern förrän du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till servern, baserat på den ursprungliga IP-adressen för varje begäran.

Skapa brandväggsregler som anger godkända IP-adressintervall om du vill konfigurera en brandvägg. Du kan skapa brandväggsregler på servernivå.

**Regler i brandväggen:** reglerna att klienter ska kunna komma åt Azure hela databasen för MySQL-server som är alla databaser inom samma logiska server. Brandväggsregler på servernivå kan konfigureras med hjälp av Azure-portalen eller Azure CLI-kommandona. Du måste vara prenumeration ägare eller deltagare i prenumeration för att skapa brandväggsregler på servernivå.

## <a name="firewall-overview"></a>Översikt över brandväggar
Alla databasåtkomst till din Azure-databas för MySQL-server är som standard blockeras av brandväggen. Du måste ange en eller flera servernivå brandväggsregler för att ge åtkomst till servern om du vill börja använda din server från en annan dator. Använd brandväggsreglerna för att ange vilka IP-adressintervall från Internet för att tillåta. Åtkomst till Azure-portalen webbplatsen själva påverkas inte av brandväggsreglerna.

Anslutningsförsök från Internet och Azure måste först passera genom brandväggen innan de kan komma åt Azure-databasen för MySQL-databas som visas i följande diagram:

![Exempel flödet av hur brandväggen fungerar](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brandväggsregler på servernivå gäller för alla databaser på Azure-databasen för MySQL-servern.

Om IP-adressen för begäran är inom ett intervall som anges i brandväggsreglerna för servernivå, beviljas anslutningen.

Om IP-adressen för begäran är utanför det intervall som anges i någon av databas- eller server-nivå brandväggsregler, misslyckas anslutningsbegäran.

## <a name="connecting-from-azure"></a>Ansluta från Azure
Azure-anslutningar så att program från Azure att ansluta till din Azure-databas för MySQL-servern måste vara aktiverat. Till exempel som värd för ett Azure Web Apps-program eller ett program som körs i en Azure VM eller att ansluta från ett Azure Data Factory data management gateway. Resurser behöver inte finnas i samma virtuella nätverk (VNET) eller resursgrupp för brandväggsregeln att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett antal metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Alternativt kan du ställa in den **Tillåt åtkomst till Azure-tjänster** att **ON** på portalen från den **anslutningssäkerhet** rutan och trycker **spara**. Om anslutningen inte är tillåtet i begäran når inte Azure-databasen för MySQL-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

![Konfigurera Tillåt åtkomst till Azure-tjänster i portalen](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Utöver Azure portal kan brandväggsregler hanteras via programmering med hjälp av Azure CLI. Se även [skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Felsöka databasbrandväggen
Tänk på följande när åtkomst till Microsoft Azure-databas för MySQL server-tjänsten inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har inte börjat gälla ännu:** kan det vara så mycket som en fem-minuters fördröjning för ändringar i Azure-databasen för brandväggskonfigurationen för MySQL-servern ska börja gälla.

* **Inloggningen har inte behörighet eller ett felaktigt lösenord användes:** om en inloggning har inte behörighet för Azure-databas för MySQL-server eller lösenordet som används är felaktig, anslutningen till Azure-databas för MySQL server nekas. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser.

* **Dynamisk IP-adress:** om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att få genom brandväggen, kan du något av följande lösningar:

* Be Internet Service Provider (ISP) för IP-adressintervall som tilldelats till klientdatorer som har åtkomst till Azure-databasen för MySQL-servern och sedan lägga till IP-adressintervall som en brandväggsregel.

* Använd statisk IP-adressering i stället för dina klientdatorer och lägg sedan till IP-adresserna som brandväggsregler.

## <a name="next-steps"></a>Nästa steg

[Skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure portal](./howto-manage-firewall-using-portal.md)
[skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
