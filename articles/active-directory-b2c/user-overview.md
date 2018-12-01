---
title: Översikt över användare som har konton i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om användarkonton i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f517754a8076fc18ed29f7db49f6ff9bfc6c7b3d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725768"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Översikt över användarkonton i Azure Active Directory B2C

Du kan använda olika typer av konton i Azure Active Directory (Azure AD) B2C. Azure Active Directory, Azure Active Directory B2B och Azure Active Directory B2C dela olika typer av användarkonton som kan användas.

Följande typer av konton är tillgängliga:

- **Arbetskonto** – ett arbetskonto kan komma åt resurser i en klient och rollen kan hantera klienter med en administratör.
- **Gästkonto** -ett gästkonto får bara innehålla ett Microsoft-konto eller en Azure Active Directory-användare som kan användas för att komma åt program eller hantera klienter. 
- **Konsumentkonto** -ett konsumentkonto har skapats genom att gå via en registrering användarflödet i ett Azure AD B2C-program eller med hjälp av Azure AD Graph API och används av användarna som är registrerade i Azure AD B2C. 

## <a name="work-account"></a>Arbetskonto

Ett arbetskonto skapas på samma sätt för alla klienter baserat på Azure AD. Om du vill skapa ett arbetskonto kan du använda informationen i [Snabbstart: lägga till nya användare till Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Ett arbetskonto skapas med hjälp av den **ny användare** choice i Azure-portalen.

När du lägger till ett nytt arbetskonto, måste du tänka på följande konfigurationsinställningar:

- **Namnet** och **användarnamn** – **namn** egenskapen innehåller den angivna och användarens efternamn. Den **användarnamn** är identifieraren som användaren anger för att logga in. Användarnamnet innehåller fullständig domänen. Domännamndelen av användarnamnet måste antingen vara det inledande standarddomännamnet *din-domän.onmicrosoft.com*, eller en verifierad icke-federerade [anpassad domän](../active-directory/fundamentals/add-custom-domain.md) namn som  *Contoso.com*.
- **Profilen** -kontot har konfigurerats med en profil för användardata. Har du möjlighet att ange en förnamn, efternamn, jobbtitel och avdelningsnamn. Du kan redigera profilen när kontot skapas.
- **Grupper** -använda en grupp för att utföra hanteringsuppgifter, till exempel tilldela licenser eller behörigheter till ett antal användare eller enheter på samma gång. Du kan placera det nya kontot i en befintlig [grupp](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) i din klient. 
- **Katalogroll** -du måste ange åtkomstnivån som också har användarkontot till resurser i din klient. Följande behörighetsnivåer är tillgängliga:

    - **Användaren** -användare kan komma åt tilldelade resurser, men kan inte hantera de flesta klientresurser.
    - **Global administratör** -globala administratörer har fullständig kontroll över alla klientresurser.
    - **Begränsad administratör** – Välj den eller de administrativa roller för användaren. Läs mer om de roller som kan väljas, [Tilldela administratörsroller i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md). 

### <a name="create-a-work-account"></a>Skapa ett arbetskonto

Du kan använda följande information för att skapa en ny arbetskonto:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_post_users)

### <a name="update-a-user-profile"></a>Uppdatera en användarprofil

Du kan använda följande information för att uppdatera profilen för en användare:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

### <a name="reset-a-password-for-a-user"></a>Återställa ett lösenord för en användare

Du kan använda följande information för att återställa lösenordet för en användare: 

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

## <a name="guest-user"></a>Gästanvändare

Du kan bjuda in externa användare till din klient som gästanvändare. Ett typiskt scenario för att bjuda in en gästanvändare till din Azure AD B2C-klient är att dela administration ansvarsområden. Ett exempel på hur du använder ett gästkonto finns i [egenskaperna för en användare för Azure Active Directory B2B-samarbete](../active-directory/b2b/user-properties.md).

När du bjuder in gästanvändare till din klient, kan du ange e-postadressen till mottagaren och ett meddelande som beskriver inbjudan. Inbjudan länken öppnas sidan medgivande där den **börjar** knapp väljs och granskning av behörigheterna har godkänts. Om en inkorg inte är kopplad till den e-postadressen, kan användaren gå till sidan medgivande genom att gå till en Microsoft-sida med inbjudna autentiseringsuppgifter. Om användaren tvingas sedan att lösa in inbjudan på samma sätt som när du klickar på länken i e-postmeddelandet. Till exempel: `https://myapps.microsoft.com/B2CTENANTNAME`.

Du kan också använda den [Microsoft Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/invitation_post) att bjuda in gästanvändare.

## <a name="consumer-user"></a>Konsument-användare

Konsument-användaren kan logga in på program som skyddas av Azure AD B2C, men kan inte komma åt Azure-resurser, till exempel Azure portal.  Konsument-användaren kan använda ett lokalt konto eller externa konton, t.ex Facebook eller Twitter. Ett konsumentkonto skapas med hjälp av en [registrering eller inloggning användarflödet](../active-directory-b2c/active-directory-b2c-reference-policies.md).

Du kan ange de data som samlas in när ett användarkonto för konsument skapas med hjälp av anpassade användarattribut. Mer information finns i [definiera anpassade attribut i Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Du kan använda informationen i den **skapa användarkonton för konsument** delen av [använder Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) att skapa ett konto för Azure AD B2C-konsument. Du kan också använda informationen i den **uppdatera användarkonton för konsument** avsnittet i samma artikel för att hantera egenskaper för kontot.

### <a name="migrate-consumer-user-accounts"></a>Migrera konsument användarkonton

Du kanske behöver migrera befintliga konsument-användarkonton från alla identitetsprovider till Azure AD B2C. Mer information finns i [användarmigrering](active-directory-b2c-user-migration.md) eller [Migrera användare med sociala identiteter](active-directory-b2c-social-migration.md).