---
title: Hanterade identiteter för Azure
description: Läs mer om hur du använder hanterade identiteter för Azure med Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: a26f188ed2f5e18bdf775cd1fb21001495ffdc89
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461454"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Använda hanterade identiteter för Azure med Service Fabric

En vanlig utmaning när du skapar molnprogram är hur du på ett säkert sätt hanterar autentiseringsuppgifterna i koden för att autentisera till olika tjänster utan att spara dem lokalt på en utvecklararbetsstation eller i källkontroll. *Hanterade identiteter för Azure* löser det här problemet för alla dina resurser i Azure Active Directory (Azure AD) genom att förse dem med automatiskt hanterade identiteter i Azure AD. Du kan använda en tjänsts identitet för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter som lagras i din kod.

*Hanterade identiteter för Azure-resurser* är kostnadsfria med Azure AD för Azure-prenumerationer. Ingen extra kostnad utgår.

> [!NOTE]
> *Hanterade identiteter för Azure* är det nya namnet för tjänsten som tidigare kallades MSI (Managed Service Identity).

## <a name="concepts"></a>Begrepp

Hanterade identiteter för Azure baseras på flera nyckelbegrepp:

- **Klient-ID** - en unik identifierare som genereras av Azure AD och som är knuten till ett program och tjänsthuvudnamn under dess första etablering (se även [program-ID](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **Huvud-ID** - objekt-ID för tjänstens huvudobjekt för din hanterade identitet som används för att bevilja rollbaserad åtkomst till en Azure-resurs.

- **Tjänsthuvudnamn** - ett Azure Active Directory-objekt, som representerar projektionen av ett AAD-program i en viss klient (se även [tjänstens huvudnamn](../active-directory/develop/developer-glossary.md#service-principal-object).)

Det finns två typer av hanterade identiteter:

- En **systemtilldelad hanterad identitet** aktiveras direkt på en Azure-tjänstinstans.  Livscykeln för en systemtilldelad identitet är unik för Azure-tjänstinstansen som den är aktiverad på.
- En **användartilldelad hanterad identitet** skapas som en fristående Azure-resurs. Identiteten kan tilldelas en eller flera Azure-tjänstinstanser och hanteras separat från livscykeln för dessa instanser.

Mer information om skillnaden mellan hanterade identitetstyper finns i [Hur fungerar hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Scenarier som stöds för Service Fabric-program

Hanterade identiteter för Service Fabric stöds endast i Azure-distribuerade Service Fabric-kluster och endast för program som distribueras som Azure-resurser. ett program som inte distribueras som en Azure-resurs kan inte tilldelas en identitet. Begreppsmässigt består stödet för hanterade identiteter i ett Azure Service Fabric-kluster av två faser:

1. Tilldela en eller flera hanterade identiteter till programresursen. Ett program kan tilldelas en enda systemtilldelad identitet och/eller upp till 32 användartilldelade identiteter.

2. Inom programmets definition mappar du en av de identiteter som tilldelats programmet till en enskild tjänst som ingår i programmet.

Den systemtilldelade identiteten för ett program är unikt för det programmet. en användartilldelad identitet är en fristående resurs som kan tilldelas flera program. I ett program kan en enda identitet (oavsett om den är systemtilldelad eller användartilldelad) tilldelas flera tjänster i programmet, men varje enskild tjänst kan bara tilldelas en identitet. Slutligen måste en tjänst tilldelas en identitet som uttryckligen ska ha åtkomst till den här funktionen. I själva verket möjliggör mappningen av ett programs identiteter till dess ingående tjänster isolering i programet – en tjänst får endast använda den identitet som mappats till den.  

För närvarande stöds följande scenarier för den här funktionen:

- Distribuera ett nytt program med en eller flera tjänster och en eller flera tilldelade identiteter

- Tilldela en eller flera hanterade identiteter till ett befintligt (Azure-distribuerat) program för att komma åt Azure-resurser

Följande scenarier stöds inte eller rekommenderas inte. Observera att dessa åtgärder kanske inte blockeras, men kan leda till avbrott i dina program:

- Ta bort eller ändra identiteterna som tilldelats ett program. Om du måste göra ändringar skickar du separata distributioner för att först lägga till en ny identitetstilldelning och tar sedan bort en tidigare tilldelad. Borttagning av en identitet från ett befintligt program kan ha oönskade effekter, inklusive att lämna ditt program i ett tillstånd som inte kan uppgraderas. Det är säkert att stryka ansökan helt och hållet om det är nödvändigt att ta bort en identitet. Observera att detta tar bort den systemtilldelade identitet (om så definieras) som är associerad med programmet och tar bort alla associationer med de användartilldelade identiteter som tilldelats programmet.

- Service Fabric-stöd för hanterade identiteter är för närvarande inte integrerat i [AzureServiceTokenProvider](../key-vault/general/service-to-service-authentication.md).

## <a name="next-steps"></a>Nästa steg

- [Distribuera ett nytt Azure Service Fabric-kluster med stöd för hanterad identitet](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Aktivera stöd för hanterad identitet i ett befintligt Azure Service Fabric-kluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Distribuera ett Azure Service Fabric-program med en användartilldelad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Utnyttja den hanterade identiteten för ett Service Fabric-program från servicekod](./how-to-managed-identity-service-fabric-app-code.md)
- [Bevilja en Azure Service Fabric-programåtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
- [Deklarera och använda programhemligheter som KeyVaultReferences](./service-fabric-keyvault-references.md)
