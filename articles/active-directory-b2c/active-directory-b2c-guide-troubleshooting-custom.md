---
title: Felsöka anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om metoder för att lösa fel när du arbetar med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: be3a3a50d3ace369964814bc7fcc0fa0d353a332
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54842910"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Felsöka Azure AD B2C anpassade principer och Identitetsupplevelse

Om du använder Azure Active Directory B2C (Azure AD B2C) anpassade principer, kan du uppleva utmaningarna med att ställa in identiteten uppleva ramverket i dess princip språk XML-format.  Lär dig att skriva anpassade principer kan vara så att lära dig ett nytt språk. I den här artikeln beskriver vi verktyg och tips som hjälper dig att snabbt identifiera och lösa problem. 

> [!NOTE]
> Den här artikeln handlar om hur du felsöker din konfiguration för Azure AD B2C-anpassad princip. Den upp inte förlitande partsprogram eller dess identity-bibliotek.

## <a name="xml-editing"></a>XML-redigering

De vanligaste fel i hur du konfigurerar anpassade principer är felaktigt formaterad XML. En bra XML-redigerare är nästan viktigt. En bra XML-redigerare visar XML internt, färgkodar innehåll, prefills vanliga termer, ser till att XML-element som indexeras och kan verifiera med schema. Här följer två av våra favorit XML-redigerare:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Anteckningar ++](https://notepad-plus-plus.org/)

XML-schemavalideringen identifierar fel innan du laddar upp din XML-fil. I rotmappen på startpaket, får du XML-schemadefinitionen TrustFrameworkPolicy_0.3.0.0.xsd. Mer information i dokumentationen för XML-redigerare, leta efter *XML-verktyg* och *XML-verifiering*.

Du kan vara användbara en granskning av XML-regler. Azure AD B2C avvisar alla XML formateringsfel som upptäcks. Ibland kan kan felaktigt formaterad XML leda till felmeddelanden som är vilseledande.

## <a name="upload-policies-and-policy-validation"></a>Ladda upp principer och verifieringen av användarprinciper

 Verifiera uppladdning av XML-filen är automatisk. De flesta fel orsakar att överföringen kommer att misslyckas. Verifieringen den principfil som överförs. Den innehåller också en kedja av filer som uppladdningsfilen avser (förlitande part principfilen tilläggsfilen och grundläggande filen). 
 
 Vanliga verifieringsfel inkluderar följande.

Fel utdrag: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* ClaimType-värdet kan vara felstavad, eller så finns inte i schemat.
* ClaimType-värden måste definieras i minst en av filerna i principen. 
    Exempel: ` <ClaimType Id="socialIdpUserId">`
* Om ClaimType definieras i tilläggsfilen, men den används också i TechnicalProfile-värdet i filen grundläggande, resulterar ladda upp filen grundläggande i ett fel.

Fel utdrag: `...makes a reference to a ClaimsTransformation with id...`
* Orsaker till felet kan vara samma som för ClaimType-fel.

Fel utdrag: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Kontrollera att TenantId värde i den **\<TrustFrameworkPolicy\>** och **\<BasePolicy\>** element matchar din target Azure AD B2C-klient.  

## <a name="troubleshoot-the-runtime"></a>Felsöka körningen

* Använd `Run Now` och `https://jwt.io` att testa din principer oberoende av dina webb- och mobilprogram. Den här webbplatsen fungerar som ett förlitande partsprogram. Den visar innehållet i den JSON Web Token (JWT) som genereras av din Azure AD B2C-princip. Om du vill skapa ett testprogram i Identitetsramverk, använder du följande värden:
    * Namn: TestApp
    * Web App/webb-API: Nej
    * Inbyggd klient: Nej

* Om du vill spåra ett utbyte av meddelanden mellan klientens webbläsare och Azure AD B2C använder [Fiddler](https://www.telerik.com/fiddler). Det kan hjälpa dig få en indikation där användarresan misslyckas i orchestration-steg.

* I **utvecklingsläge**, använda **Application Insights** att spåra aktivitet på resan Identitetsramverk användare. I **utvecklingsläge**, du kan se utbyten av anspråk mellan den Identitetsramverk och de olika Anspråksproviders som definieras av tekniska profiler, till exempel identitetsleverantörer, API-baserade tjänster i Azure AD B2C-användarkatalog och andra tjänster som Azure Multi-Factor Authentication.  

## <a name="recommended-practices"></a>Rekommenderade metoder

**Spara flera versioner av dina scenarier. Gruppera dem i ett projekt med ditt program.** Den bas, tillägg och förlitande part-filer är direkt beroende av varandra. Spara dem som en grupp. När nya funktioner läggs till dina principer, håll separat fungerande versioner. Steg fungerande versioner i din egen filsystem med programkod som de interagerar med.  Dina program kan anropa många olika förlitande part principer i en klient. De kan bli beroende på vilka anspråk som de förväntar sig från dina Azure AD B2C-principer.

**Utveckla och testa tekniska profiler med kända användare resor.** Använd testade starter pack principer för att konfigurera dina tekniska profiler. Testa dem separat innan du lägga till dem i din egen utbildning för användaren.

**Utveckla och testa användare körning med testade tekniska profiler.** Ändra orchestration-steg i en användarresa inkrementellt. Progressivt skapa din avsedda scenarier.

## <a name="next-steps"></a>Nästa steg

* I GitHub, laddar ned den [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip-filen.
