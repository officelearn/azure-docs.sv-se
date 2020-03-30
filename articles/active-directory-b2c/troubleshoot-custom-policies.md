---
title: Felsöka anpassade principer i Azure Active Directory B2C
description: Lär dig mer om metoder för att lösa fel när du arbetar med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f65e98cec04991fe9edef1b81bcb3ecc3d93d76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186376"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Felsöka anpassade principer för Azure AD B2C och Identity Experience Framework

Om du använder anpassade principer för Azure Active Directory B2C (Azure AD B2C) kan det uppstå utmaningar med att konfigurera Identity Experience Framework i dess XML-format för principspråk. Att lära sig att skriva anpassade principer kan vara som att lära sig ett nytt språk. I den här artikeln beskriver vi några verktyg och tips som kan hjälpa dig att upptäcka och lösa problem.

Den här artikeln fokuserar på felsökning av din anpassade principkonfiguration för Azure AD B2C. Det tar inte upp det förlitande part-programmet eller dess identitetsbibliotek.

## <a name="xml-editing"></a>XML-redigering

Det vanligaste felet vid inställning av anpassade principer är felaktigt formaterad XML. En bra XML-redigerare är nästan nödvändig. Den visar XML internt, färgkoder innehåll, förfyller vanliga termer, håller XML-element indexerade och kan validera mot ett XML-schema.

Två av våra favoritredigerare är [Visual Studio Code](https://code.visualstudio.com/) och [Notepad++](https://notepad-plus-plus.org/).

XML-schemaverifiering identifierar fel innan du laddar upp XML-filen. Hämta XML-schemadefinitionsfilen *TrustFrameworkPolicy_0.3.0.0.xsd*i [startpaketets](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)rotmapp . Om du vill ta reda på hur du använder XSD-schemafilen för validering i redigeraren letar du efter *XML-verktyg* och *XML-validering* eller liknande i redigerarens dokumentation.

Det kan vara till praktiskt att granska XML-regler. Azure AD B2C avvisar alla XML-formateringsfel som identifieras. Ibland kan felaktigt formaterad XML orsaka felmeddelanden som är vilseledande.

## <a name="upload-policies-and-policy-validation"></a>Ladda upp principer och principvalidering

Validering av XML-principfilen utförs automatiskt vid uppladdning. De flesta fel gör att överföringen misslyckas. Valideringen innehåller principfilen som du laddar upp. Den innehåller också kedjan av filer som uppladdningsfilen refererar till (den förlitande partens principfil, tilläggsfilen och basfilen).

Vanliga valideringsfel är följande:

> Kodavsnittet vid fel:`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* ClaimType-värdet kan vara felstavat eller finns inte i schemat.
* ClaimType-värden måste definieras i minst en av filerna i principen.
    Exempel: `<ClaimType Id="issuerUserId">`
* Om ClaimType definieras i tilläggsfilen, men den också används i ett TechnicalProfile-värde i basfilen, resulterar det i ett fel om du överför basfilen.

> Kodavsnittet vid fel:`...makes a reference to a ClaimsTransformation with id...`

* Orsakerna till det här felet kan vara desamma som för ClaimType-felet.

> Kodavsnittet vid fel:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Kontrollera att TenantId-värdet `<TrustFrameworkPolicy\>` `<BasePolicy\>` i och elementen matchar ditt mål Azure AD B2C-klientorganisation.

## <a name="troubleshoot-the-runtime"></a>Felsöka körningen

* Använd **Kör** `https://jwt.ms` nu och för att testa dina principer oberoende av din webb eller mobilapp. Denna webbplats fungerar som en förlitande part ansökan. Den visar innehållet i JSON-webbtoken (JWT) som genereras av din Azure AD B2C-princip.

    Så här skapar du ett `https://jwt.ms` testprogram som kan omdirigeras till för tokeninspektion:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Om du vill spåra utbytet av meddelanden mellan klientwebbläsaren och Azure AD B2C använder du [Fiddler](https://www.telerik.com/fiddler). Det kan hjälpa dig att få en indikation på var din användarresa misslyckas i dina orkestreringssteg.

* I **utvecklingsläge**använder du [Application Insights](troubleshoot-with-application-insights.md) för att spåra aktiviteten för din användarresa för Identity Experience Framework. I **utvecklingsläge**kan du observera utbytet av anspråk mellan Identity Experience Framework och de olika anspråksleverantörer som definieras av tekniska profiler, till exempel identitetsleverantörer, API-baserade tjänster, Azure AD B2C-användarkatalogen och andra tjänster, till exempel Azure Multi-Factor Authentication.

## <a name="recommended-practices"></a>Rekommenderade metoder

**Behåll flera versioner av dina scenarier. Gruppera dem i ett projekt med din ansökan.** Basen, tilläggen och förlitande part filer är direkt beroende av varandra. Spara dem som en grupp. När nya funktioner läggs till i dina principer bör du hålla separata arbetsversioner. Arrangera arbetsversioner i ditt eget filsystem med den programkod de interagerar med. Dina program kan anropa många olika förlitande part principer i en klient. De kan bli beroende av de anspråk som de förväntar sig av dina Azure AD B2C-principer.

**Utveckla och testa tekniska profiler med kända användarresor.** Använd testade startpaketprinciper för att ställa in dina tekniska profiler. Testa dem separat innan du införlivar dem i dina egna användarresor.

**Utveckla och testa användarresor med testade tekniska profiler.** Ändra orchestration-stegen för en användarresa stegvis. Skapa successivt dina avsedda scenarier.

## <a name="next-steps"></a>Nästa steg

Finns på GitHub, ladda ner [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip-arkiv. Du kan också klona databasen:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
