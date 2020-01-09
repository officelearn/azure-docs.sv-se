---
title: Översikt över Service Fabric hanterad identitet
description: Den här artikeln är en översikt över hanterade identiteter och program till Azure Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: dc7dafa59596537456accde66e878c06f9e5ca23
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528155"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Hanterad identitet för Service Fabric program (förhands granskning)

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Att skydda dina autentiseringsuppgifter är en viktig uppgift, eftersom de aldrig visas på arbets stationer för utvecklare och inte checkas in i käll kontrollen. Funktionen för hanterad identitet för Azure-resurser i Azure Active Directory (Azure AD) löser problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.

Funktionen för hanterad identitet för Azure-resurser är kostnads fri med Azure AD för Azure-prenumerationer. Det kostar inget extra.

> [!NOTE]
> Hanterad identitet för Azure-resurser är det nya namnet på tjänsten som tidigare kallades Hanterad tjänstidentitet (MSI).

## <a name="terminology"></a>Terminologi

Följande villkor används i dokumentations uppsättningen för hanterade identiteter för Azure-resurser:

- **Klient-ID** – en unik identifierare som genererats av Azure AD och som är kopplad till ett program och tjänstens huvud namn under dess första etablering (se även [program-ID](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **Huvud-ID** – objekt-ID för tjänstens huvud namn för din hanterade identitet som används för att bevilja rollbaserad åtkomst till en Azure-resurs.

- **Tjänstens huvud namn** – ett Azure Active Directory objekt som representerar projektionen av ett AAD-program i en specifik klient organisation (se [tjänstens huvud namn](../active-directory/develop/developer-glossary.md#service-principal-object)).


## <a name="about-managed-identities-in-azure"></a>Om hanterade identiteter i Azure

- [Typer av hanterad identitet (MI) i Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Hur fungerar systemtilldelad hanterad identitet i Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Hur fungerar användardefinierad hanterad identitet (MI) i Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Scenarier som stöds för Service Fabric program

Hanterade identiteter för Service Fabric stöds bara i Azure-distribuerade Service Fabric-kluster och endast för program som distribueras som Azure-resurser. ett program som inte har distribuerats som en Azure-resurs kan inte tilldelas en identitet. Det är konceptuellt att prata, stöd för hanterade identiteter i Azure Service Fabric-klustret består av två faser:

1. Tilldela program resursen en eller flera hanterade identiteter. ett program kan tilldelas en enda tilldelad system identitet och/eller upp till 32 användarspecifika identiteter.

2. I programmets definition mappar du en av identiteterna som tilldelats programmet till en enskild tjänst som omfattar programmet.

Den systemtilldelade identiteten för ett program är unik för det programmet. en användardefinierad identitet är en fristående resurs som kan tilldelas till flera program. I ett program kan en enda identitet (om systemtilldelade eller tilldelade användare) tilldelas till flera tjänster i programmet, men varje enskild tjänst kan bara tilldelas en identitet. Slutligen måste en tjänst tilldelas en identitet som uttryckligen har åtkomst till den här funktionen. Mappningen av ett programs identiteter för komponent tjänster gör det möjligt för en isolering i program – en tjänst kan bara använda den identitet som är mappad till den (och ingen alls om den inte uttryckligen har tilldelats en sådan).  

Listan över scenarier som stöds för för hands versionen är följande:

   - Distribuera ett nytt program med en eller flera tjänster och en eller flera tilldelade identiteter

   - Tilldela en eller flera hanterade identiteter till ett befintligt program för att få åtkomst till Azure-resurser. programmet måste ha distribuerats som en Azure-resurs


Följande scenarier stöds inte eller rekommenderas inte. Observera att de här åtgärderna inte blockeras, men kan leda till avbrott i dina program:

   - Ta bort eller ändra identiteter som har tilldelats till ett program. Om du måste göra ändringar måste du skicka in separata distributioner för att först lägga till en ny identitets tilldelning och sedan ta bort en tidigare tilldelad. Borttagning av en identitet från ett befintligt program kan ha oönskade effekter, inklusive att lämna programmet i ett tillstånd som inte kan uppgraderas. Det är säkert att ta bort programmet helt om det är nödvändigt att ta bort en identitet. OBS! detta tar bort den systemtilldelade identiteten (om den har definierats) som är associerad med programmet och tar bort alla associationer med de användardefinierade identiteter som tilldelats programmet.

   - SF-stöd för hanterade identiteter är inte integrerat för närvarande i [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md); integrationen görs i slutet av för hands versions perioden för funktionen hanterad identitet.

>
> [!NOTE]
>
> Den här funktionen är i för hands version. Det kan till exempel vara föremål för frekventa ändringar och kanske inte lämpar sig för produktions distributioner.

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett nytt Azure Service Fabric-kluster med stöd för hanterad identitet](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Aktivera stöd för hanterad identitet i ett befintligt Azure Service Fabric-kluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Utnyttja den hanterade identiteten för ett Service Fabric program från service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
* [Deklarera och använda program hemligheter som KeyVaultReferences](./service-fabric-keyvault-references.md) 
