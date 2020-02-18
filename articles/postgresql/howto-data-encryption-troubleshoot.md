---
title: Data kryptering för Azure Database for PostgreSQL-enskild server fel sökning
description: Lär dig hur du felsöker data kryptering för din Azure Database for PostgreSQL-enskild server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 6d2e027c47bef3186f95c2183b316b5c15511070
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371506"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-postgresql---single-server"></a>Fel sökning av data kryptering med Kundhanterade nycklar i Azure Database for PostgreSQL-enskild server
Den här artikeln beskriver hur du identifierar och löser vanliga problem/fel som uppstår på en Azure Database for PostgreSQL-enskild server som kon figurer ATS med data kryptering med hjälp av kundhanterad nyckel.

## <a name="introduction"></a>Introduktion
När data kryptering har kon figurer ATS för att använda en kundhanterad nyckel i Azure Key Vault, krävs kontinuerlig åtkomst till den här nyckeln för att servern ska vara tillgänglig. Om servern förlorar åtkomsten till den Kundhanterade nyckeln i Azure Key Vault, kommer servern att starta neka alla anslutningar med lämpligt fel meddelande och ändra dess tillstånd till ***otillgängligt*** i Azure Portal.

Om en otillgänglig Azure Database for PostgreSQL-enskild server inte längre behövs kan den tas bort omedelbart för att sluta debiteras kostnaderna. Alla andra åtgärder på servern tillåts inte förrän åtkomst till Azure Key Vault har återställts och servern är tillgänglig igen. Att ändra data krypterings alternativet från "Ja" (kundhanterat) till "nej" (hanterad tjänst) på en oåtkomlig Server är inte heller möjligt medan en server krypteras med kundhanterad. Du måste validera nyckeln manuellt för att servern ska vara tillgänglig igen. Detta är nödvändigt för att skydda data från obehörig åtkomst medan behörigheter till kundhanterad nyckel har återkallats.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Vanliga fel som orsakar att servern blir oåtkomlig

De flesta problem som uppstår när du använder data kryptering med Azure Key Vault orsakas av något av följande fel konfigurationer –

Nyckel valvet är inte tillgängligt eller finns inte

* Nyckel valvet har tagits bort av misstag.
* Ett tillfälligt nätverks fel gör att nyckel valvet inte är tillgängligt.

Inga behörigheter för att komma åt nyckel valvet eller nyckeln finns inte

* Nyckeln har tagits bort av misstag, inaktiverats eller så har nyckeln upphört att gälla.
* Den Azure Database for PostgreSQL-hanterade identiteten för en Server instans togs bort av misstag.
* Behörigheter som beviljats för den Azure Database for PostgreSQL hanterade identiteten för nycklarna är inte tillräckliga (de omfattar inte hämta, Radbryt och packa upp).
* Behörigheter för den Azure Database for PostgreSQL-hanterade identiteten för en enskild server har återkallats eller tagits bort.

## <a name="identify-and-resolve-common-errors"></a>Identifiera och lösa vanliga fel
### <a name="errors-on-the-key-vault"></a>Fel i nyckel valvet

#### <a name="disabled-key-vault"></a>Inaktiverat nyckel valv
* AzureKeyVaultKeyDisabledMessage
* **Förklaring** : åtgärden kunde inte slutföras på servern på grund av att Azure Key Vault nyckeln är inaktive rad.

#### <a name="missing-key-vault-permissions"></a>Nyckel valv saknar behörigheter
* AzureKeyVaultMissingPermissionsMessage
* Servern har inte behörigheterna get, wrap och unwrap för Azure Key Vault behörigheter. Bevilja alla saknade behörigheter till tjänstens huvud namn med ID.

### <a name="mitigation"></a>Åtgärd
* Bekräfta att den Kundhanterade nyckeln finns i Key Vault:
* Identifiera nyckel valvet och gå sedan till nyckel valvet i Azure Portal.
* Se till att nyckeln som identifieras av nyckel-URI: n finns.


## <a name="next-steps"></a>Nästa steg
[Konfigurera data kryptering med en kundhanterad nyckel för Azure Database för postgresql med hjälp av Azure Portal](howto-data-encryption-portal.md).