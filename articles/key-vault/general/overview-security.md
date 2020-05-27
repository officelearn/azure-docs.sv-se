---
title: Azure Key Vault säkerhet | Microsoft Docs
description: Hantera åtkomst behörigheter för Azure Key Vault, nycklar och hemligheter. Beskriver autentiserings-och auktoriserings modellen för Key Vault och hur du skyddar nyckel valvet.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: cd8557a33971be9fd0913bfdf84397d344901581
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834386"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-säkerhet

Du måste skydda krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord i molnet så att du använder Azure Key Vault. Eftersom du lagrar känsliga och affärs kritiska data måste du vidta åtgärder för att maximera säkerheten för dina valv och de data som lagras i dem. Den här artikeln beskriver några av de begrepp som du bör tänka på när du utformar din Azure Key Vault säkerhet.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

När du skapar ett nyckel valv i en Azure-prenumeration associeras det automatiskt med Azure AD-klienten för prenumerationen. Alla som försöker hantera eller hämta innehåll från ett valv måste autentiseras av Azure AD.

- Autentisering upprättar identiteten för anroparen.
- Auktorisering avgör vilka åtgärder som anroparen kan utföra. Auktorisering i Key Vault använder sig av en kombination av [rollbaserad åtkomst kontroll](../../role-based-access-control/overview.md) (RBAC) och Azure Key Vault åtkomst principer.

### <a name="access-model-overview"></a>Översikt över åtkomst modell

Åtkomst till valv äger rum genom två gränssnitt eller plan. Dessa plan är hanterings planet och data planet.

- *Hanterings planet* är den plats där du hanterar Key Vault och är det gränssnitt som används för att skapa och ta bort valv. Du kan också läsa Key Vault-egenskaper och hantera åtkomst principer.
- Med *data planet* kan du arbeta med data som lagras i ett nyckel valv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

För att få åtkomst till ett nyckel valv i något av planerna måste alla anropare (användare eller program) autentiseras och auktoriseras. Båda planerna använder Azure Active Directory (Azure AD) för autentisering. För auktorisering använder hanterings planet rollbaserad åtkomst kontroll (RBAC) och data planet använder en Key Vault åtkomst princip.

Modellen för en enda mekanism för autentisering till båda planerna har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckel valv i organisationen.
- Om en användare lämnar, förlorar de direkt åtkomst till alla nyckel valv i organisationen.
- Organisationer kan anpassa autentiseringen med hjälp av alternativen i Azure AD, till exempel för att aktivera Multi-Factor Authentication för extra säkerhet.

### <a name="managing-administrative-access-to-key-vault"></a>Hantera administrativ åtkomst till Key Vault

När du skapar ett nyckel valv i en resurs grupp hanterar du åtkomst med hjälp av Azure AD. Du beviljar användare eller grupper möjligheten att hantera nyckel valv i en resurs grupp. Du kan bevilja åtkomst på en bestämd omfattnings nivå genom att tilldela lämpliga RBAC-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckel valv tilldelar du en fördefinierad `key vault Contributor` roll till användaren vid en bestämd omfattning. Följande omfattnings nivåer kan tilldelas en RBAC-roll:

- **Prenumeration**: en RBAC-roll som tilldelas på prenumerations nivå gäller för alla resurs grupper och resurser i prenumerationen.
- **Resurs grupp**: en RBAC-roll som tilldelas på resurs grupps nivå gäller för alla resurser i den resurs gruppen.
- **Resurs**: en RBAC-roll som är tilldelad en angiven resurs gäller för den resursen. I det här fallet är resursen ett särskilt nyckel valv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en användare har `Contributor` behörighet till ett nyckel valv hanterings plan kan användaren ge sig själva åtkomst till data planet genom att ange en Key Vault åtkomst princip. Du bör noggrant kontrol lera vem som har `Contributor` roll åtkomst till dina nyckel valv. Se till att endast behöriga personer kan komma åt och hantera nyckel valv, nycklar, hemligheter och certifikat.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Kontrol lera åtkomsten till Key Vault data

Key Vault åtkomst principer beviljar behörigheter separat till nycklar, hemligheter eller certifikat. Du kan endast ge en användare åtkomst till nycklar och inte till hemligheter. Åtkomst behörigheter för nycklar, hemligheter och certifikat hanteras på valv nivån.

> [!IMPORTANT]
> Key Vault åtkomst principer stöder inte detaljerade behörigheter på objekt nivå som en speciell nyckel, hemlighet eller certifikat. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckel valvet.

Om du vill ange åtkomst principer för ett nyckel valv använder du [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azureps-cmdlets-docs)eller [Key Vault hantering REST-API: er](/rest/api/keyvault/).

Du kan begränsa åtkomsten till data planet genom att använda [tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)). Du kan konfigurera [brand väggar och virtuella nätverks regler](network-security.md) för ett extra säkerhets lager.

## <a name="network-access"></a>Nätverksåtkomst

Du kan minska exponeringen för dina valv genom att ange vilka IP-adresser som har åtkomst till dem. Med tjänst slut punkter för virtuella nätverk för Azure Key Vault kan du begränsa åtkomsten till ett angivet virtuellt nätverk. Med slut punkterna kan du också begränsa åtkomsten till en lista över IPv4-adress intervall (Internet Protocol version 4). Alla användare som ansluter till nyckel valvet utanför dessa källor nekas åtkomst.

När brand Väggs reglerna är aktiva kan användarna bara läsa data från Key Vault när deras begär Anden kommer från tillåtna virtuella nätverk eller IPv4-adress intervall. Detta gäller även för att komma åt Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckel valv från Azure Portal, kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klient dator inte finns i listan över tillåtna. Detta påverkar också Key Vault väljare från andra Azure-tjänster. Användarna kanske kan se en lista över nyckel valv, men inte lista nycklar, om brand Väggs reglerna förhindrar sin klient dator.

Mer information om Azure Key Vault nätverks adress granska [slut punkter för virtuella nätverks tjänster för Azure Key Vault](overview-vnet-service-endpoints.md))

## <a name="monitoring"></a>Övervakning

Key Vault loggning sparar information om de aktiviteter som utförts i valvet. Key Vault loggar:

- Alla autentiserade REST API begär Anden, inklusive misslyckade förfrågningar
  - Åtgärder i själva nyckel valvet. Dessa åtgärder omfattar att skapa, ta bort, ställa in åtkomst principer och uppdatera Key Vault-attribut som taggar.
  - Åtgärder för nycklar och hemligheter i nyckel valvet, inklusive:
    - Skapa, ändra eller ta bort nycklar eller hemligheter.
    - Signering, verifiering, kryptering, dekryptering, wrapping och unwrap-nycklar, Hämta hemligheter och Visa nycklar och hemligheter (och deras versioner).
- Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begär Anden som inte har en Bearer-token, som har fel format eller som har upphört att gälla eller som har en ogiltig token.

Loggnings information kan nås inom 10 minuter efter nyckel valvs åtgärden. Det är upp till dig att hantera dina loggar i ditt lagrings konto.

- Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
- Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Rekommendationer för att hantera lagrings konton på ett säkert sätt finns i [Azure Storage säkerhets guide](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Efterföljande moment

- [Tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md)
- [tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)
