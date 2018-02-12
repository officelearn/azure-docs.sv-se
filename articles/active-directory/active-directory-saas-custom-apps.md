---
title: "Konfigurera Azure AD enkel inloggning för program | Microsoft Docs"
description: "Lär dig hur självbetjäning ansluta appar till Azure Active Directory med SAML och lösenordsbaserad enkel inloggning"
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2018
ms.author: asmalser
ms.reviewer: luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e161bb308f08e2a7c137c696e77bf1dfb86e8d31
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet
Den här artikeln handlar om en funktion som låter administratörer konfigurera enkel inloggning till program finns inte i appgalleriet för Azure Active Directory *utan att skriva kod*. Den här funktionen har frigjorts från technical preview 18 November 2015 och ingår i [Azure Active Directory Premium](active-directory-editions.md). Om du söker i stället för guide för utvecklare om hur du integrerar anpassade appar med Azure AD via kod, se [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md).

Azure Active Directory-programgalleriet innehåller en lista över program som stöder en form av enkel inloggning med Azure Active Directory, enligt beskrivningen i [i den här artikeln](active-directory-appssoaccess-whatis.md). När du (som en IT-specialist eller system integrator i din organisation) har hittat programmet som du vill ansluta till, kan du komma igång genom att följa de stegvisa anvisningarna som visas i Azure-portalen för att aktivera enkel inloggning.

Kunder med [Azure Active Directory Premium](active-directory-editions.md) licens också få dessa ytterligare funktioner:

* Självbetjäning integrering av alla program som stöder SAML 2.0 identitetsleverantörer (SP-initierad eller IdP-initierad)
* Självbetjäning integrering av alla webbprogram som har en HTML-baserad inloggningssidan med [lösenordsbaserade SSO](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Självbetjäning anslutning av program som använder protokollet SCIM för användaretablering ([beskrivs här](active-directory-scim-provisioning.md))
* Möjligheten att lägga till länkar till alla program i den [Office 365 app programstart](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Detta kan inkludera inte bara SaaS-program som du använder men har inte ännu har publicerat i Azure AD application Gallery utan från tredje part webbprogram som din organisation har distribuerats till servrar som du kontroll över, antingen i molnet eller lokalt.

Dessa funktioner, även kallat *app integration mallar*anger standardbaserad anslutningspunkter för appar som stöder SAML, SCIM eller formulärbaserad autentisering och inkludera flexibla alternativ och inställningar för kompatibilitet med ett stort antal program. 

## <a name="adding-an-unlisted-application"></a>Lägga till ett program som inte finns i listan
Logga in på Azure-portalen med ditt konto i Azure Active Directory-administratör för att ansluta ett program med en mall för integrering av appen, och bläddra till den **Active Directory > företagsprogram > Nytt program > Icke-galleriet programmet** väljer **Lägg till**, och sedan **lägga till ett program från galleriet**.

  ![][1]

I appgalleriet, du kan lägga till en ny app genom att välja den **icke-galleriet programmet** sida vid sida som visas i sökresultaten om appen önskade inte hittades. Du kan konfigurera alternativ för enkel inloggning och beteende när du har angett ett namn för ditt program. 

**Tips**: Använd sökfunktionen ett bra tips är för att kontrollera om programmet redan finns i programgalleriet. Om hitta appen och dess beskrivning nämns ”enkel inloggning”, stöds redan programmet för federerad enkel inloggning.

  ![][2]

Lägga till ett program som det här sättet ger en mycket lik upplevelse till den tillgängliga för redan integrerade program. Om du vill starta, Välj **Konfigurera enkel inloggning**. På nästa skärm anger följande tre alternativ för att konfigurera enkel inloggning, som beskrivs i följande avsnitt.

  ![][3]

## <a name="saml-based-sign-on"></a>SAML-baserade inloggning
Välj det här alternativet för att konfigurera SAML-baserad autentisering för programmet. Detta kräver att programmets support SAML 2.0, och du bör samla in information om hur du använder SAML-funktioner i programmet innan du fortsätter. När du har valt **nästa**, uppmanas du att ange tre olika URL: er för SAML-slutpunkter för programmet.

  ![][4]

Dessa är:

* **Logga URL (SP-initierad endast)** – där användaren går för att logga in på det här programmet. Om programmet har konfigurerats för att utföra service provider-initierad enkel inloggning, och sedan när användarna navigerar till den här URL: en, tjänstleverantör gör nödvändiga omdirigeringen med Azure AD för att autentisera och logga in användaren i. Om det här fältet fylls använder Azure AD sedan denna URL starta program från Office 365 och Azure AD-åtkomstpanelen. Om fältet utelämnas, så Azure AD i stället utför identitetsleverantör-initieras inloggning när appen startas från Office 365, Azure AD-åtkomstpanelen eller i Azure AD enkel inloggnings-URL (copyable från fliken instrumentpanel).
* **Utfärdar-URL** -utfärdar-URL ska identifiera programmet där enkel inloggning som ska konfigureras. Detta är det värde som Azure AD skickar tillbaka till programmet som den **målgruppen** parametern för SAML-token och programmet förväntas att verifiera den. Det här värdet visas också som den **enhets-ID** i eventuella SAML-metadata som tillhandahålls av programmet. Kontrollera programmets SAML dokumentation för information om dess värde med enhets-ID eller en publik är. Nedan visas ett exempel på hur URL: en målgrupp visas i SAML-token som returneras till programmet:
  
  ```
  <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
    ```

* **Reply URL** -reply-URL är där programmet förväntas ta emot SAML-token. Detta kallas även för som den **Assertion konsumenten Service (ACS) URL**. Kontrollera programmets SAML dokumentation för information om dess SAML-token reply URL eller ACS-URL är.
  När dessa har angetts, klickar du på **nästa** fortsätta till nästa sida. Den här skärmen innehåller information om vad som måste konfigureras på program på klientsidan att godkänna en SAML-token från Azure AD.

  * Klicka på **konfigurera Litware**.
  
    ![][5]

Vilka värden som krävs varierar beroende på programmet, så kontrollera programmets SAML-dokumentationen för mer information. Den **inloggning** och **utloggning** URL både motsvara samma slutpunkt, vilket är SAML hantering av begäran slutpunkten för din instans av Azure AD-tjänsten. Utfärdar-URL är det värde som visas som ”utfärdaren” i SAML-token utfärdas till programmet. 

När programmet har konfigurerats, klickar du på **nästa** knappen och sedan den **Slutför** att stänga dialogrutan. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Tilldela användare och grupper till SAML-program
När programmet har konfigurerats för att använda Azure AD som en SAML-baserade identitetsleverantör, är det nästan redo att testa. Azure AD utfärdar inte en token så att de kan logga in på programmet, om de har beviljats åtkomst med hjälp av Azure AD som en säkerhetskontroll. Användare kan beviljas åtkomst direkt eller via en grupp som är medlem i. 

Om du vill tilldela en användare eller grupp i programmet, klickar du på den **tilldela användare** knappen. Välj den användare eller grupp som du vill tilldela och välj sedan den **tilldela** knappen.

  ![][6]

Tilldela en användare kan Azure AD för att utfärda en token för användaren som orsakar en panel för det här programmet ska visas i användarens åtkomstpanelen. Ett program sida vid sida visas också i startprogrammet för Office 365 om användaren använder Office 365. 

Du kan ladda upp en ikonlogotyp för program med den **överföra logotypen** knappen på den **konfigurera** för programmet. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>Anpassa anspråk som utfärdats i SAML-token
När en användare autentiseras för utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller detta användarens användarnamn, e-postadress, Förnamn och efternamn. 

Du kan visa eller redigera anspråk skickas i SAML-token till programmet under den **attribut** fliken.

  ![][7]

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token: •mål program som har skrivits till kräver en annan uppsättning anspråk URI: er eller anspråksvärden •Your programmet har distribuerats på ett sätt som kräver NameIdentifier påstår sig vara något annat än användarnamnet (AKA användarens huvudnamn) lagras i Azure Active Directory. 

Mer information om hur du lägger till och redigera anspråk för dessa scenarier kolla detta [artikel om anspråk anpassning](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>Testa SAML-program
När SAML URL: er och certifikat har konfigurerats i Azure AD och programmet, användare eller grupper har tilldelats till programmet i Azure, och anspråk har granskat och redigera vid behov och sedan användaren är redo att logga in på programmet. 

Testa bara logga in på Azure AD-åtkomstpanelen på https://myapps.microsoft.com med ett användarkonto som du har tilldelat till programmet, och klicka sedan på ikonen för programmet att startar processen för enkel inloggning. Alternativt kan bläddra du direkt till Sign-On-URL för programmet och logga in därifrån. 

Felsöka tips finns i följande [artikel om hur du felsöker SAML-baserade enkel inloggning till program](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Lösenord för enkel inloggning
Välj det här alternativet för att konfigurera [lösenordsbaserade enkel inloggning](active-directory-appssoaccess-whatis.md) för ett webbprogram som har en HTML-inloggningssida. Lösenordsbaserade SSO, även kallat lösenord vaulting, kan du hantera åtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbara för situationer där flera användare behöva dela ett enda konto som din organisation sociala medier app konton. 

När du har valt **nästa**, du uppmanas att ange Webbadressen till programmets webbaserade inloggningssidan. Observera att detta måste vara den sida som innehåller indatafält användarnamn och lösenord. När du angett, startar Azure AD en process för att analysera inloggningssidan för att ange användarnamn och ett lösenord som indata. Om processen inte lyckas, sedan hjälper den dig att ett alternativ att installera ett webbläsartillägg (kräver Internet Explorer, Chrome eller Firefox) som gör att du kan avbilda fälten manuellt.

När inloggningssidan avbildas, användare och grupper kan tilldelas och autentiseringsuppgifter policys kan ställas in som vanlig [lösenord SSO appar](active-directory-appssoaccess-whatis.md).

Obs: Du kan ladda upp en ikonlogotyp för program med den **överföra logotypen** knappen på den **konfigurera** för programmet. 

## <a name="existing-single-sign-on"></a>Befintliga enkel inloggning
Välj det här alternativet om du vill lägga till en länk till ett program till din organisations Azure AD-åtkomstpanelen eller Office 365-portalen. Du kan använda detta att lägga till länkar till anpassade webbprogram som använder Azure Active Directory Federation Services (eller andra federationstjänsten) i stället för Azure AD för autentisering. Eller, du kan lägga till djuplänkar till specifika SharePoint-sidor eller andra webbsidor som bara ska visas på användarnas åtkomst paneler. 

När du har valt **nästa**, uppmanas du att ange URL för det program du länkar till. När slutfört, användare och grupper kan tilldelas till programmet, vilket gör att programmet ska visas i den [Office 365 app programstart](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) för dessa användare.

Obs: Du kan ladda upp en ikonlogotyp för program med den **överföra logotypen** knappen på den **konfigurera** för programmet.

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Anpassa anspråk som utfärdats i SAML-Token för förintegrerade appar](active-directory-saml-claims-customization.md)
* [Felsökning av SAML-baserade enkel inloggning](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png