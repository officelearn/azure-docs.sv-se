---
title: Datakryptering med kundhanterad nyckel - Azure Database för PostgreSQL - Enkel server
description: Azure Database for PostgreSQL Single server data encryption with a customer-managed key enables you to Bring Your Own Key (BYOK) for data protection at rest. Det gör det också möjligt för organisationer att genomföra åtskillnad av uppgifter i hanteringen av nycklar och data.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297030"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database för Datakryptering för PostgreSQL-server med en kundhanterad nyckel

> [!NOTE]
> För närvarande måste du begära åtkomst för att kunna använda den här funktionen. Om du vill AskAzureDBforPostgreSQL@service.microsoft.comgöra det kontaktar du .

Datakryptering med kundhanterade nycklar för Azure Database för PostgreSQL Single server gör att du kan ta med din egen nyckel (BYOK) för dataskydd i vila. Det gör det också möjligt för organisationer att genomföra åtskillnad av uppgifter i hanteringen av nycklar och data. Med kundhanterad kryptering ansvarar du för och har full kontroll över en nyckels livscykel, behörigheter för nyckelanvändning och granskning av åtgärder på nycklar.

Datakryptering med kundhanterade nycklar för Azure Database för PostgreSQL Single server anges på servernivå. För en viss server används en kundhanterad nyckel, kallad nyckelkrypteringsnyckeln (KEK), för att kryptera den datakrypteringsnyckel (DEK) som används av tjänsten. KEK är en asymmetrisk nyckel som lagras i en kundägd och kundhanterad [Azure Key Vault-instans.](../key-vault/key-Vault-secure-your-key-Vault.md) Nyckelkrypteringsnyckeln (KEK) och datakrypteringsnyckeln (DEK) beskrivs mer i detalj senare i den här artikeln.

Key Vault är ett molnbaserat externt nyckelhanteringssystem. Den är mycket tillgänglig och ger skalbar, säker lagring för RSA kryptografiska nycklar, eventuellt backas upp av FIPS 140-2 Nivå 2 validerade maskinvarusäkerhetsmoduler (HSM). Det tillåter inte direkt åtkomst till en lagrad nyckel, men tillhandahåller tjänster för kryptering och dekryptering till auktoriserade entiteter. Key Vault kan generera nyckeln, importera den eller [få den överförd från en lokal HSM-enhet](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL Single server har stöd för prisnivåer för "Allmänt syfte" och "Minnesoptimerad".

## <a name="benefits"></a>Fördelar

Datakryptering för Azure Database för PostgreSQL Single-server ger följande fördelar:

* Dataåtkomst styrs helt av dig av möjligheten att ta bort nyckeln och göra databasen otillgänglig 
*    Full kontroll över nyckellivscykeln, inklusive rotation av nyckeln för att anpassa sig till företagets policyer
*    Central hantering och organisation av nycklar i Azure Key Vault
*    Förmåga att genomföra åtskillnad mellan ordningsvakter och DBA och systemadministratörer

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Datakrypteringsnyckel (DEK):** En symmetrisk AES256-nyckel som används för att kryptera en partition eller ett datablock. Om du krypterar varje datablock med en annan nyckel blir kryptoanalysattacker svårare. Åtkomst till DEK:er krävs av resursprovidern eller programinstansen som krypterar och dekrypterar ett visst block. När du ersätter en DEK med en ny nyckel måste endast data i det associerade blocket krypteras om med den nya nyckeln.

**Nyckelkrypteringsnyckel (KEK):** En krypteringsnyckel som används för att kryptera DEK:erna. En KEK som aldrig lämnar Key Vault tillåter DEKs själva att krypteras och kontrolleras. Den enhet som har åtkomst till KEK kan vara annorlunda än den enhet som kräver DEK. Eftersom KEK är skyldig att dekryptera DEK, är KEK effektivt en enda punkt genom vilken DEK effektivt kan tas bort genom radering av KEK.

De-er, krypterade med KEK:erna, lagras separat. Endast en entitet med åtkomst till KEK kan dekryptera dessa DEK:er. Mer information finns [i Säkerhet i kryptering i vila](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Så här fungerar datakryptering med en kundhanterad nyckel

![Diagram som visar en översikt över Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

För att en PostgreSQL-server ska kunna använda kundhanterade nycklar som lagras i Key Vault för kryptering av DEK, ger en Key Vault-administratör följande åtkomsträttigheter till servern:

* **hämta:** För att hämta den offentliga delen och egenskaperna för nyckeln i nyckelvalvet.
* **wrapKey:** För att kunna kryptera DEK.
* **unwrapKey:** För att kunna dekryptera DEK.

Nyckelvalvsadministratören kan också [aktivera loggning av granskningshändelser för Key Vault,](../azure-monitor/insights/azure-key-vault.md)så att de kan granskas senare.

När servern är konfigurerad för att använda den kundhanterade nyckeln som lagras i nyckelvalvet skickar servern DEK till nyckelvalvet för krypteringar. Key Vault returnerar den krypterade DEK, som lagras i användardatabasen. På samma sätt skickar servern, när det behövs, den skyddade DEK till nyckelvalvet för dekryptering. Granskare kan använda Azure Monitor för att granska nyckelvalvsgranskningshändelseloggar, om loggning är aktiverat.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Krav för att konfigurera datakryptering för Azure Database för PostgreSQL Single server

Följande är krav för att konfigurera Key Vault:

* Key Vault och Azure Database för PostgreSQL Single server måste tillhöra samma Azure Active Directory (Azure AD) klient. Nyckelvalv och serverinteraktioner som inte är innehavare stöds inte. Flytta resurser efteråt kräver att du konfigurera om datakryptering.
* Du måste aktivera funktionen för mjuk borttagning i nyckelvalvet för att skydda mot dataförlust om en oavsiktlig borttagning av nyckeln (eller Key Vault) inträffar. Resurser med mjuka borttagna finns kvar i 90 dagar, såvida inte användaren återställer eller rensar dem under tiden. Återställnings- och rensningsåtgärderna har sina egna behörigheter som är associerade i en åtkomstprincip för Nyckelvalv. Funktionen för mjuk borttagning är inaktiverad som standard, men du kan aktivera den via PowerShell eller Azure CLI (observera att du inte kan aktivera den via Azure-portalen).
* Bevilja Azure-databasen för PostgreSQL Enkel server åtkomst till nyckelvalvet med behörigheterna hämta, wrapKey och unwrapKey med hjälp av dess unika hanterade identitet. I Azure-portalen skapas den unika identiteten automatiskt när datakryptering är aktiverad på PostgreSQL Single-servern. Se [Datakryptering för Azure Database för PostgreSQL Single server med hjälp av Azure-portalen](howto-data-encryption-portal.md) för detaljerade steg-för-steg-instruktioner när du använder Azure-portalen.

* När du använder en brandvägg med Key Vault måste du aktivera alternativet **Tillåt betrodda Microsoft-tjänster att kringgå brandväggen**.

Följande är krav för att konfigurera den kundhanterade nyckeln:

* Den kundhanterade nyckeln som ska användas för att kryptera DEK kan bara vara asymmetrisk, RSA 2028.
* Nyckelaktiveringsdatumet (om det är inställt) måste vara ett datum och en tid tidigare. Utgångsdatumet (om det är inställt) måste vara ett framtida datum och en framtida tid.
* Nyckeln måste vara i tillståndet *Aktiverad.*
* Om du importerar en befintlig nyckel till nyckelvalvet måste du ange den`.pfx` `.byok`i `.backup`de filformat som stöds ( , , ).

## <a name="recommendations"></a>Rekommendationer

När du använder datakryptering med hjälp av en kundhanterad nyckel finns här rekommendationer för att konfigurera Key Vault:

* Ange ett resurslås på Key Vault för att styra vem som kan ta bort den här kritiska resursen och förhindra oavsiktlig eller obehörig borttagning.
* Aktivera granskning och rapportering på alla krypteringsnycklar. Key Vault tillhandahåller loggar som är lätta att injicera i annan säkerhetsinformation och händelsehanteringsverktyg. Azure Monitor Log Analytics är ett exempel på en tjänst som redan är integrerad.

* Kontrollera att Key Vault och Azure Database för PostgreSQL Single server finns i samma region, för att säkerställa en snabbare åtkomst för DEK-inrrap- och uppkoksåtgärder.

Här är rekommendationer för att konfigurera en kundhanterad nyckel:

* Förvara en kopia av den kundhanterade nyckeln på en säker plats eller spärra den till spärrtjänsten.

* Om Key Vault genererar nyckeln skapar du en nyckelsäkerhetskopiering innan du använder nyckeln för första gången. Du kan bara återställa säkerhetskopian till Key Vault. Mer information om kommandot säkerhetskopiering finns i [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Oåtkomligt kundhanterat nyckelvillkor

När du konfigurerar datakryptering med en kundhanterad nyckel i Key Vault krävs kontinuerlig åtkomst till den här nyckeln för att servern ska vara online. Om servern förlorar åtkomsten till den kundhanterade nyckeln i Key Vault börjar servern neka alla anslutningar inom 10 minuter. Servern utfärdar ett motsvarande felmeddelande och ändrar servertillståndet till *Otillgängligt*. Den enda åtgärd som tillåts i en databas i det här tillståndet är att ta bort den.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Återkallelse av oavsiktlig åtkomst från Key Vault

Det kan hända att någon med tillräcklig behörighet till Key Vault av misstag inaktiverar serveråtkomst till nyckeln genom att:

* Återkalla nyckelvalvets behörigheter hämta, wrapKey och ta bort nycklar från servern.
* Ta bort nyckeln.
* Tar bort nyckelvalvet.
* Ändra nyckelvalvets brandväggsregler.

* Ta bort den hanterade identiteten för servern i Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Övervaka den kundhanterade nyckeln i Key Vault

Om du vill övervaka databastillståndet och aktivera aviseringar om förlust av transparent datakrypteringsskydd konfigurerar du följande Azure-funktioner:

* [Azure Resource Health](../service-health/resource-health-overview.md): En otillgänglig databas som har förlorat åtkomst till kundnyckeln visas som "Otillgänglig" efter att den första anslutningen till databasen har nekats.
* [Aktivitetslogg:](../service-health/alerts-activity-log-service-notifications.md)När åtkomsten till kundnyckeln i det kundhanterade Nyckelvalvet misslyckas läggs transaktioner till i aktivitetsloggen. Du kan återställa åtkomsten så snart som möjligt om du skapar aviseringar för dessa händelser.

* [Åtgärdsgrupper](../azure-monitor/platform/action-groups.md): Definiera dessa för att skicka meddelanden och aviseringar till dig baserat på dina inställningar.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Återställa och replikera med en kunds hanterade nyckel i Key Vault

När Azure Database for PostgreSQL Single server krypteras med en kunds hanterade nyckel lagrad i Key Vault krypteras även alla nyskapade kopior av servern. Du kan göra den nya kopian antingen via en lokal eller geo-återställningsåtgärd eller genom läsrepliker. Kopian kan dock ändras för att återspegla en ny kunds hanterade nyckel för kryptering. När den kundhanterade nyckeln ändras börjar gamla säkerhetskopior av servern med den senaste nyckeln.

För att undvika problem när du konfigurerar kundhanterad datakryptering under återställning eller läsning replik skapande, är det viktigt att följa dessa steg på befälhavaren och återställda / replik servrar:

* Initiera processen för att skapa återställning eller läsa replik från huvuddatabasen Azure-databas för PostgreSQL Single server.
* Håll den nyskapade servern (återställd/replik) i ett otillgängligt tillstånd, eftersom dess unika identitet ännu inte har fått behörighet till Key Vault.
* På den återställda/replikservern aktiverar du den kundhanterade nyckeln i datakrypteringsinställningarna. Detta säkerställer att den nyskapade servern får radbryts- och uppslitningsbehörigheter till nyckeln som lagras i Key Vault.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar datakryptering med en kundhanterad nyckel för din Azure-databas för PostgreSQL Single server med hjälp av Azure-portalen](howto-data-encryption-portal.md).

