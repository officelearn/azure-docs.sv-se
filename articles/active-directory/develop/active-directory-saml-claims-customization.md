---
title: Anpassa SAML-anspråk för Azure AD-app
titleSuffix: Microsoft identity platform
description: Lär dig hur du anpassar anspråk som utfärdas i SAML-token för företagsprogram i Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 87a9632ec2433b8698e3ae3761ba733aa6bc63a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885692"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Så här anpassar du anspråk som utfärdats i SAML-token för företagsprogram

Idag stöder Azure Active Directory (Azure AD) enkel inloggning (SSO) med de flesta företagsprogram, inklusive båda programmen förintegrerade i Azure AD-appgalleriet samt anpassade program. När en användare autentiserar till ett program via Azure AD med SAML 2.0-protokollet skickar Azure AD en token till programmet (via ett HTTP POST). Och sedan validerar och använder programmet token för att logga in användaren i stället för att fråga efter ett användarnamn och lösenord. Dessa SAML-token innehåller information om användaren som kallas *anspråk*.

Ett *anspråk* är information som en identitetsprovider anger om en användare i den token de utfärdar för den användaren. I [SAML-token](https://en.wikipedia.org/wiki/SAML_2.0)finns dessa data vanligtvis i SAML-attributsatsen. Användarens unika ID representeras vanligtvis i SAML-ämnet som också kallas namnidentifierare.

Som standard utfärdar Azure AD en SAML-token `NameIdentifier` till ditt program som innehåller ett anspråk med värdet av användarens användarnamn (kallas även användarens huvudnamn) i Azure AD, som unikt kan identifiera användaren. SAML-token innehåller också ytterligare anspråk som innehåller användarens e-postadress, förnamn och efternamn.

Om du vill visa eller redigera anspråk som utfärdats i SAML-token till programmet öppnar du programmet i Azure-portalen. Öppna sedan avsnittet **Användarattribut & anspråk.**

![Öppna avsnittet Användarattribut & anspråk i Azure-portalen](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

* Programmet kräver `NameIdentifier` att nameID-anspråket eller nameID ska vara något annat än användarnamnet (eller användarens huvudnamn) som lagras i Azure AD.
* Programmet har skrivits för att kräva en annan uppsättning anspråks-URI:er eller anspråksvärden.

## <a name="editing-nameid"></a>Redigera nameID

Så här redigerar du NameID (namnidentifieringsvärde):

1. Öppna sidan **Namnidentifieringsvärde.**
1. Markera det attribut eller den omvandling som du vill använda på attributet. Du kan också ange vilket format du vill att NameID-anspråket ska ha.

   ![Redigera värdet NameID (namnidentifierare)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-format

Om SAML-begäran innehåller elementet NameIDPolicy med ett visst format, kommer Azure AD att uppfylla formatet i begäran.

Om SAML-begäran inte innehåller ett element för NameIDPolicy utfärdar Azure AD NameID med det format du anger. Om inget format har angetts kommer Azure AD att använda standardkällans format som är associerat med den valda anspråkskällan.

I listrutan **Välj namnidentifierare** kan du välja ett av följande alternativ.

| NameID-format | Beskrivning |
|---------------|-------------|
| **Default** | Azure AD använder standardkällans format. |
| **Permanent** | Azure AD använder Beständigt som NameID-format. |
| **Emailaddress** | Azure AD använder EmailAddress som NameID-format. |
| **Ospecificerat** | Azure AD använder Ospecificerat som NameID-format. |
| **Kvalificerat namn på Windows-domän** | Azure AD använder WindowsDomainQualifiedName som NameID-format. |

Transient NameID stöds också, men är inte tillgängligt i listrutan och kan inte konfigureras på Azures sida. Mer information om attributet NameIDPolicy finns i [SAML-protokollet För enkel inloggning](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribut

Välj önskad källa `NameIdentifier` för anspråket (eller NameID). Du kan välja bland följande alternativ.

| Namn | Beskrivning |
|------|-------------|
| E-post | Användarens e-postadress |
| userprincipalName | Användarens användarnamn (UPN) |
| onpremisessamaccount | SAM-kontonamn som har synkroniserats från lokala Azure AD |
| Objectid | Objectid för användaren i Azure AD |
| employeeid | Användarens medarbetar-ID |
| Katalogtillägg | Katalogtillägg [synkroniserade från lokala Active Directory med Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Tilläggsattribut 1-15 | Lokala tilläggsattribut som används för att utöka Azure AD-schemat |

Mer information finns i [tabell 3: Giltiga ID-värden per källa](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Du kan också tilldela alla konstanta (statiska) värden till alla anspråk som du definierar i Azure AD. Följ stegen nedan för att tilldela ett konstant värde:

1. I [Azure-portalen](https://portal.azure.com/)klickar du på ikonen **Redigera** i avsnittet **Användarattribut & anspråk.**

1. Klicka på det anspråk som krävs som du vill ändra.

1. Ange konstantvärdet utan citattecken i **attributet Source** enligt organisationen och klicka på **Spara**.

    ![Öppna avsnittet Användarattribut & anspråk i Azure-portalen](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Konstantvärdet visas enligt nedan.

    ![Öppna avsnittet Användarattribut & anspråk i Azure-portalen](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Särskilda anspråk - transformationer

Du kan också använda anspråksomvandlingsfunktionerna.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffixet från e-postadressen eller användarens huvudnamn. Detta extraherar bara den första delen av användarnamnet som skickas igenom (till joe_smith@contoso.comexempel "joe_smith" i stället för ). |
| **Gå med()** | Ansluter till ett attribut med en verifierad domän. Om det valda användaridentifieringsvärdet har en domän extraheras användarnamnet för att lägga till den valda verifierade domänen. Om du till exempel väljerjoe_smith@contoso.come-postmeddelandet ( ) som användaridentifierare och väljer contoso.onmicrosoft.com joe_smith@contoso.onmicrosoft.comsom verifierad domän, resulterar detta i . |
| **ToLower()** | Konverterar tecknen i det markerade attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det markerade attributet till versaler. |

## <a name="adding-application-specific-claims"></a>Lägga till programspecifika anspråk

Så här lägger du till programspecifika anspråk:

1. I **Användarattribut & anspråk**väljer du Lägg till ett nytt **anspråk** för att öppna sidan **Hantera användarens anspråk.**
1. Ange **namnet** på anspråken. Värdet behöver inte strikt följa ett URI-mönster, enligt SAML-specen. Om du behöver ett URI-mönster kan du placera det i fältet **Namnområde.**
1. Välj den **källa** där anspråket ska hämta dess värde. Du kan välja ett användarattribut i listrutan källattribut eller tillämpa en omvandling på användarattributet innan du avger det som ett anspråk.

### <a name="claim-transformations"></a>Anspråk omvandlingar

Så här tillämpar du en omvandling på ett användarattribut:

1. I **Hantera anspråk**väljer du *Omvandling* som anspråkskälla för att öppna sidan **Hantera omvandling.**
2. Välj funktionen i listrutan för omvandling. Beroende på vilken funktion som valts måste du ange parametrar och ett konstant värde för att utvärdera i omformningen. Se tabellen nedan för mer information om tillgängliga funktioner.
3. Om du vill använda flera omvandlingar klickar du på **Lägg till omvandling**. Du kan använda högst två omvandlingar på ett anspråk. Du kan till exempel först extrahera e-postprefixet för `user.mail`. Gör sedan strängens versaler.

   ![Redigera värdet NameID (namnidentifierare)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Du kan använda följande funktioner för att omvandla anspråk.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffixet från e-postadressen eller användarens huvudnamn. Detta extraherar bara den första delen av användarnamnet som skickas igenom (till joe_smith@contoso.comexempel "joe_smith" i stället för ). |
| **Gå med()** | Skapar ett nytt värde genom att sammanfoga två attribut. Du kan också använda en avgränsare mellan de två attributen. För Transformering av NameID-anspråk är kopplingen begränsad till en verifierad domän. Om det valda användaridentifieringsvärdet har en domän extraheras användarnamnet för att lägga till den valda verifierade domänen. Om du till exempel väljerjoe_smith@contoso.come-postmeddelandet ( ) som användaridentifierare och väljer contoso.onmicrosoft.com joe_smith@contoso.onmicrosoft.comsom verifierad domän, resulterar detta i . |
| **ToLower()** | Konverterar tecknen i det markerade attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det markerade attributet till versaler. |
| **Innehåller()** | Matar ut ett attribut eller en konstant om indata matchar det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill avge ett anspråk där värdet är användarens e-postadress om den innehåller domänen "@contoso.com", annars vill du mata ut användarens huvudnamn. För att göra detta skulle du konfigurera följande värden:<br/>*Parameter 1(indata):* user.email<br/>*Värde*:@contoso.com" "<br/>Parameter 2 (utdata): user.email<br/>Parameter 3 (utdata om det inte finns någon matchning): user.userprincipalname |
| **Slutmed()** | Matar ut ett attribut eller en konstant om indata slutar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill avge ett anspråk där värdet är användarens medarbetar-ID om medarbetar-ID:t slutar med "000", annars vill du mata ut ett tilläggsattribut. För att göra detta skulle du konfigurera följande värden:<br/>*Parameter 1(indata):* user.employeeid<br/>*Värde*: "000"<br/>Parameter 2 (utdata): user.employeeid<br/>Parameter 3 (utdata om det inte finns någon matchning): user.extensionattribute1 |
| **Startmed()** | Matar ut ett attribut eller en konstant om indata börjar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill avge ett anspråk där värdet är användarens medarbetar-ID om landet/regionen börjar med "USA", annars vill du mata ut ett tilläggsattribut. För att göra detta skulle du konfigurera följande värden:<br/>*Parameter 1(indata):* user.country<br/>*Värde*: "USA"<br/>Parameter 2 (utdata): user.employeeid<br/>Parameter 3 (utdata om det inte finns någon matchning): user.extensionattribute1 |
| **Utdrag() - Efter matchning** | Returnerar delsträngen efter att den matchar det angivna värdet.<br/>Om indatavärdet till exempel är "Finance_BSimon", är det matchande värdet "Finance_", är anspråkets utdata "BSimon". |
| **Utdrag() - Före matchning** | Returnerar delsträngen tills den matchar det angivna värdet.<br/>Om indatavärdet till exempel är "BSimon_US", är det matchande värdet "_US", är anspråkets utdata "BSimon". |
| **Utdrag() - Mellan matchning** | Returnerar delsträngen tills den matchar det angivna värdet.<br/>Om indatas värde till exempel är "Finance_BSimon_US", är det första matchande värdet "Finance_", det andra matchande värdet "_US", då är anspråkets utdata "BSimon". |
| **ExtractAlpha() - Prefix** | Returnerar prefixet alfabetisk del av strängen.<br/>Om indatavärdet till exempel är "BSimon_123" returneras "BSimon". |
| **ExtractAlpha() - Suffix** | Returnerar suffixet alfabetisk del av strängen.<br/>Om indatavärdet till exempel är "123_Simon" returneras "Simon". |
| **UtdragNumeric() - Prefix** | Returnerar prefixets numeriska del av strängen.<br/>Om indatavärdet till exempel är "123_BSimon" returneras "123". |
| **UtdragNumeric() - Suffix** | Returnerar suffixet numerisk del av strängen.<br/>Om indatavärdet till exempel är "BSimon_123" returneras "123". |
| **IfEmpty()** | Matar ut ett attribut eller en konstant om indata är null eller tom.<br/>Om du till exempel vill mata ut ett attribut som lagras i en tilläggsattribut om medarbetar-ID:t för en viss användare är tomt. För att göra detta skulle du konfigurera följande värden:<br/>Parameter 1(indata): user.employeeid<br/>Parameter 2 (utdata): user.extensionattribute1<br/>Parameter 3 (utdata om det inte finns någon matchning): user.employeeid |
| **IfNotEmpty()** | Matar ut ett attribut eller en konstant om indata inte är null eller tom.<br/>Om du till exempel vill mata ut ett attribut som lagras i en tilläggsattribut om medarbetar-ID:t för en viss användare inte är tomt. För att göra detta skulle du konfigurera följande värden:<br/>Parameter 1(indata): user.employeeid<br/>Parameter 2 (utdata): user.extensionattribute1 |

Om du behöver ytterligare omvandlingar skickar du in din idé i [feedbackforumet i Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) under kategorin *SaaS-program.*

## <a name="emitting-claims-based-on-conditions"></a>Avge anspråk på grundval av villkor

Du kan ange källan till ett anspråk baserat på användartyp och den grupp som användaren tillhör. 

Användartypen kan vara:
- **Alla:** Alla användare har rätt att komma åt programmet.
- **Medlemmar**: Native medlem i hyresgästen
- **Alla gäster**: Användaren kommer över från en extern organisation med eller utan Azure AD.
- **AAD-gäster:** Gästanvändare tillhör en annan organisation med Azure AD.
- **Externa gäster**: Gästanvändaren tillhör en extern organisation som inte har Azure AD.


Ett scenario där detta är användbart är när källan till en fordran är annorlunda för en gäst och en anställd som använder ett program. Du kanske vill ange att om användaren är anställd kommer NameID från user.email, men om användaren är gäst kommer NameID från user.extensionattribute1.

Så här lägger du till ett anspråksvillkor:

1. Expandera anspråksvillkoren i **Hantera anspråk.**
2. Välj användartyp.
3. Välj den grupp som användaren ska tillhöra. Du kan välja upp till 10 unika grupper i alla anspråk för ett visst program. 
4. Välj den **källa** där anspråket ska hämta dess värde. Du kan välja ett användarattribut i listrutan källattribut eller tillämpa en omvandling på användarattributet innan du avger det som ett anspråk.

I vilken ordning du lägger till villkoren är viktiga. Azure AD utvärderar villkoren uppifrån och ned för att bestämma vilket värde som ska avges i anspråket. 

Brita Simon är till exempel gästanvändare i Contoso-klienten. Hon tillhör en annan organisation som också använder Azure AD. Med tanke på nedanstående konfiguration för Fabrikam-programmet, när Brita försöker logga in på Fabrikam, utvärderar Azure AD villkoren enligt följande.

Först verifierar Azure AD om Britas `All guests`användartyp är . Sedan dess är detta sant då Azure AD `user.extensionattribute1`tilldelar källan för anspråket till . För det andra verifierar Azure AD om `AAD guests`Britas användartyp är , eftersom detta också `user.mail`är sant så tilldelar Azure AD källan för anspråket till . Slutligen avges påståendet med `user.email` värde för Brita.

![Villkorskonfiguration för anspråk](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Nästa steg

* [Programhantering i Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurera enstaka inloggningsprogram som inte finns i Azure AD-programgalleriet](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
