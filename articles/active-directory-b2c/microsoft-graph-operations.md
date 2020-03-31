---
title: Microsoft Graph-åtgärder som stöds
titleSuffix: Azure AD B2C
description: Ett index över Microsoft Graph-åtgärder som stöds för hantering av Azure AD B2C-resurser, inklusive användare, användarflöden, identitetsleverantörer, anpassade principer, principnycklar med mera.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184256"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph-åtgärder tillgängliga för Azure AD B2C

Följande Microsoft Graph API-åtgärder stöds för hantering av Azure AD B2C-resurser, inklusive användare, identitetsleverantörer, användarflöden, anpassade principer och principnycklar.

Varje länk i följande avsnitt riktar sig till motsvarande sida i Microsoft Graph API-referensen för den åtgärden.

## <a name="user-management"></a>Användarhantering

- [Visa användare](https://docs.microsoft.com/graph/api/user-list)
- [Skapa en konsumentanvändare](https://docs.microsoft.com/graph/api/user-post-users)
- [Skaffa en användare](https://docs.microsoft.com/graph/api/user-get)
- [Uppdatera en användare](https://docs.microsoft.com/graph/api/user-update)
- [Ta bort en användare](https://docs.microsoft.com/graph/api/user-delete)

Mer information om hur du hanterar Azure AD B2C-användarkonton med Microsoft Graph API finns i [Hantera Azure AD B2C-användarkonton med Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Identitetsleverantörer (användarflöde)

Hantera de identitetsleverantörer som är tillgängliga för dina användarflöden i din Azure AD B2C-klientorganisation.

- [Lista identitetsleverantörer som är registrerade i Azure AD B2C-klienten](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Skapa en identitetsprovider](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Skaffa en identitetsleverantör](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Uppdatera identitetsprovider](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Ta bort en identitetsprovider](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Användarflöde

Konfigurera färdiga principer för registrering, inloggning, kombinerad registrering och inloggning, återställning av lösenord och profiluppdatering.

- [Lista användarflöden](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Skapa ett användarflöde](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Hämta ett användarflöde](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Ta bort ett användarflöde](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Anpassade principer

Med följande åtgärder kan du hantera dina Azure AD B2C Trust Framework-principer, så kallade [anpassade principer](custom-policy-overview.md).

- [Lista alla principer för förtroenderamar som konfigurerats i en klient](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Skapa princip för förtroenderamar](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Läs egenskaper för en befintlig princip för förtroenderamverk](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Uppdatera eller skapa princip för förtroenderamar.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Ta bort en befintlig princip för förtroenderamverk](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Principnycklar

Identity Experience Framework lagrar hemligheterna som refereras i en anpassad princip för att skapa förtroende mellan komponenter. Dessa hemligheter kan vara symmetriska eller asymmetriska nycklar / värden. I Azure-portalen visas dessa entiteter som **principnycklar**.

Resursen på den översta nivån för principnycklar i Microsoft Graph API är [Trusted Framework Keyset](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Varje **Keyset** innehåller minst en **nyckel**. Om du vill skapa en nyckel skapar du först en tom nyckeluppsättning och genererar sedan en nyckel i nyckeluppsättningen. Du kan skapa en manuell hemlighet, ladda upp ett certifikat eller en PKCS12-nyckel. Nyckeln kan vara en genererad hemlighet, en sträng som du definierar (till exempel Facebook-programmet hemlighet) eller ett certifikat som du laddar upp. Om en nyckeluppsättning har flera nycklar är bara en av nycklarna aktiv.

### <a name="trust-framework-policy-keyset"></a>Nyckeln till förtroenderampolitik

- [Lista nyckeluppsättningarna för förtroenderamar](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Skapa en trust framework-nycklar](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Skaffa en nyckeluppsättning](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Uppdatera en nyckeluppsättning för förtroenderamar](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Ta bort en nyckeluppsättning för förtroenderamar](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Policynyckel för trust framework

- [Hämta aktiv nyckel i nyckeluppsättningen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generera en nyckel i nyckeluppsättningen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Ladda upp en strängbaserad hemlighet](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Ladda upp ett X.509-certifikat](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Ladda upp ett PKCS12-formatcertifikat](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Program

- [Lista program](https://docs.microsoft.com/graph/api/application-list)
- [Skapa ett program](https://docs.microsoft.com/graph/api/resources/application)
- [Uppdatera program](https://docs.microsoft.com/graph/api/application-update)
- [Skapa servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Skapa oauth2Permission Grant](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Ta bort program](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Egenskaper för programtillägg

- [Egenskaper för listtillägg](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C tillhandahåller en katalog som rymmer 100 anpassade attribut per användare. För användarflöden hanteras dessa [tilläggsegenskaper med hjälp av Azure-portalen](custom-policy-custom-attributes.md). För anpassade principer skapar Azure AD B2C egenskapen för dig första gången principen skriver ett värde till tilläggetgenskapen.

## <a name="audit-logs"></a>Granskningsloggar

- [Lista granskningsloggar](https://docs.microsoft.com/graph/api/directoryaudit-list)

Mer information om hur du använder Granskningsloggar för Azure AD B2C med Microsoft Graph API finns i [Komma åt Granskningsloggar för Azure AD B2C](view-audit-logs.md).
