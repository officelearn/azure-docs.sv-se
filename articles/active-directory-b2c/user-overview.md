---
title: Översikt över användarkonton i Azure Active Directory B2C
description: Lär dig mer om vilka typer av användarkonton som kan användas i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9630e1f23b6595ca690ecafcf0c4b9bfff603f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185666"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Översikt över användarkonton i Azure Active Directory B2C

I Azure Active Directory B2C (Azure AD B2C) finns det flera typer av konton som kan skapas. Azure Active Directory, Active Directory B2B och Active Directory B2C-resurs i de typer av användarkonton som kan användas.

Följande typer av konton är tillgängliga:

- **Arbetskonto** - Ett arbetskonto kan komma åt resurser i en klient och med en administratörsroll kan klienter hantera.
- **Gästkonto** - Ett gästkonto kan bara vara ett Microsoft-konto eller en Azure Active Directory-användare som kan användas för att komma åt program eller hantera klienter.
- **Konsumentkonto** – Ett konsumentkonto används av en användare av de program som du har registrerat hos Azure AD B2C. Konsumentkonton kan skapas genom:
  - Användaren går igenom ett registreringsanvändarflöde i ett Azure AD B2C-program
  - Använda Microsoft Graph API
  - Använda Azure Portal

## <a name="work-account"></a>Arbetskonto

Ett arbetskonto skapas på samma sätt för alla klienter baserat på Azure AD. Om du vill skapa ett arbetskonto kan du använda informationen i [Snabbstart: Lägg till nya användare i Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Ett arbetskonto skapas med hjälp av alternativet **Nytt användarval** i Azure-portalen.

När du lägger till ett nytt arbetskonto måste du tänka på följande konfigurationsinställningar:

- **Namn** och **användarnamn** - Egenskapen **Namn** innehåller användarens angivna och efternamn. **Användarnamnet** är den identifierare som användaren anger för att logga in. Användarnamnet innehåller hela domänen. Domännamnsdelen av användarnamnet måste antingen vara det ursprungliga standarddomännamnet *your-domain.onmicrosoft.com*eller ett verifierat, icke-federerat [anpassat domännamn](../active-directory/fundamentals/add-custom-domain.md) som *contoso.com*.
- **Profil** - Kontot ställs in med en profil med användardata. Du har möjlighet att ange förnamn, efternamn, befattning och avdelningsnamn. Du kan redigera profilen när kontot har skapats.
- **Grupper** - Använd en grupp för att utföra hanteringsuppgifter som att tilldela licenser eller behörigheter till ett antal användare eller enheter samtidigt. Du kan placera det nya kontot i en befintlig [grupp](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) i din klientorganisation.
- **Katalogroll** - Du måste ange vilken åtkomstnivå användarkontot har till resurser i din klientorganisation. Följande behörighetsnivåer är tillgängliga:

    - **Användare** - Användare kan komma åt tilldelade resurser men kan inte hantera de flesta klientresurser.
    - **Global administratör** - Globala administratörer har full kontroll över alla klientresurser.
    - **Begränsad administratör** - Välj den administrativa rollen eller rollerna för användaren. Mer information om de roller som kan väljas finns i [Tilldela administratörsroller i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Skapa ett arbetskonto

Du kan använda följande information för att skapa ett nytt arbetskonto:

- [Azure-portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Uppdatera en användarprofil

Du kan använda följande information för att uppdatera profilen för en användare:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Återställa ett lösenord för en användare

Du kan använda följande information för att återställa lösenordet för en användare:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Gästanvändare

Du kan bjuda in externa användare till din klientorganisation som gästanvändare. Ett typiskt scenario för att bjuda in en gästanvändare till din Azure AD B2C-klient är att dela administrationsansvar. Ett exempel på hur du använder ett gästkonto finns i [Egenskaper för en Azure Active Directory B2B-samarbetsanvändare](../active-directory/b2b/user-properties.md).

När du bjuder in en gästanvändare till din klientorganisation anger du mottagarens e-postadress och ett meddelande som beskriver inbjudan. Inbjudningslänken tar användaren till samtyckessidan där knappen **Kom igång** är markerad och granskningen av behörigheter accepteras. Om en inkorg inte är kopplad till e-postadressen kan användaren navigera till samtyckessidan genom att gå till en Microsoft-sida med hjälp av de inbjudna autentiseringsuppgifterna. Användaren tvingas sedan lösa in inbjudan på samma sätt som att klicka på länken i e-postmeddelandet. Till exempel: `https://myapps.microsoft.com/B2CTENANTNAME`.

Du kan också använda [Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) för att bjuda in en gästanvändare.

## <a name="consumer-user"></a>Konsumentanvändare

Konsumentanvändaren kan logga in på program som skyddas av Azure AD B2C, men kan inte komma åt Azure-resurser som Azure-portalen. Konsumentanvändaren kan använda ett lokalt konto eller federerade konton, till exempel Facebook eller Twitter. Ett konsumentkonto skapas med hjälp av ett [användarflöde](user-flow-overview.md)för registrering eller inloggning , med hjälp av Microsoft Graph API eller med hjälp av Azure-portalen.

Du kan ange vilka data som samlas in när ett konsumentanvändarkonto skapas med hjälp av anpassade användarattribut. Mer information finns [i Definiera anpassade attribut i Azure Active Directory B2C](user-flow-custom-attributes.md).

Mer information om hur du hanterar konsumentkonton finns i [Hantera Azure AD B2C-användarkonton med Microsoft Graph](manage-user-accounts-graph-api.md).

### <a name="migrate-consumer-user-accounts"></a>Migrera användarkonton för konsumenter

Du kan behöva migrera befintliga konsumentanvändarkonton från alla identitetsleverantörer till Azure AD B2C. Mer information finns i [Migrera användare till Azure AD B2C](user-migration.md).
