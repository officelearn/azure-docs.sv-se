---
title: "Azure Active Directory B2C: Anpassade Felsökningsprinciper | Microsoft Docs"
description: "Mer information om metoder för att lösa fel när du arbetar med anpassade principer i Azure Active Directory."
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.openlocfilehash: 023390ba36a74217503ff8b3076ad17978fe3fb8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Felsöka Azure AD B2C anpassade principer och identitet upplevelse Framework

Om du använder Azure Active Directory B2C (Azure AD B2C) anpassade principer, kan det uppstå problem konfigurera identitet får ramverk i dess princip språk XML-format.  Lär dig att skriva anpassade principer kan vara som Lär dig ett nytt språk. I den här artikeln beskrivs verktyg och tips som hjälper dig att snabbt identifiera och lösa problem. 

> [!NOTE]
> Den här artikeln fokuserar på att felsöka konfigurationen av Azure AD B2C anpassad princip. Den adressen inte förlitande partsprogram eller dess identitet-biblioteket.

## <a name="xml-editing"></a>XML-redigering

De vanligaste fel i Konfigurera anpassade principer är felaktigt formaterad XML. En bra XML-redigerare är nästan viktigt. En bra XML-redigerare visar XML internt, färgkodar innehåll, prefills vanliga termer, håller XML-element som indexeras och kan valideras med schemat. Här följer två av våra favorit XML-redigerare:

* [Visual Studio Code](https://code.visualstudio.com/)
* [Anteckningar ++](https://notepad-plus-plus.org/)

XML-schemavalideringen identifierar fel innan du laddar upp XML-filen. Hämta XML-schemadefinitionen TrustFrameworkPolicy_0.3.0.0.xsd i rotmappen på startpaket. Mer information i dokumentationen för XML-redigerare, leta efter *XML-verktyg* och *XML-verifiering*.

En granskning av XML-regler kan vara användbara. Azure AD B2C avvisar alla XML-formatering fel som upptäcks. Ibland kan kan felaktigt formaterad XML orsaka missvisande felmeddelanden.

## <a name="upload-policies-and-policy-validation"></a>Överför principer och Principverifiering

 XML-filen överför valideringen är automatisk. De flesta fel orsakar överföringen misslyckas. Verifieringen inkluderar principfilen som överförs. Den innehåller också en kedja av filer som överför filen refererar till (förlitande part principfilen tilläggsfilen och filen base). 
 
 Följande finns vanliga valideringsfel.

Fel utdrag:`... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* ClaimType värdet kan vara felstavat eller finns inte i schemat.
* ClaimType värden måste definieras i minst en av filerna i principen. 
    Exempel: ` <ClaimType Id="socialIdpUserId">`
* Om ClaimType definieras i tilläggsfilen, men det används också i ett TechnicalProfile värde i filen grundläggande, resulterar ladda upp filen grundläggande i ett fel.

Fel utdrag:`...makes a reference to a ClaimsTransformation with id...`
* Orsaker till felet kan vara desamma som för ClaimType-fel.

Fel utdrag:`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Kontrollera att TenantId värde i den  **\<TrustFrameworkPolicy\>**  och  **\<BasePolicy\>**  element matcha mål Azure AD B2C-klient.  

## <a name="troubleshoot-the-runtime"></a>Felsökning av körningsmiljön

* Använd `Run Now` och `https://jwt.io` att testa din principer oberoende av webb- eller mobila program. Den här webbplatsen fungerar som ett förlitande partsprogram. Den visar innehållet i den Token JWT (JSON Web) som genereras av Azure AD B2C-principen. Om du vill skapa ett testprogram i Identity upplevelse Framework, använder du följande värden:
    * Namn: TestApp
    * Webbapp/webb API: Nej
    * -Klienten: Nej

* Om du vill spåra utbyte av meddelanden mellan klientens webbläsare och Azure AD B2C, Använd [Fiddler](http://www.telerik.com/fiddler). Det kan hjälpa dig få en indikation på om dina användare resa misslyckas i orchestration-steg.

* I **utvecklingsläge**, använda **Programinsikter** att spåra aktiviteten hos din identitet upplevelse Framework användaren resa. I **utvecklingsläge**, du kan se utbyten av anspråk mellan identitet upplevelse Framework och de Anspråksproviders som definieras av teknisk profiler, till exempel identitetsleverantörer, API-baserade tjänster i Azure AD B2C användarkatalog och andra tjänster som Azure flera-Factor-autentisering.  

## <a name="recommended-practices"></a>Rekommenderade metoder

**Spara flera versioner av dina scenarier. Gruppera dem i ett projekt med ditt program.** Bas, tillägg och förlitande part filer är direkt beroende av varandra. Spara dem som en grupp. När nya funktioner läggs till dina principer ha olika fungerande-versioner. Steget fungerar versioner i din egen filsystem med programkod som de nyttjar.  Dina program kan anropa många olika förlitande part principer i en klient. De kan bli beroende på vilka anspråk som de förväntar sig från principer för Azure AD B2C.

**Utveckla och testa teknisk profiler med kända användare resor.** Använd testade starter pack principer för att konfigurera dina tekniska profiler. Testa dem separat innan du lägga till dem i din egen resor för användaren.

**Utveckla och testa användare resor med testade tekniska profiler.** Ändra orchestration-steg för en användare resa inkrementellt. Skapa progressivt din avsedda scenarier.

## <a name="next-steps"></a>Nästa steg

* Hämta [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ZIP-filen i GitHub.
