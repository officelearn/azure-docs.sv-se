---
title: Azure Key Vault-säkerhet
description: Hantera åtkomst behörigheter för Azure Key Vault, nycklar och hemligheter. Beskriver autentiserings-och auktoriserings modellen för Key Vault och hur du skyddar nyckel valvet.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: dc08df7390285f9b6e4701bb1ca5c4227b19f1da
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445038"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-säkerhet

Du använder Azure Key Vault för att skydda krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord i molnet. När du lagrar känsliga och affärs kritiska data måste du vidta åtgärder för att maximera säkerheten för dina valv och de data som lagras i dem.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

När du skapar ett nyckel valv i en Azure-prenumeration associeras det automatiskt med Azure AD-klienten för prenumerationen. Alla som försöker hantera eller hämta innehåll från ett valv måste autentiseras av Azure AD.

- Autentisering upprättar identiteten för anroparen.
- Auktorisering avgör vilka åtgärder som anroparen kan utföra. Auktorisering i Key Vault använder en kombination av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) och Azure Key Vault åtkomst principer.

### <a name="access-model-overview"></a>Översikt över åtkomst modell

Åtkomst till valv äger rum genom två gränssnitt eller plan. Dessa plan är hanterings planet och data planet.

- *Hanterings planet* är den plats där du hanterar Key Vault och är det gränssnitt som används för att skapa och ta bort valv. Du kan också läsa Key Vault-egenskaper och hantera åtkomst principer.
- Med *data planet* kan du arbeta med data som lagras i ett nyckel valv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

För att få åtkomst till ett nyckel valv i något av planerna måste alla anropare (användare eller program) autentiseras och auktoriseras. Båda planerna använder Azure Active Directory (Azure AD) för autentisering. För auktorisering använder hanterings planet Azure-rollbaserad åtkomst kontroll (Azure RBAC) och data planet använder en Key Vault åtkomst princip.

Modellen för en enda mekanism för autentisering till båda planerna har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckel valv i organisationen.
- Om en användare lämnar, förlorar de direkt åtkomst till alla nyckel valv i organisationen.
- Organisationer kan anpassa autentiseringen med hjälp av alternativen i Azure AD, till exempel för att aktivera Multi-Factor Authentication för extra säkerhet.

### <a name="managing-administrative-access-to-key-vault"></a>Hantera administrativ åtkomst till Key Vault

När du skapar ett nyckel valv i en resurs grupp hanterar du åtkomst med hjälp av Azure AD. Du beviljar användare eller grupper möjligheten att hantera nyckel valv i en resurs grupp. Du kan bevilja åtkomst på en bestämd omfattnings nivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckel valv tilldelar du en fördefinierad `key vault Contributor` roll till användaren vid en bestämd omfattning. Följande omfattnings nivåer kan tilldelas en Azure-roll:

- **Prenumeration** : en Azure-roll som tilldelas på prenumerations nivån gäller för alla resurs grupper och resurser i prenumerationen.
- **Resurs grupp** : en Azure-roll som tilldelas på resurs grupps nivå gäller för alla resurser i den resurs gruppen.
- **Resurs** : en Azure-roll som är tilldelad en angiven resurs gäller resursen. I det här fallet är resursen ett särskilt nyckel valv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [Azure RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en användare har `Contributor` behörighet till ett nyckel valv hanterings plan kan användaren ge sig själva åtkomst till data planet genom att ange en Key Vault åtkomst princip. Du bör noggrant kontrol lera vem som har `Contributor` roll åtkomst till dina nyckel valv. Se till att endast behöriga personer kan komma åt och hantera nyckel valv, nycklar, hemligheter och certifikat.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Kontrol lera åtkomsten till Key Vault data

Key Vault åtkomst principer beviljar behörigheter separat till nycklar, hemligheter eller certifikat. Du kan endast ge en användare åtkomst till nycklar och inte till hemligheter. Åtkomst behörigheter för nycklar, hemligheter och certifikat hanteras på valv nivån.

> [!IMPORTANT]
> Key Vault åtkomst principer stöder inte detaljerade behörigheter på objekt nivå som en speciell nyckel, hemlighet eller certifikat. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckel valvet.

Du kan ställa in åtkomst principer för ett nyckel valv använder [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)eller [Key Vault hantering REST-API: er](/rest/api/keyvault/).

Du kan begränsa åtkomsten till data planet genom att använda [tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)). Du kan konfigurera [brand väggar och virtuella nätverks regler](network-security.md) för ett extra säkerhets lager.

## <a name="network-access"></a>Nätverksåtkomst

Du kan minska exponeringen för dina valv genom att ange vilka IP-adresser som har åtkomst till dem. Med tjänst slut punkter för virtuella nätverk för Azure Key Vault kan du begränsa åtkomsten till ett angivet virtuellt nätverk. Med slut punkterna kan du också begränsa åtkomsten till en lista över IPv4-adress intervall (Internet Protocol version 4). Alla användare som ansluter till nyckel valvet utanför dessa källor nekas åtkomst.

När brand Väggs reglerna är aktiva kan användarna bara läsa data från Key Vault när deras begär Anden kommer från tillåtna virtuella nätverk eller IPv4-adress intervall. Detta gäller även för att komma åt Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckel valv från Azure Portal, kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klient dator inte finns i listan över tillåtna. Detta påverkar också Key Vault väljare från andra Azure-tjänster. Användarna kanske kan se en lista över nyckel valv, men inte lista nycklar, om brand Väggs reglerna förhindrar sin klient dator.

Mer information om Azure Key Vault nätverks adress granska [slut punkter för virtuella nätverks tjänster för Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="tls-and-https"></a>TLS och HTTPS

*   Key Vault klient delen (data planet) är en server för flera innehavare. Det innebär att nyckel valv från olika kunder kan dela samma offentliga IP-adress. För att uppnå isolering autentiseras och auktoriseras varje HTTP-begäran oberoende av andra begär Anden.
*   Du kan identifiera äldre versioner av TLS för att rapportera sårbarheter, men eftersom den offentliga IP-adressen delas, är det inte möjligt för Key Vault Service-teamet att inaktivera gamla versioner av TLS för enskilda nyckel valv på transport nivå.
*   HTTPS-protokollet gör att klienten kan delta i TLS-förhandling. **Klienter kan genomdriva den senaste versionen av TLS** , och varje gång en klient gör det, kommer hela anslutningen att använda motsvarande nivå skydd. Det faktum att Key Vault fortfarande har stöd för äldre TLS-versioner påverkar inte säkerheten för anslutningar med nyare TLS-versioner.
*   Trots kända sårbarheter i TLS-protokollet finns det ingen känd attack som tillåter en skadlig agent att extrahera information från ditt nyckel valv när angriparen initierar en anslutning med en TLS-version som har sårbarheter. Angriparen behöver fortfarande autentisera och auktorisera sig själv, och så länge som legitima klienter alltid ansluter med de senaste TLS-versionerna, finns det inget sätt för autentiseringsuppgifterna som har läckts bort från sårbarheter i gamla TLS-versioner.

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Key Vault loggning sparar information om de aktiviteter som utförts i valvet. Fullständig information finns i [Key Vault loggning](logging.md).

Rekommendationer för att hantera lagrings konton på ett säkert sätt finns i [Azure Storage säkerhets guide](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Nästa steg

- [Tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md)