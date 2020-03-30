---
title: Azure Key Vault säkerhet | Microsoft-dokument
description: Hantera åtkomstbehörigheter för Azure Key Vault, nycklar och hemligheter. Täcker autentiserings- och auktoriseringsmodellen för Key Vault och hur du skyddar nyckelvalvet.
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
ms.openlocfilehash: 3cc5cb68f430ac8e5070b9c8c4a1aa0c28639311
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270906"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-säkerhet

Du måste skydda krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord i molnet så att du använder Azure Key Vault. Eftersom du lagrar känsliga och affärskritiska data måste du vidta åtgärder för att maximera säkerheten för dina valv och de data som lagras i dem. Den här artikeln innehåller några av de begrepp som du bör tänka på när du utformar din Azure Key Vault-säkerhet.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

När du skapar ett nyckelvalv i en Azure-prenumeration associeras det automatiskt med Azure AD-klienten för prenumerationen. Alla som försöker hantera eller hämta innehåll från ett valv måste autentiseras av Azure AD.

- Autentisering upprättar identiteten på den som ringer.
- Auktoriseringen avgör vilka åtgärder anroparen kan utföra. Auktorisering i Key Vault använder en kombination av [rbac-principer (Role Based Access Control)](../role-based-access-control/overview.md) och Azure Key Vault.

### <a name="access-model-overview"></a>Översikt över Åtkomstmodell

Tillgång till valv sker via två gränssnitt eller plan. Dessa plan är ledningsplan och dataplan.

- *Hanteringsplanet* är där du hanterar Key Vault själv och det är det gränssnitt som används för att skapa och ta bort valv. Du kan också läsa nyckelvalvsegenskaper och hantera åtkomstprinciper.
- *Dataplanet* gör att du kan arbeta med data som lagras i ett nyckelvalv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

För att komma åt ett nyckelvalv i något av planet måste alla anropare (användare eller program) autentiseras och auktoriseras. Båda planen använder Azure Active Directory (Azure AD) för autentisering. För auktorisering använder hanteringsplanet rollbaserad åtkomstkontroll (RBAC) och dataplanet använder en åtkomstprincip för Key Vault.

Modellen för en enda mekanism för autentisering till båda planen har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla viktiga valv i organisationen.
- Om en användare lämnar, förlorar de omedelbart åtkomst till alla viktiga valv i organisationen.
- Organisationer kan anpassa autentiseringen med hjälp av alternativen i Azure AD, till exempel för att aktivera multifaktorautentisering för ökad säkerhet

### <a name="managing-administrative-access-to-key-vault"></a>Hantera administrativ åtkomst till Key Vault

När du skapar ett nyckelvalv i en resursgrupp hanterar du åtkomst med hjälp av Azure AD. Du ger användare eller grupper möjlighet att hantera nyckelvalven i en resursgrupp. Du kan bevilja åtkomst på en viss scopenivå genom att tilldela lämpliga RBAC-roller. Om du vill bevilja åtkomst till en användare för att `key vault Contributor` hantera nyckelvalv tilldelar du en fördefinierad roll till användaren i ett visst omfång. Följande scopenivåer kan tilldelas en RBAC-roll:

- **Prenumeration**: En RBAC-roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser inom den prenumerationen.
- **Resursgrupp**: En RBAC-roll som tilldelats på resursgruppsnivå gäller för alla resurser i resursgruppen.
- **Specifik resurs**: En RBAC-roll som tilldelats för en viss resurs gäller för den resursen. I det här fallet är resursen ett specifikt nyckelvalv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera din egen roll. Mer information finns [i RBAC: Inbyggda roller](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en `Contributor` användare har behörighet till ett nyckelvalvshanteringsplan kan användaren ge sig själva åtkomst till dataplanet genom att ange en åtkomstprincip för Key Vault. Du bör noga styra `Contributor` vem som har rollåtkomst till dina nyckelvalv. Se till att endast behöriga personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Styra åtkomsten till Key Vault-data

Åtkomstprinciper för Key Vault ger behörigheter separat till nycklar, hemligheter eller certifikat. Du kan bara ge en användare åtkomst till nycklar och inte till hemligheter. Åtkomstbehörigheter för nycklar, hemligheter och certifikat hanteras på arkivnivå.

> [!IMPORTANT]
> Åtkomstprinciper för Key Vault stöder inte detaljerade behörigheter på objektnivå som en viss nyckel, hemlighet eller ett certifikat. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckelvalvet.

Om du vill ange åtkomstprinciper för ett nyckelvalv använder du [Azure-portalen,](https://portal.azure.com/) [Azure CLI,](../cli-install-nodejs.md) [Azure PowerShell](/powershell/azureps-cmdlets-docs)eller [REST-API:erna för nyckelvalvshantering](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Du kan begränsa åtkomsten till dataplan genom att använda [slutpunkter för virtuella nätverkstjänster för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Du kan konfigurera [brandväggar och virtuella nätverksregler](key-vault-network-security.md) för ytterligare ett säkerhetslager.

## <a name="network-access"></a>Nätverksåtkomst

Du kan minska exponeringen för dina valv genom att ange vilka IP-adresser som har åtkomst till dem. Slutpunkterna för den virtuella nätverkstjänsten för Azure Key Vault gör att du kan begränsa åtkomsten till ett angivet virtuellt nätverk. Ändpunkterna gör det också möjligt att begränsa åtkomsten till en lista över IPv4-adressintervall (internetprotokoll version 4). Alla användare som ansluter till ditt nyckelvalv utanför dessa källor nekas åtkomst.

När brandväggsregler har gällande kan användare bara läsa data från Key Vault när deras begäranden kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även för åtkomst till Key Vault från Azure-portalen. Även om användare kan bläddra till ett nyckelvalv från Azure-portalen kanske de inte kan lista nycklar, hemligheter eller certifikat om klientdatorn inte finns i listan över tillåtna. Detta påverkar också Nyckelvalvväljaren av andra Azure-tjänster. Användare kanske kan se en lista över nyckelvalv, men inte listnycklar, om brandväggsregler förhindrar klientdatorn.

Mer information om Azure Key Vault-nätverksadressen granskar [slutpunkter för virtuella nätverkstjänster för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Övervakning

Key Vault loggning sparar information om de aktiviteter som utförs på ditt valv. Key Vault loggar:

- Alla autentiserade REST API-begäranden, inklusive misslyckade begäranden
  - Operationer på själva nyckelvalvet. Dessa åtgärder omfattar skapande, borttagning, inställning av åtkomstprinciper och uppdatering av nyckelvalvsattribut som taggar.
  - Åtgärder på nycklar och hemligheter i nyckelvalvet, inklusive:
    - Skapa, ändra eller ta bort dessa nycklar eller hemligheter.
    - Signering, verifiera, kryptera, dekryptera, slå in och packa upp nycklar, få hemligheter och lista nycklar och hemligheter (och deras versioner).
- Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begäranden som inte har en innehavartoken, som är felaktig eller har upphört att gälla eller som har en ogiltig token.

Loggningsinformation kan nås inom 10 minuter efter nyckelvalvet. Det är upp till dig att hantera dina loggar i ditt lagringskonto.

- Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
- Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Om du vill ha rekommendation om hur du hanterar lagringskonton på ett säkert sätt granska [säkerhetsguiden för Azure Storage](../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Efterföljande moment

- [Slutpunkter för virtuella nätverkstjänster för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Inbyggda roller](../role-based-access-control/built-in-roles.md)
- [slutpunkter för virtuella nätverkstjänsten för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
