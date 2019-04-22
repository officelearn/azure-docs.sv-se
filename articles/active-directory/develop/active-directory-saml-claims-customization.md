---
title: Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure AD | Microsoft Docs
description: Lär dig hur du anpassar de anspråk som utfärdats i SAML-token för företagsprogram i Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: celested
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6fe74852824c10d24729f785e5e33a17b793161
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878578"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Så här: Anpassa anspråk som utfärdats i SAML-token för företagsprogram

Idag, Azure Active Directory (Azure AD) har stöd för enkel inloggning (SSO) med de flesta företagsprogram, inklusive båda programmen som är förintegrerade i appgalleriet för Azure AD samt anpassade program. När en användare autentiseras till ett program via Azure AD med hjälp av protokollet SAML 2.0, skickar Azure AD en token till programmet (via en HTTP-POST). Och sedan programmet validerar och använder token för att logga in användaren i stället för att fråga om ett användarnamn och lösenord. Dessa SAML-token innehåller uppgifter om användaren kallas *anspråk*.

En *anspråk* är information som en identitetsprovider som anger om en användare i de token som de utfärda för användaren. I [SAML-token](https://en.wikipedia.org/wiki/SAML_2.0), dessa data finns vanligtvis i instruktionen SAML-attribut. Användarens unika ID: T är vanligtvis representeras i SAML-ämne som även kallas för namnidentifierare.

Som standard Azure AD utfärdar en SAML-token i ditt program som innehåller en `NameIdentifier` anspråk med ett värde av användarens användarnamn (även kallat user principal name) i Azure AD, som kan identifiera användaren. SAML-token innehåller också ytterligare anspråk som innehåller användarens e-postadress, Förnamn och efternamn.

Om du vill visa eller redigera de anspråk som utfärdats i SAML-token till programmet genom att öppna programmet i Azure-portalen. Öppna sedan den **användarattribut och anspråk** avsnittet.

![Avsnittet användarattribut och anspråk](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

* Programmet kräver den `NameIdentifier` eller NameID påstår sig vara något annat än användarnamnet (eller användarens huvudnamn) lagras i Azure AD.
* Programmet har skrivits för att kräva en annan uppsättning anspråk URI: er eller anspråksvärden.

## <a name="editing-nameid"></a>Redigera NameID

Redigera NameID (namnet ID-värde):

1. Öppna den **namnge identifierarvärde** sidan.
1. Välj attribut- eller omvandlingsåtgärd som du vill använda för attributet. Alternativt kan du ange det önskade formatet han NameID-anspråket har.

   ![Redigera värdet NameID (namnidentifierare)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-format

Om SAML-begäran innehåller NameIDPolicy-element med ett visst format, respekterar format i Azure AD.

Om SAML-begäran inte innehåller ett element för NameIDPolicy kan sedan utfärdar Azure AD NameID med det format som du anger. Om inget format har angetts för Azure AD ska använda källa standardformatet som är associerade med anspråkskällan som valts.

Från den **Välj format för namn på identifierare** listrutan kan du välja något av följande alternativ.

| NameID-format | Beskrivning |
|---------------|-------------|
| **Standard** | Azure AD ska använda standardformatet källa. |
| **Beständiga** | Azure AD använder beständiga som NameID-format. |
| **E-postadress** | Azure AD använder e-postadress som NameID-format. |
| **Ospecificerad** | Azure AD använder Ospecificerad som NameID-format. |
| **Transient** | Azure AD använder tillfälligt som NameID-format. |

Läs mer om attributet NameIDPolicy i [enkel inloggning SAML-protokoll](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribut

Välj önskad källa för den `NameIdentifier` (eller NameID) anspråk. Du kan välja bland följande alternativ.

| Namn | Beskrivning |
|------|-------------|
| E-post | E-postadressen för användaren |
| userprincipalName | Användarens huvudnamn (UPN) för användaren |
| onpremisessamaccount | SAM-kontonamn som har synkroniserats från den lokala Azure AD |
| objekt-ID | objekt-ID för användaren i Azure AD |
| EmployeeID | EmployeeID för användaren |
| Katalogtillägg | Katalogtillägg [synkroniseras från den lokala Active Directory med Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Tilläggsattribut 1 – 15 | Lokala tilläggsattribut som används för att utöka Azure AD-schema |

Mer information finns i [tabell 3: Giltiga värden för ID per källa](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Särskilda anspråk - transformeringar

Du kan också använda funktionerna anspråk transformationer.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffix från e-postadressen eller användarens huvudnamn. Då extraheras bara den första delen av användarnamnet som skickas via (till exempel ”joe_smith” i stället för joe_smith@contoso.com). |
| **Join()** | Ansluter till ett attribut med en verifierad domän. Om det valda användaren ID-värdet har en domän, extraherar användarnamnet om du vill lägga till valda verifierade domän. Exempel: Om du väljer e-postmeddelandet (joe_smith@contoso.com) som värde för användaridentifierare och väljer contoso.onmicrosoft.com som verifierad domän, detta resulterar i joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det valda attributet till versaler. |

## <a name="adding-application-specific-claims"></a>Att lägga till programspecifika anspråk

Lägg till programspecifika anspråk:

1. I **användarattribut och anspråk**väljer **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** sidan.
1. Ange den **namn** av anspråk. Värdet behöver inte strikt följer ett mönster för URI, per SAML-specifikationen. Om du behöver ett URI-mönster kan du placera som i den **Namespace** fält.
1. Välj den **källa** var anspråket ska hämta dess värde. Du kan välja ett användarattribut i listrutan på attributet källa eller tillämpa en omvandling till användarattributet innan de som ett anspråk.

### <a name="application-specific-claims---transformations"></a>Programspecifika anspråk - transformeringar

Du kan också använda funktionerna anspråk transformationer.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffix från e-postadressen eller användarens huvudnamn. Då extraheras bara den första delen av användarnamnet som skickas via (till exempel ”joe_smith” i stället för joe_smith@contoso.com). |
| **Join()** | Skapar ett nytt värde genom att gå med två attribut. Du kan även använda en avgränsare mellan de två attributen. |
| **ToLower()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det valda attributet till versaler. |
| **Contains()** | Matar ut ett attribut eller konstant om indata matchar det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Exempel: Om du vill generera ett anspråk där värdet är användarens e-postadress om den innehåller domänen ”@contoso.com”, annars du vill spara användarens huvudnamn. Om du vill göra detta måste konfigurerar du följande värden:<br/>*Parametern 1(input)*: user.email<br/>*Värdet*”:@contoso.com”<br/>Parameter 2 (utdata): user.email<br/>Parameter 3 (utdata om det inte finns någon matchning): user.userprincipalname |
| **EndWith()** | Matar ut ett attribut eller konstant om indata som slutar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Till exempel om du vill generera ett anspråk där värdet är användarens employeeid om employeeid slutar med ”000”, du annars att mata ut ett tilläggsattribut. Om du vill göra detta måste konfigurerar du följande värden:<br/>*Parametern 1(input)*: user.employeeid<br/>*Värde*: "000"<br/>Parameter 2 (utdata): user.employeeid<br/>Parameter 3 (utdata om det inte finns någon matchning): user.extensionattribute1 |
| **StartWith()** | Matar ut ett attribut eller konstant om indata som börjar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Till exempel om du vill generera ett anspråk där värdet är användarens employeeid om landet som börjar med ”US”, du annars att mata ut ett tilläggsattribut. Om du vill göra detta måste konfigurerar du följande värden:<br/>*Parametern 1(input)*: User.Country.<br/>*Värde*: ”OSS”<br/>Parameter 2 (utdata): user.employeeid<br/>Parameter 3 (utdata om det inte finns någon matchning): user.extensionattribute1 |
| **Extract() - efter matchande** | Returnerar delsträngen efter att det matchar det angivna värdet.<br/>Till exempel om den inkommande värdet är ”Finance_BSimon”, det matchande värdet är ”Finance_” och sedan anspråkets utdata är ”BSimon”. |
| **Extract() - innan matchande** | Returnerar delsträngen tills det matchar det angivna värdet.<br/>Till exempel om den inkommande värdet är ”BSimon_US”, det matchande värdet är ”_US” och sedan anspråkets utdata är ”BSimon”. |
| **Extract() - mellan matchande** | Returnerar delsträngen tills det matchar det angivna värdet.<br/>Till exempel den indata-värdet är ”Finance_BSimon_US” är det första matchande värdet är ”Finance_” andra matchande värdet är ”_US”, sedan anspråkets utdata är ”BSimon”. |
| **ExtractAlpha() - Prefix** | Returnerar prefix alfabetisk delen av strängen.<br/>Till exempel om den inkommande värdet är ”BSimon_123”, returnerar sedan den ”BSimon”. |
| **ExtractAlpha() - Suffix** | Returnerar suffix alfabetisk delen av strängen.<br/>Till exempel om den inkommande värdet är ”123_Simon”, returnerar sedan den ”BSimon”. |
| **ExtractNumeric() - Prefix** | Returnerar prefix numeriska delen av strängen.<br/>Till exempel om den inkommande värdet är ”123_BSimon”, returneras sedan ”123”. |
| **ExtractNumeric() - Suffix** | Returnerar suffix numeriska delen av strängen.<br/>Till exempel om den inkommande värdet är ”BSimon_123”, returneras sedan ”123”. |
| **IfEmpty()** | Matar ut ett attribut eller konstant om indata är null eller tomt.<br/>Exempel: Om du vill spara ett attribut som lagras i en extensionattribute om employeeid för en viss användare är tom. Om du vill göra detta måste konfigurerar du följande värden:<br/>Parametern 1(input): user.employeeid<br/>Parameter 2 (utdata): user.extensionattribute1<br/>Parameter 3 (utdata om det inte finns någon matchning): user.employeeid |
| **IfNotEmpty()** | Matar ut ett attribut eller konstant om indata inte är null eller tomt.<br/>Exempel: Om du vill spara ett attribut som lagras i en extensionattribute om employeeid för en viss användare inte är tom. Om du vill göra detta måste konfigurerar du följande värden:<br/>Parametern 1(input): user.employeeid<br/>Parameter 2 (utdata): user.extensionattribute1 |

Om du behöver ytterligare omvandlingar kan du skicka in din idé i den [i Azure AD-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) under den *SaaS-program* kategori.

## <a name="next-steps"></a>Nästa steg

* [Programhantering i Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurera enkel inloggning på program som inte ingår i Azure AD-programgalleriet](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](howto-v1-debug-saml-sso-issues.md)
