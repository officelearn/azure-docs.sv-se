---
title: Azure Database for PostgreSQL data kryptering med en server med kundhanterad nyckel
description: Azure Database for PostgreSQL data kryptering med en enskild server med kundhanterad nyckel kan du Bring Your Own Key (BYOK) för data skydd i vila och gör det möjligt för organisationer att implementera separering av uppgifter i hanteringen av nycklar och data.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: f9e60b2f1685e03a9daa7a4801f43799a21eb411
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940559"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>Azure Database for PostgreSQL data kryptering med en server med kundhanterad nyckel

> [!NOTE]
> För tillfället måste du begära åtkomst för att använda den här funktionen. Om du vill göra det kontaktar du AskAzureDBforPostgreSQL@service.microsoft.com.

Azure Database for PostgreSQL data kryptering med en enskild server med kundhanterad nyckel kan du Bring Your Own Key (BYOK) för data skydd i vila och gör det möjligt för organisationer att implementera separering av uppgifter i hanteringen av nycklar och data. Med kundhanterad kryptering ansvarar du för och i en fullständig kontroll av nyckelns livs cykel (skapa, ladda upp, rotera, ta bort), nyckel användnings behörigheter och granskning av åtgärder på nycklar.

För Azure Database for PostgreSQL enskild server anges data kryptering på server nivå. Med den här typen av data kryptering används nyckeln för kryptering av databas krypterings nyckeln (DEK), som är en kundhanterad asymmetrisk nyckel som lagras i ett kundägda och Kundhanterade [Azure Key Vault (AKV)](../key-vault/key-Vault-secure-your-key-Vault.md), ett molnbaserad hanterings system för externa nycklar. AKV har hög tillgänglighet och ger skalbart säkert lagrings utrymme för kryptografiska RSA-nycklar, eventuellt backas upp av FIPS 140-2 nivå 2-validerade maskinvarubaserade säkerhetsmoduler (HSM: er). Den tillåter inte direkt åtkomst till en lagrad nyckel men tillhandahåller tjänster för kryptering/dekryptering med hjälp av nyckeln till auktoriserade entiteter. Nyckeln kan genereras av Key Vault, importeras eller [överförs till Key Vault från en lokal HSM-enhet](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL enskild server stöder Generell användning och minnesoptimerade pris nivåer.

## <a name="benefits"></a>Erbjudande

Data kryptering för Azure Database for PostgreSQL enskild server ger följande fördelar:

* Ökad transparens och detaljerad kontroll och hantering av krypterings nyckeln 
* Central hantering och organisation av nycklar genom att vara värd för dem i Azure Key Vault. 
* Möjlighet att implementera separering av uppgifter i hanteringen av nycklar och data inom organisationen
* Separera nyckel hantering från data hantering i en organisation, så Key Vault administratören kan återkalla nyckel åtkomst behörigheter för att göra den otillgängliga databasen oåtkomlig 
* Bättre förtroende från dina slut kunder, eftersom Azure Key Vault har utformats så att Microsoft inte kan se eller extrahera krypterings nycklar

## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Data krypterings nyckel (DEK)** – en symmetrisk AES256-nyckel som används för att kryptera en partition eller data block. Kryptering av varje data block med en annan nyckel gör det svårare att analysera krypteringen. Åtkomst till DEKs krävs av resurs leverantören eller program instansen som krypterar och dekrypterar ett särskilt block. När en DEK ersätts med en ny nyckel måste endast data i det associerade blocket krypteras igen med den nya nyckeln.

**Nyckel krypterings nyckel (KEK)** – en krypterings nyckel som används för att kryptera data krypterings nycklarna. Om du använder en nyckel krypterings nyckel som aldrig lämnar Key Vault kan data krypterings nycklarna vara krypterade och kontrollerade. Entiteten som har åtkomst till KEK kan skilja sig från den entitet som kräver DEK. Eftersom KEK krävs för att dekryptera DEKs är KEK en enda punkt med vilken DEKs kan tas bort effektivt genom borttagning av KEK.

Data krypterings nycklarna som krypteras med nyckel krypterings nycklarna lagras separat och endast en entitet med åtkomst till nyckel krypterings nyckeln kan dekryptera dessa data krypterings nycklar. Mer information finns i [säkerhet i kryptering i vila](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Hur data kryptering med kundhanterad nyckel fungerar

![Ta med din egen nyckel översikt](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

För att en PostgreSQL-Server ska kunna använda Kundhanterade nycklar som lagras i AKV för kryptering av DEK måste en Key Vault administratör ge följande åtkomst behörighet till servern med hjälp av sin unika identitet:

* **Hämta** för att hämta den offentliga delen och egenskaperna för nyckeln i Key Vault
* **wrapKey** -för att kunna skydda (kryptera) DEK
* **unwrapKey** -för att kunna ta bort skyddet (DEKRYPTERA) DEK

Key Vault-administratören kan också [Aktivera loggning av Key Vault gransknings händelser](../azure-monitor/insights/azure-key-vault.md)så att de kan granskas senare.

När servern har kon figurer ATS för att använda den Kundhanterade nyckeln som lagras i Key Vault, skickar servern DEK till Key Vault för kryptering. Key Vault returnerar den krypterade DEK som lagras i användar databasen. På samma sätt skickar servern skyddade DEK till Key Vault för dekryptering när det behövs. Granskare kan använda Azure Monitor för att granska Key Vault AuditEvent-loggar om loggning är aktiverat.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Krav för att konfigurera data kryptering för Azure Database for PostgreSQL enskild server

### <a name="requirements-for-configuring-akv"></a>Krav för att konfigurera AKV

* Key Vault och Azure Database for PostgreSQL en enskild server måste tillhöra samma Azure Active Directory-klient (AAD). Key Vault mellan klienter och Server interaktioner stöds inte. När du flyttar resurser måste du konfigurera om data krypteringen. Lär dig mer om att flytta resurser.
* Funktionen för mjuk borttagning måste vara aktive rad på Key Vault, för att skydda mot oavsiktlig nyckel (eller Key Vault) borttagning av data förlust. Mjuka, borttagna resurser behålls i 90 dagar, såvida de inte återställs eller rensas av kunden under tiden. Åtgärder för att återställa och rensa har sina egna behörigheter som är kopplade till en Key Vault åtkomst princip. Funktionen mjuk borttagning är inaktive rad som standard och kan aktive ras via PowerShell eller CLI. Den kan inte aktive ras via Azure Portal.
* Bevilja åtkomst till Azure Database for PostgreSQL enskild server till Key Vault med behörigheterna **Get, wrapKey, unwrapKey** med sin unika hanterade identitet. När du använder Azure Portal skapas den unika identifieraren automatiskt när data kryptering är aktiverat på PostgreSQL enskild server. Mer information om hur du använder Azure Portal finns i [Konfigurera data kryptering för postgresql enskild server](howto-data-encryption-portal.md) .

* När du använder en brand vägg med AKV måste du aktivera alternativet *Tillåt att betrodda Microsoft-tjänster kringgår brand väggen*.

### <a name="requirements-for-configuring-customer-managed-key"></a>Krav för att konfigurera kundhanterad nyckel

* Den Kundhanterade nyckeln som ska användas för att kryptera DEK kan bara vara asymmetrisk, RSA 2028.
* Aktiverings datumet (om det är inställt) måste vara datum och tid tidigare. Utgångs datum (om det anges) måste vara ett framtida datum och en framtida tidpunkt.
* Nyckeln måste vara i *aktiverat* läge.
* Om du importerar en befintlig nyckel till Key Vault, se till att tillhandahålla den i de fil format som stöds (`.pfx`, `.byok``.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Rekommendationer när du använder data kryptering med kundhanterad nyckel

### <a name="recommendation-for-configuring-akv"></a>Rekommendation för att konfigurera AKV

* Ange ett resurs lås på Key Vault för att kontrol lera vem som kan ta bort den här kritiska resursen och förhindra oavsiktlig eller obehörig borttagning. Läs mer om resurs lås.
* Aktivera granskning och rapportering på alla krypterings nycklar: Key Vault innehåller loggar som är lätta att mata in i andra säkerhets informations-och händelse hanterings verktyg. Azure Monitor Log Analytics är ett exempel på en tjänst som redan är integrerad.

* Se till att Key Vault och Azure Database for PostgreSQL en enskild server finns i samma region för att säkerställa snabbare åtkomst för DEK figursatta/unwrap-åtgärder.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Rekommendation för att konfigurera kundhanterad nyckel

* Behåll en kopia av den Kundhanterade nyckeln (KEK) på en säker plats eller depositions den till depositions-tjänsten.

* Om nyckeln genereras i Key Vault skapar du en nyckel säkerhets kopia innan du använder nyckeln i AKV för första gången. Säkerhets kopieringen kan bara återställas till en Azure Key Vault. Läs mer om kommandot [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) .

## <a name="inaccessible-customer-managed-key-condition"></a>Otillgängligt kund hanterat nyckel villkor

När data kryptering har kon figurer ATS med kundhanterad nyckel i Azure Key Vault (AKV) krävs kontinuerlig åtkomst till den här nyckeln för att servern ska vara online. Om servern förlorar åtkomst till den Kundhanterade nyckeln i AKV, inom 10 minuter, kommer servern att börja neka alla anslutningar med motsvarande fel meddelande och ändra server tillstånd till **otillgängligt**. Den enda åtgärden som tillåts för en databas i det otillgängliga läget tar bort den.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Oavsiktlig nyckel åtkomst återkallning från Azure Key Vault (AKV)

Det kan hända att någon med tillräckliga åtkomst rättigheter till Key Vault oavsiktligt inaktiverar åtkomst till nyckeln av:

* återkallar Key Vaults get-, wrapKey-, unwrapKey-behörigheter från servern
* tar bort nyckeln
* tar bort Key Vault
* ändra Key Vaults brand Väggs regler

* tar bort den hanterade identiteten för servern i Azure Active Directory

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Övervakning av den Kundhanterade nyckeln i Key Vault

Konfigurera följande Azure-funktioner för att övervaka databas tillstånd och aktivera avisering för förlust av TDE-skydds åtkomst:

* [Azure Resource Health](../service-health/resource-health-overview.md) -en oåtkomlig databas som har förlorat åtkomst till kund nyckeln visas som "oåtkomlig" när den första anslutningen till databasen har nekats.
* [Aktivitets logg](../service-health/alerts-activity-log-service-notifications.md) – när åtkomst till kund nyckeln i den kundhanterade Key Vault Miss lyckas läggs poster till i aktivitets loggen. Genom att skapa aviseringar för dessa händelser kan du återställa åtkomst så snart som möjligt.

* [Åtgärds grupper](../azure-monitor/platform/action-groups.md) kan definieras för att skicka meddelanden och aviseringar baserat på dina inställningar, till exempel e-post, SMS/push/röst, Logic app, webhook, ITSM eller Automation Runbook.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Återställa och replikera med kundens hanterade nyckel i Key Vault

När en Azure Database for PostgreSQL enskild server är krypterad med kundens hanterade nyckel lagrad i Key Vault, krypteras alla nyligen skapade kopior av servern (antingen trots att en lokal eller geo-återställnings åtgärd eller Läs repliker) också är krypterad med samma kundens hanterade nyckel. De kan dock ändras för att återspegla den nya kundens hanterade nyckel för kryptering. När den Kundhanterade nyckeln ändras, kommer gamla säkerhets kopieringar av servern att börja använda den senaste nyckeln.

För att undvika problem när du etablerar en kundhanterad data kryptering under återställningen eller skapandet av en läsning av repliken är det viktigt att följa dessa steg på huvud-och återställnings-/replik servern:

* Initiera processen för att återställa eller läsa replikering från huvud Azure Database for PostgreSQL enskild server.
* Den nyligen skapade servern (återställd/replik) behåller ett otillgängligt tillstånd eftersom dess unika identitet ännu inte har fått behörighet till Azure Key Vault (AKV)
* På den återställda/replik servern verifierar du om den Kundhanterade nyckeln i data krypterings inställningarna för att säkerställa att den nya servern har tilldelats den nyckel som lagras i AKV.

* Båda stegen ovan måste utföras för att säkerställa att data krypteringen bevaras på huvud servern samt den återställda/replik servern.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar data kryptering med kundhanterad nyckel för Azure Database för postgresql-server med hjälp av Azure Portal](howto-data-encryption-portal.md).
