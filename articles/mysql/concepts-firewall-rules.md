---
title: Azure Database for MySQL server brand Väggs regler
description: Beskriver brand Väggs regler för din Azure Database for MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0802185b7fb0d1a6d7d41cd1fa5a30f5ce10424b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443912"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL server brand Väggs regler
Brand väggar förhindrar all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Brand väggen beviljar åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.

Konfigurera en brand vägg genom att skapa brand Väggs regler som anger intervall för acceptabla IP-adresser. Du kan skapa brand Väggs regler på server nivå.

**Brand Väggs regler:** Dessa regler gör det möjligt för klienter att komma åt hela Azure Database for MySQL server, det vill säga alla databaser på samma logiska Server. Brand Väggs regler på server nivå kan konfigureras med hjälp av Azure Portal-eller Azure CLI-kommandon. Du måste vara prenumerations ägare eller en prenumerations deltagare för att skapa brand Väggs regler på server nivå.

## <a name="firewall-overview"></a>Översikt över brandväggar
All databas åtkomst till din Azure Database for MySQL Server blockeras som standard av brand väggen. Om du vill börja använda servern från en annan dator måste du ange en eller flera brand Väggs regler på server nivå för att aktivera åtkomst till servern. Använd brand Väggs reglerna för att ange vilka IP-adressintervall från Internet som ska tillåtas. Åtkomst till den Azure Portal webbplatsen påverkas inte av brand Väggs reglerna.

Anslutnings försök från Internet och Azure måste först passera brand väggen innan de kan komma åt din Azure Database for MySQL databas, som du ser i följande diagram:

![Exempel flöde för hur brand väggen fungerar](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brand Väggs regler på server nivå gäller för alla databaser på Azure Database for MySQL-servern.

Om IP-adressen för begäran är inom ett intervall som anges i brand Väggs reglerna på server nivå, beviljas anslutningen.

Om IP-adressen för begäran ligger utanför de intervall som anges i brand Väggs reglerna på databas-eller server nivå, så Miss lyckas anslutningsbegäran.

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure Database for MySQL-server måste Azure-anslutningar vara aktiverade. Till exempel, för att vara värd för ett Azure Web Apps-program eller ett program som körs på en virtuell Azure-dator eller för att ansluta från en Azure Data Factory Data Management Gateway. Resurserna behöver inte finnas i samma Virtual Network (VNet) eller resurs gruppen för brand Väggs regeln för att aktivera dessa anslutningar. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. Det finns ett par metoder för att aktivera dessa typer av anslutningar. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Alternativt kan du ställa in alternativet **Tillåt åtkomst till Azure-tjänster** på **i portalen** från fönstret **anslutnings säkerhet** och trycka på **Spara**. Om anslutnings försöket inte är tillåtet når begäran inte Azure Database for MySQL servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

![Konfigurera Tillåt åtkomst till Azure-tjänster i portalen](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Förutom Azure Portal kan brand Väggs regler hanteras program mässigt med hjälp av Azure CLI. Se även [skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Felsöka databasbrandväggen
Tänk på följande när åtkomst till Microsoft Azure databasen för MySQL Server-tjänsten inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har inte börjat att fungera än:** Det kan finnas en fördröjning på fem minuter för ändringar i Azure Database for MySQL serverns brand Väggs konfiguration som börjar gälla.

* **Inloggningen är inte auktoriserad eller så användes ett felaktigt lösen ord:** Om en inloggning inte har behörighet på Azure Database for MySQL-servern eller om det lösen ord som används är felaktigt nekas anslutningen till Azure Database for MySQL-servern. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser.

* **Dynamisk IP-adress:** Om du har en Internet anslutning med dynamisk IP-adressering och du har problem med att komma åt brand väggen kan du prova någon av följande lösningar:

* Be Internet leverantören (ISP) om IP-adressintervallet som har tilldelats till de klient datorer som har åtkomst till Azure Database for MySQL-servern och Lägg sedan till IP-adressintervallet som en brand Väggs regel.

* Använd statisk IP-adressering i stället för dina klientdatorer och lägg sedan till IP-adresserna som brandväggsregler.

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure Portal](./howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
