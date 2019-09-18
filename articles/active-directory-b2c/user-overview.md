---
title: Översikt över användar konton i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om användar konton i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0ef4bca3a2b08271b3623a346df6613b5ad16995
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063145"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Översikt över användar konton i Azure Active Directory B2C

I Azure Active Directory B2C (Azure AD B2C) kan du använda olika typer av konton. Azure Active Directory, Azure Active Directory B2B och Azure Active Directory B2C resurs i de typer av användar konton som kan användas.

Följande typer av konton är tillgängliga:

- **Arbets konto** – ett arbets konto kan komma åt resurser i en klient, och med en administratörs roll kan de hantera klienter.
- **Gäst konto** – ett gäst konto kan bara vara en Microsoft-konto eller en Azure Active Directory användare som kan användas för att få åtkomst till program eller hantera klienter.
- **Konsument konto** – ett konsument konto skapas genom att gå igenom ett registrerings användar flöde i ett Azure AD B2C program eller med hjälp av Azure AD Graph API och används av användare av de program som är registrerade med Azure AD B2C.

## <a name="work-account"></a>arbets konto

Ett arbets konto skapas på samma sätt för alla klienter som baseras på Azure AD. Om du vill skapa ett arbets konto kan du använda informationen i [snabb start: Lägga till nya användare i Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Ett arbets konto skapas med hjälp av det **nya användar** alternativet i Azure Portal.

När du lägger till ett nytt arbets konto måste du tänka på följande konfigurations inställningar:

- **Namn** och **användar namn** – egenskapen **Name** innehåller användarens tilldelade och efter namn. **Användar namnet** är det ID som användaren anger för att logga in. Användar namnet innehåller den fullständiga domänen. Domän namns delen av användar namnet måste antingen vara det initiala standard domän namnet *your-domain.onmicrosoft.com*, eller ett verifierat, icke-federerat [anpassat domän](../active-directory/fundamentals/add-custom-domain.md) namn, till exempel *contoso.com*.
- **Profil** -kontot har kon figurer ATS med en profil med användar data. Du har möjlighet att ange förnamn, efter namn, befattning och avdelnings namn. Du kan redigera profilen när kontot har skapats.
- **Grupper** – Använd en grupp för att utföra hanterings uppgifter, till exempel tilldela licenser eller behörigheter till flera användare eller enheter samtidigt. Du kan ställa in det nya kontot i en befintlig [grupp](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) i din klient organisation.
- **Katalog roll** – du måste ange den åtkomst nivå som användar kontot måste ha för resurser i din klient organisation. Följande behörighets nivåer är tillgängliga:

    - **Användare** – användare har åtkomst till tilldelade resurser, men kan inte hantera de flesta klient resurser.
    - **Global administratör** – globala administratörer har fullständig kontroll över alla klient resurser.
    - **Begränsad administratör** – Välj den administrativa rollen eller rollerna för användaren. Mer information om vilka roller som kan väljas finns [i tilldela administratörs roller i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Skapa ett arbets konto

Du kan använda följande information för att skapa ett nytt arbets konto:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Uppdatera en användar profil

Du kan använda följande information för att uppdatera profilen för en användare:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Återställa ett lösen ord för en användare

Du kan använda följande information för att återställa lösen ordet för en användare:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Gästanvändare

Du kan bjuda in externa användare till din klient som gäst användare. Ett typiskt scenario för att bjuda in en gäst användare till din Azure AD B2C klient organisation är att dela administrations ansvar. Ett exempel på hur du använder ett gäst konto finns i [Egenskaper för en Azure Active Directory B2B-samarbets användare](../active-directory/b2b/user-properties.md).

När du bjuder in en gäst användare till din klient anger du e-postadressen till mottagaren och ett meddelande som beskriver inbjudan. Länken inbjudan tar användaren till sidan för godkännande där knappen **Kom igång** är markerad och granskningen av behörigheter godkänns. Om en inkorg inte är kopplad till e-postadressen kan användaren gå till sidan medgivande genom att gå till en Microsoft-sida med de inbjudna autentiseringsuppgifterna. Användaren tvingas sedan att lösa in inbjudan på samma sätt som om du klickar på länken i e-postmeddelandet. Till exempel: `https://myapps.microsoft.com/B2CTENANTNAME`.

Du kan också använda [Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) för att bjuda in en gäst användare.

## <a name="consumer-user"></a>Konsument användare

Konsument användaren kan logga in på program som skyddas av Azure AD B2C, men kan inte komma åt Azure-resurser som Azure Portal.  Konsument användaren kan använda ett lokalt konto eller federerade konton, till exempel Facebook eller Twitter. Ett konsument konto skapas med hjälp av ett [användar flöde för registrering eller inloggning](../active-directory-b2c/active-directory-b2c-reference-policies.md).

Du kan ange de data som samlas in när ett konsument användar konto skapas med anpassade användarattribut. Mer information finns i [definiera anpassade attribut i Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Du kan använda informationen i avsnittet **skapa konsument användar konton** i [använda Azure AD-Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) för att skapa ett Azure AD B2C konsument konto. Du kan också använda informationen i avsnittet **Uppdatera konsument användar konton** i samma artikel för att hantera egenskaperna för kontot.

### <a name="migrate-consumer-user-accounts"></a>Migrera konsument användar konton

Du kanske behöver migrera befintliga konsument användar konton från valfri identitetsprovider till Azure AD B2C. Mer information finns i [användar migrering](active-directory-b2c-user-migration.md) eller [migrering av användare med sociala identiteter](active-directory-b2c-social-migration.md).
