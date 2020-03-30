---
title: Metoder för användarmigrering
titleSuffix: Azure AD B2C
description: Migrera användarkonton från en annan identitetsprovider till Azure AD B2C med hjälp av massimport- eller sömlösa migreringsmetoder.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332339"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrera användare till Azure AD B2C

Migrering från en annan identitetsprovider till Azure Active Directory B2C (Azure AD B2C) kan också kräva att befintliga användarkonton migreras. Två migreringsmetoder diskuteras här, *massimport* och *sömlös migrering*. Med någon av tillvägagångssätten måste du skriva ett program eller skript som använder [Microsoft Graph API](manage-user-accounts-graph-api.md) för att skapa användarkonton i Azure AD B2C.

## <a name="bulk-import"></a>Massimport

I massimportflödet utför migreringsprogrammet dessa steg för varje användarkonto:

1. Läs användarkontot från den gamla identitetsleverantören, inklusive dess aktuella autentiseringsuppgifter (användarnamn och lösenord).
1. Skapa ett motsvarande konto i din Azure AD B2C-katalog med aktuella autentiseringsuppgifter.

Använd massimportflödet i någon av dessa två situationer:

- Du har tillgång till en användares autentiseringsuppgifter för klartext (användarnamn och lösenord).
- Autentiseringsuppgifterna är krypterade, men du kan dekryptera dem.

Information om hur du skapar användarkonton programmässigt finns i [Hantera Azure AD B2C-användarkonton med Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="seamless-migration"></a>Sömlös migrering

Använd det sömlösa migreringsflödet om lösenord med klartext i den gamla identitetsprovidern inte är tillgängliga. Till exempel när:

- Lösenordet lagras i ett enkelriktat krypterat format, till exempel med en hash-funktion.
- Lösenordet lagras av den äldre identitetsleverantören på ett sätt som du inte kan komma åt. Till exempel när identitetsprovidern validerar autentiseringsuppgifter genom att ringa en webbtjänst.

Det sömlösa migreringsflödet kräver fortfarande massmigrering av användarkonton, men använder sedan en [anpassad princip](custom-policy-get-started.md) för att fråga ett [REST API](custom-policy-rest-api-intro.md) (som du skapar) för att ange varje användares lösenord vid första inloggningen.

Det sömlösa migreringsflödet har alltså två faser: *massimport* och *ange autentiseringsuppgifter*.

### <a name="phase-1-bulk-import"></a>Fas 1: Massimport

1. I migreringsprogrammet läses användarkontona från den gamla identitetsprovidern.
1. Migreringsprogrammet skapar motsvarande användarkonton i din Azure AD B2C-katalog, men *anger inte lösenord*.

### <a name="phase-2-set-credentials"></a>Fas 2: Ange autentiseringsuppgifter

När massmigrering av kontona är klar utför din anpassade princip och REST API följande när en användare loggar in:

1. Läs Azure AD B2C-användarkontot som motsvarar den angivna e-postadressen.
1. Kontrollera om kontot har flaggats för migrering genom att utvärdera ett booleskt tilläggsattribut.
    - Om tilläggets `True`attribut returnerar anropar du REST-API:et för att validera lösenordet mot den äldre identitetsprovidern.
      - Om REST API bedömer att lösenordet är felaktigt returnerar du ett eget fel till användaren.
      - Om REST API avgör att lösenordet är korrekt skriver du lösenordet till Azure AD B2C-kontot och ändrar attributet boolean-tillägg till `False`.
    - Om det booleska `False`tilläggets attribut returnerar fortsätter du inloggningsprocessen som vanligt.

Om du vill se en anpassad princip och REST-API i exempel kan du se [exemplet sömlös användarmigrering](https://aka.ms/b2c-account-seamless-migration) på GitHub.

![Flödesschema för den sömlösa migreringsmetoden för användarmigrering](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: Sömlöst migreringsflöde*

## <a name="best-practices"></a>Bästa praxis

### <a name="security"></a>Säkerhet

Metoden för sömlös migrering använder ditt eget anpassade REST API för att validera en användares autentiseringsuppgifter mot den äldre identitetsprovidern.

**Du måste skydda DITT REST API mot brute-force-attacker.** En angripare kan skicka in flera lösenord i hopp om att så småningom gissa en användares autentiseringsuppgifter. För att hjälpa till att besegra sådana attacker, sluta betjäna begäranden till ditt REST API när antalet inloggningsförsök passerar ett visst tröskelvärde. Skydda också kommunikationen mellan Azure AD B2C och REST API. Mer information om hur du skyddar dina RESTful API:er för produktion finns i [Secure RESTful API](secure-rest-api.md).

### <a name="user-attributes"></a>Användarattribut

All information i den äldre identitetsprovidern ska inte migreras till din Azure AD B2C-katalog. Identifiera lämplig uppsättning användarattribut som ska lagras i Azure AD B2C innan du migrerar.

- **DO-butik** i Azure AD B2C
  - Användarnamn, lösenord, e-postadresser, telefonnummer, medlemsnummer/identifierare.
  - Samtyckesmarkörer för sekretesspolicy och licensavtal för slutanvändare.
- **Lagra INTE** i Azure AD B2C
  - Känsliga uppgifter som kreditkortsnummer, personnummer (SSN), journaler eller andra uppgifter som regleras av myndigheter eller branschorganisationer.
  - Marknadsförings- eller kommunikationsinställningar, användarbeteenden och insikter.

### <a name="directory-clean-up"></a>Katalogrensning

Innan du startar migreringsprocessen bör du ta tillfället i akt att rensa katalogen.

- Identifiera uppsättningen användarattribut som ska lagras i Azure AD B2C och migrera bara det du behöver. Om det behövs kan du skapa [anpassade attribut](custom-policy-custom-attributes.md) för att lagra mer data om en användare.
- Om du migrerar från en miljö med flera autentiseringskällor (till exempel har varje program en egen användarkatalog) migrerar du till ett enhetligt konto i Azure AD B2C.
- Om flera program har olika användarnamn kan du lagra dem alla i ett Azure AD B2C-användarkonto med hjälp av identitetssamlingen. När det gäller lösenordet, låt användaren välja en och ställa in den i katalogen. Med den sömlösa migreringen ska till exempel endast det valda lösenordet lagras i Azure AD B2C-kontot.
- Ta bort oanvända användarkonton före migreringen eller migrera inte inaktuella konton.

### <a name="password-policy"></a>Lösenordsprincip

Om de konton du migrerar har svagare lösenordsstyrka än den [starka lösenordsstyrka](../active-directory/authentication/concept-sspr-policy.md) som tillämpas av Azure AD B2C kan du inaktivera kravet på starkt lösenord. Mer information finns i [Egenskapen Lösenordsprincip](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Nästa steg

Databasen [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) på GitHub innehåller ett sömlöst exempel på anpassad princip för migrering och exempel på REST API-kod:

[Sömlös anpassad princip för användarmigrering & exempel på REST API-kod](https://aka.ms/b2c-account-seamless-migration)
