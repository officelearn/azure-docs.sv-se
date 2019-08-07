---
title: Anpassa anspråk för SAML-token för företags program i Azure AD | Microsoft Docs
description: Lär dig hur du anpassar anspråk som utfärdats i SAML-token för företags program i Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0625a233b3b4a949feff2e289361a26fc8dc5a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835353"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Anvisningar: Anpassa anspråk som utfärdats i SAML-token för företags program

Idag har Azure Active Directory (Azure AD) stöd för enkel inloggning (SSO) med de flesta företags program, inklusive båda program som är förintegrerade i Azure AD App-galleriet samt anpassade program. När en användare autentiserar till ett program via Azure AD med hjälp av SAML 2,0-protokollet skickar Azure AD en token till programmet (via ett HTTP-inlägg). Därefter verifierar och använder appen token för att logga användaren i stället för att begära ett användar namn och lösen ord. Dessa SAML-tokens innehåller delar av information om den användare som kallas för *anspråk*.

Ett *anspråk* är information som en identitets leverantör anger för en användare inom den token som de utfärdar för den användaren. I [SAML](https://en.wikipedia.org/wiki/SAML_2.0)-token finns dessa data vanligt vis i instruktionen SAML Attribute. Användarens unika ID visas vanligt vis i SAML-ämnet även kallat namn identifierare.

Som standard utfärdar Azure AD en SAML-token till ditt program som innehåller `NameIdentifier` ett anspråk med ett värde för användarens användar namn (även kallat User Principal Name) i Azure AD, vilket kan identifiera användaren unikt. SAML-token innehåller också ytterligare anspråk som innehåller användarens e-postadress, förnamn och efter namn.

Om du vill visa eller redigera de anspråk som utfärdats i SAML-token till programmet öppnar du programmet i Azure Portal. Öppna sedan avsnittet **användarattribut &-anspråk** .

![Öppna avsnittet användarattribut &-anspråk i Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

* Programmet kräver `NameIdentifier` att NameID-anspråk är något annat än det användar namn (eller User Principal Name) som lagras i Azure AD.
* Programmet har skrivits för att kräva en annan uppsättning av anspråk-URI: er eller anspråks värden.

## <a name="editing-nameid"></a>Redigera NameID

Så här redigerar du NameID (namn-ID-värde):

1. Öppna sidan **namn på identifierat värde** .
1. Välj det attribut eller den omvandling som du vill använda för attributet. Alternativt kan du ange det format som du vill att NameID-anspråket ska ha.

   ![Redigera värdet för NameID (namn identifierare)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-format

Om SAML-begäran innehåller elementet NameIDPolicy med ett särskilt format, kommer Azure AD att respektera formatet i begäran.

Om SAML-begäran inte innehåller ett element för NameIDPolicy utfärdar Azure AD NameID med det format som du anger. Om inget format har angetts använder Azure AD standard käll formatet som är associerat med den valda anspråks källan.

I list rutan **Välj namn identifierare format** kan du välja något av följande alternativ.

| NameID-format | Beskrivning |
|---------------|-------------|
| **Standard** | Standardvärdet för käll formatet används i Azure AD. |
| **Bestående** | Azure AD kommer att använda beständigt som NameID-format. |
| **E-postadress** | Azure AD kommer att använda EmailAddress som NameID-format. |
| **Ospecificerad** | Azure AD kommer att använda ospecificerat som NameID-format. |
| **Tillfälliga** | Azure AD kommer att använda tillfällig som NameID-format. |

Mer information om attributet NameIDPolicy finns i [SAML-protokoll för enkel inloggning](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribut

Välj önskad källa för `NameIdentifier` (eller NameID)-anspråket. Du kan välja bland följande alternativ.

| Namn | Beskrivning |
|------|-------------|
| Email | Användarens e-postadress |
| userprincipalName | Användarens huvud namn (UPN) |
| onpremisessamaccount | SAM-kontonamn som har synkroniserats från den lokala Azure AD |
| objectID | objectID för användaren i Azure AD |
| EmployeeID | användarens Anställningsnr |
| Katalogtillägg | Katalog tillägg som har [synkroniserats från lokala Active Directory med Azure AD Connect synkronisering](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attribut för tillägg 1-15 | Attribut för lokala tillägg som används för att utöka Azure AD-schemat |

Mer information finns i [tabell 3: Giltiga ID-värden per](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)källa.

### <a name="special-claims---transformations"></a>Särskilda anspråk – transformeringar

Du kan också använda funktionerna för anspråks omvandlingar.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffix från antingen e-postadressen eller User Principal Name. Detta extraherar bara den första delen av användar namnet som skickas genom (till exempel "joe_smith" i stället för joe_smith@contoso.com). |
| **Anslut ()** | Ansluter till ett attribut med en verifierad domän. Om värdet för den valda användar identifieraren har en domän extraheras användar namnet för att lägga till den valda verifierade domänen. Om du till exempel väljer e-postmeddelandet (joe_smith@contoso.com) som värde för användar-ID och väljer contoso.onmicrosoft.com som verifierade domän kommer detta att resultera i. joe_smith@contoso.onmicrosoft.com |
| **ToLower()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det valda attributet till versaler. |

## <a name="adding-application-specific-claims"></a>Lägga till programspecifika anspråk

Så här lägger du till programspecifika anspråk:

1. I användarattribut **& anspråk**väljer du **Lägg till nytt anspråk** för att öppna sidan **hantera användar anspråk** .
1. Ange anspråkets **namn** . Värdet behöver inte absolut följa ett URI-mönster, enligt SAML-specifikationen. Om du behöver ett URI-mönster kan du ange det i fältet **namn område** .
1. Välj **källan** där anspråket ska hämta sitt värde. Du kan välja ett användarattribut från List rutan källattribut eller tillämpa en omvandling till användarattribut innan du avger det som ett anspråk.

### <a name="application-specific-claims---transformations"></a>Programspecifikt anspråk-transformeringar

Du kan också använda funktionerna för anspråks omvandlingar.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffix från antingen e-postadressen eller User Principal Name. Detta extraherar bara den första delen av användar namnet som skickas genom (till exempel "joe_smith" i stället för joe_smith@contoso.com). |
| **Anslut ()** | Skapar ett nytt värde genom att koppla ihop två attribut. Du kan också använda en avgränsare mellan de två attributen. |
| **ToLower()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det valda attributet till versaler. |
| **Contains ()** | Matar ut ett attribut eller en konstant om indata matchar det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens e-postadress om den innehåller domänen "@contoso.com", annars vill du spara User Principal Name. Det gör du genom att konfigurera följande värden:<br/>*Parameter 1 (indata)* : User. email<br/>*Värde*: "@contoso.com"<br/>Parameter 2 (utdata): User. email<br/>Parameter 3 (utdata om det inte finns någon matchning): User. UserPrincipalName |
| **EndWith()** | Matar ut ett attribut eller en konstant om indata slutar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens Anställningsnr om Anställningsnr slutar med "000", annars vill du spara ett attribut för tillägg. Det gör du genom att konfigurera följande värden:<br/>*Parameter 1 (indata)* : User. Anställningsnr<br/>*Värde*: "000"<br/>Parameter 2 (utdata): User. Anställningsnr<br/>Parameter 3 (utdata om det inte finns någon matchning): User. extensionAttribute1 |
| **StartWith()** | Matar ut ett attribut eller en konstant om indatan börjar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens Anställningsnr om landet/regionen börjar med "US", annars vill du spara ett attribut för tillägg. Det gör du genom att konfigurera följande värden:<br/>*Parameter 1 (indata)* : användare. land<br/>*Värde*: USA<br/>Parameter 2 (utdata): User. Anställningsnr<br/>Parameter 3 (utdata om det inte finns någon matchning): User. extensionAttribute1 |
| **Extrahera ()-efter matchning** | Returnerar del strängen när den matchar det angivna värdet.<br/>Om exempelvis indatans värde är "Finance_BSimon" är det matchande värdet "Finance_", och anspråkets utdata är "BSimon". |
| **Extrahera ()-före matchning** | Returnerar del strängen tills den matchar det angivna värdet.<br/>Om exempelvis indatans värde är "BSimon_US" är det matchande värdet "_US", och anspråkets utdata är "BSimon". |
| **Extrahera () – mellan matchning** | Returnerar del strängen tills den matchar det angivna värdet.<br/>Exempel: om indatans värde är "Finance_BSimon_US" är det första matchande värdet "Finance_", det andra matchning svärdet är "_US", och anspråkets utdata är "BSimon". |
| **ExtractAlpha ()-prefix** | Returnerar den alfabetiska delen av strängen.<br/>Om exempelvis värdet för invärdet är "BSimon_123" returnerar det "BSimon". |
| **ExtractAlpha() - Suffix** | Returnerar den alfabetiska delen för suffixet i strängen.<br/>Om värdet till exempel är "123_Simon" returneras "Simon". |
| **ExtractNumeric ()-prefix** | Returnerar prefixets numeriska del av strängen.<br/>Om värdet till exempel är "123_BSimon" returneras "123". |
| **ExtractNumeric ()-suffix** | Returnerar suffixets numeriska del av strängen.<br/>Om värdet till exempel är "BSimon_123" returneras "123". |
| **IfEmpty()** | Matar ut ett attribut eller en konstant om indata är null eller tomt.<br/>Om du till exempel vill mata ut ett attribut som lagras i en extensionattribute om Anställningsnr för en specifik användare är tomt. Det gör du genom att konfigurera följande värden:<br/>Parameter 1 (indata): User. Anställningsnr<br/>Parameter 2 (utdata): User. extensionAttribute1<br/>Parameter 3 (utdata om det inte finns någon matchning): User. Anställningsnr |
| **IfNotEmpty()** | Matar ut ett attribut eller en konstant om indata inte är null eller tomma.<br/>Om du till exempel vill mata ut ett attribut som lagras i en extensionattribute om Anställningsnr för en specifik användare inte är tomt. Det gör du genom att konfigurera följande värden:<br/>Parameter 1 (indata): User. Anställningsnr<br/>Parameter 2 (utdata): User. extensionAttribute1 |

Om du behöver ytterligare transformeringar skickar du din idé i feedback- [forumet i Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) under kategorin *SaaS-program* .

## <a name="next-steps"></a>Nästa steg

* [Program hantering i Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurera enkel inloggning för program som inte finns i Azure AD-programgalleriet](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](howto-v1-debug-saml-sso-issues.md)
