---
title: Felsöka datakryptering - Azure Database för PostgreSQL - Single Server
description: Lär dig hur du felsöker datakrypteringen i din Azure-databas för PostgreSQL - Single Server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2902ff17ac14a48f1a11259339c2ab1bc4595980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299268"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Felsöka datakryptering i Azure Database för PostgreSQL - Single Server

Den här artikeln hjälper dig att identifiera och lösa vanliga problem som kan uppstå i distributionen av En server av Azure Database för PostgreSQL när den konfigureras med datakryptering med hjälp av en kundhanterad nyckel.

## <a name="introduction"></a>Introduktion

När du konfigurerar datakryptering för att använda en kundhanterad nyckel i Azure Key Vault kräver servern kontinuerlig åtkomst till nyckeln. Om servern förlorar åtkomsten till den kundhanterade nyckeln i Azure Key Vault nekar den alla anslutningar, returnerar rätt felmeddelande och ändrar dess tillstånd till ***Otillgänglig i*** Azure-portalen.

Om du inte längre behöver en otillgänglig Azure-databas för PostgreSQL-server kan du ta bort den för att stoppa kostnader. Inga andra åtgärder på servern tillåts förrän åtkomsten till nyckelvalvet har återställts och servern är tillgänglig. Det går inte heller att ändra datakrypteringsalternativet `Yes`från `No` (kundhanterad) till (tjänsthanterad) på en otillgänglig server när den krypteras med en kundhanterad nyckel. Du måste förnya nyckeln manuellt innan servern är tillgänglig igen. Den här åtgärden är nödvändig för att skydda data från obehörig åtkomst medan behörigheter till den kundhanterade nyckeln återkallas.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Vanliga fel som orsakar att servern blir otillgänglig

Följande felkonfigurationer orsakar de flesta problem med datakryptering som använder Azure Key Vault-nycklar:

- Nyckelvalvet är inte tillgängligt eller finns inte:
  - Nyckelvalvet togs bort av misstag.
  - Ett intermittent nätverksfel gör att nyckelvalvet inte är tillgängligt.

- Du har inte behörighet att komma åt nyckelvalvet eller så finns inte nyckeln:
  - Nyckeln har upphört att gälla eller togs bort eller inaktiverades av misstag.
- Den hanterade identiteten för Azure-databasen för PostgreSQL-instansen togs bort av misstag.
  - Den hanterade identiteten för Azure-databasen för PostgreSQL-instansen har inte tillräckliga nyckelbehörigheter. Behörigheterna innehåller till exempel inte Hämta, Figursättning och Uppringning.
  - De hanterade identitetsbehörigheterna till Azure-databasen för PostgreSQL-instansen återkallades eller togs bort.

## <a name="identify-and-resolve-common-errors"></a>Identifiera och lösa vanliga fel

### <a name="errors-on-the-key-vault"></a>Fel i nyckelvalvet

#### <a name="disabled-key-vault"></a>Inaktiverat nyckelvalv

- `AzureKeyVaultKeyDisabledMessage`
- **Förklaring:** Åtgärden kunde inte slutföras på servern eftersom Azure Key Vault-nyckeln är inaktiverad.

#### <a name="missing-key-vault-permissions"></a>Behörigheter för nyckelvalv saknas

- `AzureKeyVaultMissingPermissionsMessage`
- **Förklaring**: Servern har inte de nödvändiga behörigheterna Hämta, Radbryt och Packa upp till Azure Key Vault. Bevilja eventuella saknade behörigheter till tjänstens huvudnamn med ID.

### <a name="mitigation"></a>Åtgärd

- Bekräfta att den kundhanterade nyckeln finns i nyckelvalvet.
- Identifiera nyckelvalvet och gå sedan till nyckelvalvet i Azure-portalen.
- Kontrollera att nyckeln URI identifierar en nyckel som finns.

## <a name="next-steps"></a>Nästa steg

[Använd Azure-portalen för att konfigurera datakryptering med en kundhanterad nyckel i Azure Database för PostgreSQL](howto-data-encryption-portal.md)
