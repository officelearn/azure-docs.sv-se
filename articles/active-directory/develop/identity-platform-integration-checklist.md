---
title: Integrera med Microsoft identity-plattformen | Azure
description: Läs mer om bästa praxis och vanliga förbiseende när du integrerar med Microsoft identity-plattformen (v2.0).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 1f5a6d7501fd387548b111fcb0888515c8f8db25
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076412"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Checklista för Microsoft identity-plattformen integration

Microsoft identity-plattformen integration checklista är avsedd att hjälpa dig att en hög kvalitet och säker integrering. Den går igenom metodtips och vanliga förbiseende när du integrerar med Microsoft identity-plattformen så gå igenom listan med jämna mellanrum att kontrollera att du underhåller kvaliteten och säkerheten för din app-integrering med identity-plattformen. Checklistan är inte avsett att granska hela programmet. Innehållet i checklistan kan ändras efter hand som vi förbättrar för plattformen.

Om du precis har kommit igång, ta en titt på [dokumentation](index.yml) vill veta mer om grundläggande om autentisering och Programscenarier i Microsoft identity-plattformen.

## <a name="testing-your-integration"></a>Testa din integrering

Använd följande checklista för att säkerställa att ditt program effektivt är integrerat med den [Microsoft identity-plattformen](https://docs.microsoft.com/legal/mdsa).

### <a name="basics"></a>Grundläggande inställningar

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Läsa och förstå den [principer för Microsoft-plattformen](https://docs.microsoft.com/legal/mdsa). Se till att ditt program följer villkoren som de har skapats för att skydda användarna och plattformen. |

### <a name="ownership"></a>Ägarskap

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Kontrollera att informationen som är kopplade till kontot som du använde för att registrera och hantera appar är uppdaterad. |

### <a name="branding"></a>Anpassning

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Följa den [varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md). |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Ange ett beskrivande namn och logotyp för ditt program. Den här informationen visas på ditt programs medgivande. Kontrollera att ditt namn och logotyp är representativ för din företagets/produkt så att användare kan fatta välgrundade beslut. Se till att inte alla varumärken brott mot. |

### <a name="privacy"></a>Sekretess

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Innehåller länkar till appens användningsvillkor och sekretesspolicy. |

### <a name="security"></a>Säkerhet

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Underhålla ägarskapet för alla din omdirigerings-URI: er och hålla DNS-posterna för dem uppdaterade. Använd inte jokertecken (*) i din URI: er. Kontrollera att alla URI: er är säker och krypterad (till exempel med hjälp av https-scheman) för web apps. För offentliga klienter använder du plattformsspecifika omdirigerade URI: er om det är tillämpligt (huvudsakligen för iOS och Android). I annat fall omdirigera Använd URI: er med ett högt antal slumpmässighet för att förhindra kollisioner vid anrop tillbaka till din app. Om din app används från en isolerad webbagent, kan du använda https://login.microsoftonline.com/nativeclient. Granska och Trimma alla oanvända eller onödiga omdirigerade URI: er med jämna mellanrum. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Om appen registreras i en katalog, minimera och manuellt övervaka listan med ägare för registrering av appen. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Aktivera inte stöd för den [flöde beviljat med OAuth2-implicit](v2-oauth2-implicit-grant-flow.md) såvida inte uttryckligen krävs. Lär dig mer om giltiga scenariot [här](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Använd inte [resursägarens lösenord credential flöde (ROPC)](v2-oauth-ropc.md), vilket direkt hanterar användarnas lösenord. Det här flödet kräver en hög grad av förtroende och användaren exponering och bör endast användas när andra och säkrare, flöden inte kan användas. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Skydda och hantera dina app-autentiseringsuppgifter. Använd [certifikat autentiseringsuppgifter](active-directory-certificate-credentials.md), inte lösenordsautentiseringsuppgifter (klient hemligheter). Om du måste använda autentiseringsuppgifter för lösenord, inte ställa in den manuellt. Inte lagra autentiseringsuppgifter i kod eller konfigurationsversion och aldrig att de kan hanteras av människor. Använd om möjligt [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) eller [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) att lagra och rotera regelbundet dina autentiseringsuppgifter. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Kontrollera att ditt program begär minsta möjliga behörighet behörighet. Fråga för behörigheter som programmet behöver absolut och endast när det behövs. Förstå de olika [typer av behörigheter](v1-permissions-and-consent.md#types-of-permissions). Endast använda behörigheter för programmet om det behövs; Använd delegerade behörigheter om möjligt. En fullständig lista över Microsoft Graph-behörigheter finns i den här [behörighetsreferens](https://docs.microsoft.com/graph/permissions-reference). |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Om du skyddar ett API med Microsoft identity-plattformen, tänka noga igenom de behörigheter som informationen bör visas. Fundera över vad som är rätt Granulariteten för din lösning och vilka behörigheter kräver administratörens godkännande. Sök efter förväntade behörigheter i de inkommande token innan du fattar några beslut om auktorisering. |

### <a name="implementation"></a>Implementering

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Använd lösningar för modern autentisering (OAuth 2.0 [OpenID Connect](v2-protocols-oidc.md)) på ett säkert sätt logga in användare. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Inte implementerar protokollen själv – Använd [stöds av Microsoft authentication Library](reference-v2-libraries.md) (MSAL server mellanprogram). Kontrollera att du använder den senaste versionen av autentiseringsbibliotek som du har integrerat med. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Om din app kräver data är tillgängliga via [Microsoft Graph](https://developer.microsoft.com/graph), begär behörighet för dessa data med Microsoft Graph-slutpunkt i stället för enskilda API: et. |

### <a name="end-user-experience"></a>Slutanvändarens upplevelse

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | [Förstå samtycke upplevelsen](application-consent-experience.md) och konfigurera de olika delarna i medgivandetext för din app så att slutanvändare och administratörer har tillräckligt med information för att avgöra om de litar på din app. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimera antalet gånger som en användare måste ange autentiseringsuppgifter för inloggning när du använder din app genom att försöka tyst autentisering (tyst tokenförvärv) innan interaktiva flöden. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Använd inte ”kommandoprompt = medgivande” för varje inloggning. Endast använda Kommandotolken = medgivande om du har fastställt att du behöver fråga efter medgivande för ytterligare behörighet (t.ex, om du har ändrat behörigheter som krävs för din app). |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Om tillämpligt, kan du utöka ditt program med användardata. Använd den [Microsoft Graph API](https://developer.microsoft.com/graph) är ett enkelt sätt att göra detta. Den [Graph-testaren](https://developer.microsoft.com/graph/graph-explorer) verktyg som kan hjälpa dig att komma igång. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrera den fullständiga uppsättningen behörigheter som din app kräver så att administratörer kan bevilja medgivande enkelt till deras klient. Använd [inkrementella medgivande](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) vid körning för att hjälpa användarna att förstå varför appen begär behörigheter som kan avse eller förvirrande för användare när efterfrågas vid den första starten. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementera en [Rensa enkel utloggning upplevelse](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Det är en sekretess- och en säkerhetskrav och ger en bra användarupplevelse. |

### <a name="testing"></a>Testning

|   |   |
|---|---|
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Testa [principer för villkorlig åtkomst](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) som kan påverka användarnas möjlighet att använda programmet. |
| ![Kryssrutan](./media/active-directory-integration-checklist/checkbox-two.svg) | Testa ditt program med alla konton som du planerar att stödja (till exempel, arbete eller skola konton, personliga Microsoft-konton, barnkonton och nationella konton). |

## <a name="additional-resources"></a>Ytterligare resurser

Utforska detaljerad information om v2.0:

* [Microsoft identity-plattformen (v2.0 översikt)](v2-overview.md)
* [Referens för Microsoft identity-plattformen protokoll](active-directory-v2-protocols.md)
* [Referens för åtkomsttoken](access-tokens.md)
* [Referens för ID-token](id-tokens.md)
* [Referera till autentiseringsbibliotek](reference-v2-libraries.md)
* [Behörigheter och godkännande i Microsoft identity-plattformen](v2-permissions-and-consent.md)
* [Microsoft Graph-API](https://developer.microsoft.com/graph)