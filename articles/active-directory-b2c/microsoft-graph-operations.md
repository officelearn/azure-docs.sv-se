---
title: Microsoft Graph åtgärder som stöds
titleSuffix: Azure AD B2C
description: Ett index för Microsoft Graph åtgärder som stöds för hantering av Azure AD B2C resurser, inklusive användare, användar flöden, identitets leverantörer, anpassade principer, princip nycklar med mera.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184256"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph åtgärder som är tillgängliga för Azure AD B2C

Följande Microsoft Graph API-åtgärder stöds för hantering av Azure AD B2C resurser, inklusive användare, identitets leverantörer, användar flöden, anpassade principer och princip nycklar.

Varje länk i följande avsnitt riktar sig mot motsvarande sida i Microsoft Graph API-referens för åtgärden.

## <a name="user-management"></a>Användarhantering

- [Visa användare](https://docs.microsoft.com/graph/api/user-list)
- [Skapa en konsument användare](https://docs.microsoft.com/graph/api/user-post-users)
- [Hämta en användare](https://docs.microsoft.com/graph/api/user-get)
- [Uppdatera en användare](https://docs.microsoft.com/graph/api/user-update)
- [Ta bort en användare](https://docs.microsoft.com/graph/api/user-delete)

Mer information om hur du hanterar Azure AD B2C användar konton med Microsoft Graph-API finns i [hantera Azure AD B2C användar konton med Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Identitets leverantörer (användar flöde)

Hantera de identitets leverantörer som är tillgängliga för dina användar flöden i Azure AD B2C-klienten.

- [Visa lista med identitets leverantörer som registrerats i Azure AD B2C klient organisationen](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Skapa en identitets leverantör](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Hämta en identitets leverantör](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Uppdatera identitets leverantör](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Ta bort en identitets leverantör](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Användarflöde

Konfigurera fördefinierade principer för registrering, inloggning, kombinerad registrering och inloggning, återställning av lösen ord och profil uppdatering.

- [Visa lista över användar flöden](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Skapa ett användar flöde](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Hämta ett användar flöde](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Ta bort ett användar flöde](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Anpassade principer

Med följande åtgärder kan du hantera principer för Azure AD B2C förtroende Framework, så kallade [anpassade principer](custom-policy-overview.md).

- [Lista alla förtroende Ramverks principer som kon figurer ATS i en klient](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Skapa förtroende Ramverks princip](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Läsa egenskaper för en befintlig förtroende Ramverks princip](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Uppdatera eller skapa förtroende Ramverks princip.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Ta bort en befintlig princip för förtroende ramverk](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Princip nycklar

I ramverket med identitets upplevelsen lagras hemligheter som refereras i en anpassad princip för att upprätta förtroende mellan komponenter. Dessa hemligheter kan vara symmetriska eller asymmetriska nycklar/värden. I Azure Portal visas dessa entiteter som **princip nycklar**.

Resursen på den översta nivån för princip nycklar i Microsoft Graph API är den [betrodda Ramverks nyckel uppsättningen](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Varje nyckel **uppsättning** innehåller minst en **nyckel**. Skapa en nyckel genom att först skapa en tom nyckel uppsättning och sedan generera en nyckel i nyckel uppsättningen. Du kan skapa en manuell hemlighet, överföra ett certifikat eller en PKCS12 nyckel. Nyckeln kan vara en genererad hemlighet, en sträng som du definierar (till exempel Facebook-programmets hemlighet) eller ett certifikat som du överför. Om en nyckel uppsättning har flera nycklar är bara en av nycklarna aktiv.

### <a name="trust-framework-policy-keyset"></a>Princip uppsättning för förtroende ramverk

- [Visa en lista med de förtroende Framework-standarduppsättningarna](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Skapa ett förtroende Framework-standarduppsättningar](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Hämta en nyckel uppsättning](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Uppdatera en förtroende Framework-gruppuppsättning](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Ta bort ett förtroende Framework-gruppuppsättningar](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Princip nyckel för förtroende ramverk

- [Hämta den aktuella aktiva nyckeln i nyckel uppsättningen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generera en nyckel i nyckel uppsättningen](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Ladda upp en sträng baserad hemlighet](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Ladda upp ett X. 509-certifikat](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Ladda upp ett PKCS12-format certifikat](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Program

- [Lista program](https://docs.microsoft.com/graph/api/application-list)
- [Skapa ett program](https://docs.microsoft.com/graph/api/resources/application)
- [Uppdatera program](https://docs.microsoft.com/graph/api/application-update)
- [Skapa servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Skapa oauth2Permission-beviljande](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Ta bort program](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Egenskaper för program tillägg

- [Egenskaper för List tillägg](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C tillhandahåller en katalog som kan innehålla 100 anpassade attribut per användare. För användar flöden hanteras de här tilläggs egenskaperna [med hjälp av Azure Portal](custom-policy-custom-attributes.md). För anpassade principer skapar Azure AD B2C egenskapen åt dig första gången principen skriver ett värde för egenskapen Extension.

## <a name="audit-logs"></a>Granskningsloggar

- [Lista gransknings loggar](https://docs.microsoft.com/graph/api/directoryaudit-list)

Mer information om hur du kommer åt Azure AD B2C gransknings loggar med Microsoft Graph-API finns i [åtkomst Azure AD B2C gransknings loggar](view-audit-logs.md).
