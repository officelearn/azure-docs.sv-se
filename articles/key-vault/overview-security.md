---
title: Azure Key Vault-säkerhet | Microsoft Docs
description: Hantera åtkomstbehörigheter för Nyckelvalv, nycklar och hemligheter. Beskriver modellen för autentisering och auktorisering för Key Vault och hur du säkrar ditt nyckelvalv.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 43847b53fbf84fe42be3efdbd647767904a05fb8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014798"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-säkerhet

Du måste skydda krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord i molnet så att du använder Azure Key Vault. Eftersom du lagrar känsliga och affärskritiska data, som du behöver vidta åtgärder för att maximera säkerheten för ditt valv och de data som lagras i dem. Den här artikeln beskriver några av de begrepp som du bör tänka på när du utformar din säkerhet i Azure Key Vault.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

När du skapar ett nyckelvalv i en Azure-prenumeration associeras den automatiskt med Azure AD-klient för prenumerationen. Vem som helst att hantera eller hämta innehåll från ett valv måste autentiseras av Azure AD.

- Autentisering etablerar identiteten hos anroparen.
- Auktoriseringen avgör vilka åtgärder som anroparen kan utföra. Auktorisering i Key Vault använder en kombination av [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) och principer för Azure Key Vault.

### <a name="access-model-overview"></a>Översikt över åtkomst

Åtkomst till valv sker via två gränssnitt eller plan. Dessa plan är Hanteringsplanet och dataplanet.

- Den *Hanteringsplanet* är där du hanterar Key Vault själva och det är det gränssnitt som används för att skapa och ta bort valv. Du kan också läsa egenskaper för nyckelvalvet och hantera principer för åtkomst.
- Den *dataplanet* kan du arbeta med data som lagras i ett nyckelvalv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

Alla anropare (användare eller program) måste autentiseras och auktoriseras för att komma åt ett nyckelvalv i antingen plan. Båda planen använder Azure Active Directory (Azure AD) för autentisering. För auktorisering använder rollbaserad åtkomstkontroll (RBAC) för Hanteringsplanet och dataplanet använder en åtkomstprincip för Nyckelvalvet.

Modell för en enda mekanism för autentisering på båda planen har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckelvalv i organisationen.
- Om en användare lämnar förlorar de omedelbart åtkomst till alla nyckelvalv i organisationen.
- Organisationer kan anpassa autentisering via alternativen i Azure AD, som att aktivera Multi-Factor authentication för extra säkerhet

### <a name="managing-administrative-access-to-key-vault"></a>Hantera administrativ åtkomst till Key Vault

När du skapar ett nyckelvalv i en resursgrupp kan hantera du åtkomst med hjälp av Azure AD. Du ger användare eller grupper möjligheten att hantera nyckelvalv i en resursgrupp. Du kan ge åtkomst på en specifik omfattning genom att tilldela lämpliga RBAC-roller. Om du vill bevilja åtkomst till en användare att hantera nyckelvalven, tilldelar du en fördefinierad `key vault Contributor` roll till användare i ett visst omfång. Följande omfattningar nivåer kan tilldelas till RBAC-roll:

- **Prenumeration**: En RBAC-roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser inom den prenumerationen.
- **Resursgrupp**: En RBAC-roll som tilldelats på resursgruppsnivå gäller för alla resurser i resursgruppen.
- **Specifik resurs**: En RBAC-roll som tilldelats för en specifik resurs gäller till den resursen. Resursen är i det här fallet ett visst nyckelvalv.

Det finns flera fördefinierade roller. Om den fördefinierade rollen inte passar dina behov, kan du definiera en egen roll. Mer information finns i [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en användare har `Contributor` behörigheter till ett nyckelvalv Hanteringsplanet användaren kan ge sig själva åtkomst till dataplanet genom att ange en åtkomstprincip för Nyckelvalvet. Du bör begränsa vem som har `Contributor` platssystemrollens åtkomst till dina nyckelvalv. Se till att endast auktoriserade personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Kontrollera åtkomst till Key Vault-data

Åtkomstprinciper för Nyckelvalvet ger separata behörigheter separat till nycklar, hemligheter eller certifikat. Du kan ge en användaråtkomst endast till nycklar och inte till hemligheter. Åtkomstbehörighet för nycklar, hemligheter och certifikat hanteras på valvnivå.

> [!IMPORTANT]
> Åtkomstprinciper för Key Vault stöder inte detaljerade, på objektnivå behörigheter som en viss nyckel eller hemlighet certifikat. När en användare beviljas behörighet att skapa och ta bort nycklar, kan de utföra de åtgärderna på alla nycklar i nyckelvalvet.

Om du vill ställa in åtkomstprinciper för ett nyckelvalv, använda den [Azure-portalen](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs), eller [Key Vault Management REST API: er](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Du kan begränsa åtkomst till dataplanet genom att använda [tjänstslutpunkter i virtuella nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Du kan konfigurera [brandväggar och virtuella Nätverksregler](key-vault-network-security.md) för ett extra säkerhetslager.

## <a name="network-access"></a>Nätverksåtkomst

Du kan minska exponering av dina valv genom att ange vilka IP-adresser som har åtkomst till dem. Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault kan du begränsa åtkomsten till ett angivet virtuellt nätverk. Slutpunkter kan du begränsa åtkomsten till en lista över adressintervall för IPv4 (internet protocol version 4). Alla användare som ansluter till ditt nyckelvalv från utanför dessa källor nekas åtkomst.

När brandväggen är regler i praktiken användare kan endast läsa data från Key Vault när deras begäranden som kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även för åtkomst till Nyckelvalv från Azure-portalen. Även om användarna kan bläddra till ett nyckelvalv i Azure Portal, kanske de inte att lista nycklar, hemligheter eller certifikat om klientdatorn inte är i listan över tillåtna. Detta påverkar även väljaren för Key Vault med andra Azure-tjänster. Användare kanske kan se listan över nyckelvalv, men inte att lista nycklar, om brandväggsregler förhindra klientdatorn.

Mer information om Azure Key Vault network adress granska [tjänstslutpunkter i virtuella nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Övervakning

Key Vault-loggning sparar information om aktiviteter som utförs i valvet. Key Vault-loggar:

- Alla autentiserade REST API-begäranden, inklusive misslyckade förfrågningar
  - Åtgärder i valvet själva. Dessa åtgärder omfattar skapande, borttagning, inställning av åtkomstprinciper och uppdatera nyckelvärdesattribut som taggar.
  - Åtgärder för nycklar och hemligheter i nyckelvalvet, inklusive:
    - Skapa, ändra eller ta bort nycklar eller hemligheter.
    - Signering, verifiera, kryptera, dekryptera, omsluta och borttagning av nycklar, hämta hemligheter, och lista nycklar och hemligheter (och deras versioner).
- Oautentiserade förfrågningar som resulterar i ett 401-svar. Exempel är begäranden som inte har någon ägartoken, som är felaktiga eller har upphört att gälla eller som har en ogiltig token.

Loggningsinformationen kan nås inom 10 minuter efter åtgärden nyckelvalv. Det är upp till dig att hantera loggarna i ditt storage-konto. 

- Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
- Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Rekommendation för att på ett säkert sätt hantera lagring konton hittar den [säkerhetsguiden för Azure Storage](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Nästa steg

- [Tjänstslutpunkter i virtuella nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Inbyggda roller](../role-based-access-control/built-in-roles.md)
- [Tjänstslutpunkter i virtuella nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
