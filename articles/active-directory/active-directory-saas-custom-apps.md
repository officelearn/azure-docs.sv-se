---
title: Konfigurera Azure AD enkel inloggning för program | Microsoft Docs
description: Lär dig hur självbetjäning ansluta appar till Azure Active Directory med SAML och lösenordsbaserad enkel inloggning
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
ms.openlocfilehash: 3c115414fc6ae599342192196e256597c28e5aa6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293202"
---
# <a name="configure-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet

Den här artikeln handlar om en funktion som låter administratörer konfigurera enkel inloggning till program finns inte i appgalleriet för Azure Active Directory *utan att skriva kod*. Den här funktionen har frigjorts från technical preview 18 November 2015 och ingår i [Azure Active Directory Premium](active-directory-whatis.md). Om du söker i stället för guide för utvecklare om hur du integrerar anpassade appar med Azure AD via kod, se [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md).

Azure Active Directory-programgalleriet innehåller en lista över program som stöder en form av enkel inloggning med Azure Active Directory, enligt beskrivningen i [i den här artikeln](manage-apps/what-is-single-sign-on.md). När du (som en IT-specialist eller system integrator i din organisation) har hittat programmet som du vill ansluta till, kan du komma igång genom att följa de stegvisa anvisningarna som visas i Azure-portalen för att aktivera enkel inloggning.

Kunder med [Azure Active Directory Premium](active-directory-whatis.md) licens också få dessa ytterligare funktioner:

* Självbetjäning integrering av alla program som stöder SAML 2.0 identitetsleverantörer (SP-initierad eller IdP-initierad)
* Självbetjäning integrering av alla webbprogram som har en HTML-baserad inloggningssidan med [lösenordsbaserade SSO](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* Självbetjäning anslutning av program som använder protokollet SCIM för användaretablering ([beskrivs här](manage-apps/use-scim-to-provision-users-and-groups.md))
* Möjligheten att lägga till länkar till alla program i den [Office 365 app programstart](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users)

Detta kan inkludera inte bara SaaS-program som du använder men har inte ännu har publicerat i Azure AD application Gallery utan från tredje part webbprogram som din organisation har distribuerats till servrar som du kontroll över, antingen i molnet eller lokalt.

Dessa funktioner, även kallat *app integration mallar*anger standardbaserad anslutningspunkter för appar som stöder SAML, SCIM eller formulärbaserad autentisering och inkludera flexibla alternativ och inställningar för kompatibilitet med ett stort antal program. 

## <a name="adding-an-unlisted-application"></a>Lägga till ett program som inte finns i listan
För att ansluta ett program med en mall för integrering av app måste du logga in på Azure-portalen med ditt Azure Active Directory-administratörskonto. Bläddra till den **Active Directory > företagsprogram > Nytt program > icke-galleriet programmet** väljer **Lägg till**, och sedan **lägga till ett program från galleriet** .

  ![](./media/active-directory-saas-custom-apps/customapp1.png)

I appgalleriet, du kan lägga till en ny app genom att välja den **icke-galleriet programmet** sida vid sida som visas i sökresultaten om appen önskade inte hittades. Du kan konfigurera alternativ för enkel inloggning och beteende när du har angett ett namn för ditt program. 

**Tips**: Använd sökfunktionen ett bra tips är för att kontrollera om programmet redan finns i programgalleriet. Om hitta appen och dess beskrivning nämns enkel inloggning, stöds redan programmet för federerad enkel inloggning.

  ![](./media/active-directory-saas-custom-apps/customapp2.png)

Lägga till ett program på detta sätt tillhandahåller en liknande som tillgänglig för redan integrerade program. Om du vill starta, Välj **Konfigurera enkel inloggning** eller klicka på **enkel inloggning** från programmenyn vänstra navigeringsfönstret. På nästa skärm anger alternativ för att konfigurera enkel inloggning. Alternativen beskrivs i nästa avsnitt i den här artikeln.
  
![](./media/active-directory-saas-custom-apps/customapp3.png)

## <a name="saml-based-single-sign-on"></a>SAML-baserad enkel inloggning
Välj det här alternativet för att konfigurera SAML-baserad autentisering för programmet. Detta kräver att programmet har stöd för SAML 2.0. Du bör samla in information om hur du använder SAML-funktioner i programmet innan du fortsätter. Gå igenom följande avsnitt om du vill konfigurera enkel inloggning mellan program och Azure AD.

### <a name="enter-basic-saml-configuration"></a>Ange grundläggande SAML-konfiguration

Ange den grundläggande SAML-konfigurationen för att ställa in Azure AD. Du kan manuellt ange värden eller överföra en metadatafil med för att extrahera värdet för fälten.

  ![URL: er och Litware domän](./media/active-directory-saas-custom-apps/customapp4.png)

- **Logga URL (SP-initierad endast)** – där användaren går för att logga in på det här programmet. Om programmet har konfigurerats för att utföra service provider-initierad enkel inloggning, och sedan när användarna navigerar till den här URL: en, tjänstleverantör gör nödvändiga omdirigeringen med Azure AD för att autentisera och logga in användaren i. Om det här fältet fylls använder Azure AD sedan denna URL starta program från Office 365 och Azure AD-åtkomstpanelen. Om fältet utelämnas, så Azure AD i stället utför identitetsleverantör-initieras inloggning när appen startas från Office 365, Azure AD-åtkomstpanelen eller i Azure AD enkel inloggnings-URL (copyable från fliken instrumentpanel).
- **Identifierare** -ska identifiera programmet där enkel inloggning som ska konfigureras. Du hittar det här värdet som utfärdaren element i AuthRequest (SAML-begäran) som skickas av programmet. Det här värdet visas också som den **enhets-ID** i eventuella SAML-metadata som tillhandahålls av programmet. Kontrollera programmets SAML dokumentation för information om dess värde med enhets-ID eller en publik är. 

    Följande är ett exempel på hur identifierare eller utfärdaren visas i SAML-begäran som skickades av programmet till Azure AD:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Reply URL** -reply-URL är där programmet förväntas ta emot SAML-token. Detta kallas även URL: en för Assertion konsumenten Service (ACS). Kontrollera programmets SAML dokumentation för information om dess SAML-token reply URL eller ACS-URL är. 

    Du kan använda följande PowerShell-skript för att konfigurera flera replyURLs.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Mer information finns i [SAML 2.0-autentisering och -svar som har stöd för Azure Active Directory (AD Azure)](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Granska eller anpassa anspråk som utfärdats i SAML-token

När en användare autentiseras för utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller detta användarens användarnamn, e-postadress, Förnamn och efternamn. 

Du kan visa eller redigera anspråk skickas i SAML-token till programmet under den **attribut** fliken.

  ![](./media/active-directory-saas-custom-apps/customapp7.png)

Det finns två skäl till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

- Programmet har skrivits till kräver en annan uppsättning anspråk URI: er eller anspråksvärden.
- Programmet har distribuerats på ett sätt som kräver NameIdentifier att anspråket ska vara något annat än användarnamnet (AKA användarens huvudnamn) lagras i Azure Active Directory. 

Mer information finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram](./develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Granska certifikatet upphör att gälla data, status och e-postavisering

När du skapar ett galleri eller icke-galleriet program, skapar Azure AD ett programspecifika certifikat med ett förfallodatum 3 år från det datum då skapas. Behöver du certifikat för att upprätta förtroende mellan Azure AD och programmet. Information om certifikat-format finns i programmets SAML-dokumentationen. 

Du kan hämta certifikatet i Base64- eller Raw-format från Azure AD. Du kan dessutom få certifikatet genom att hämta metadata för XML-filen för programmet eller genom att använda Webbadressen federation metadata.

  ![Certifikat](./media/active-directory-saas-custom-apps/certificate.png)

Kontrollera att certifikatet har:

- Den önskade upphör att gälla. Du kan konfigurera ett sista giltighetsdatum för högst tre år.
- Statusen aktiv. Ändra status till aktiv om status är inaktiverad. Om du vill ändra statusen Kontrollera **Active** och spara konfigurationen. 
- Rätt e-postmeddelandet. När det aktiva certifikatet närmar sig förfallodatum, skickar Azure AD ett meddelande till den e-postadress som konfigurerats i det här fältet.  

Mer information finns i [hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Ställ in målprogrammet

Leta upp programmets dokumentation om du vill konfigurera programmet för enkel inloggning. Rulla till slutet av sidan SAML-baserade inloggning konfiguration för att söka efter i dokumentationen och klicka sedan på **konfigurera <application name>** . 

Värden som krävs varierar beroende på program. Mer information finns i programmets SAML-dokumentationen. Inloggning och utloggning Tjänstwebbadress motsvara både samma slutpunkt, vilket är SAML hantering av begäran slutpunkten för din instans av Azure AD. SAML-enhets-ID är det värde som visas som utfärdaren i SAML-token som utfärdas till programmet.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Tilldela användare och grupper till SAML-program

När programmet har konfigurerats för att använda Azure AD som en SAML-baserade identitetsleverantör, är det nästan redo att testa. Azure AD utfärdar inte en token så att en användare att logga in på programmet, såvida inte Azure AD har beviljat åtkomst till användaren som en säkerhetskontroll. Användare kan beviljas åtkomst direkt eller via en gruppmedlemskap. 

Om du vill tilldela en användare eller grupp i programmet, klickar du på den **tilldela användare** knappen. Välj den användare eller grupp som du vill tilldela och välj sedan den **tilldela** knappen.

  ![](./media/active-directory-saas-custom-apps/customapp6.png)

Tilldela en användare kan Azure AD för att utfärda en token för användaren. Det gör också en panel för det här programmet ska visas i användarens åtkomstpanelen. Ett program sida vid sida visas också i startprogrammet för Office 365 om användaren använder Office 365. 

> [!NOTE] 
> Du kan ladda upp en ikonlogotyp för program med den **överföra logotypen** knappen på den **konfigurera** för programmet. 


### <a name="test-the-saml-application"></a>Testa SAML-program

Innan du testar programmet SAML måste du ha Ställ in program med Azure AD och tilldelade användare eller grupper till programmet.

  ![Testning](./media/active-directory-saas-custom-apps/testing.png)

Enkel inloggning-sidan klickar du på **Test SAML inställningar** under avsnittet domän och URL: er. Då öppnas en innehållsrutan med instruktioner om hur du testar programmet.

1. Logga in på programmet. Om programmet har konfigurerats som service provider-initierad enkel inloggning, omdirigeras till enkel inloggning URL där du kan initiera logga in. Om programmet har konfigurerats som identitet provider-initierad enkel inloggning, sedan du är inloggad till programmet.
2.  Om du ser något fel på sidan för företagets kopiera felet och gå tillbaka till Azure AD testa enkel inloggning innehållsrutan. Klistra in felet i rutan och klicka på **hämta Lösningssteg**. Om felet finns på sidan i programmet, måste du Kontakta programvaruleverantören och dela konfigurationen på Azure AD för att verifiera värdena. 
3.  Baserat på felet innehåller Azure AD specifika anvisningar om hur du löser problemet.

Mer information finns i [felsöka SAML-baserade enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-saml-debugging/?WT.mc_id=DMC_AAD_?WT.mc_id=UI_AAD_Configure_NonGalleryApps)


## <a name="password-single-sign-on"></a>Lösenord för enkel inloggning

Välj det här alternativet för att konfigurera [lösenordsbaserade enkel inloggning](manage-apps/what-is-single-sign-on.md) för ett webbprogram som har en HTML-inloggningssida. Lösenordsbaserade SSO, även kallat lösenord vaulting, kan du hantera åtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbara för situationer där flera användare behöva dela ett enda konto som din organisation sociala medier app konton. 

När du har valt **nästa**, du uppmanas att ange Webbadressen till programmets webbaserade inloggningssidan. Observera att detta måste vara den sida som innehåller indatafält användarnamn och lösenord. När du angett, startar Azure AD en process för att analysera inloggningssidan för att ange användarnamn och ett lösenord som indata. Om processen inte lyckas, sedan hjälper den dig att ett alternativ att installera ett webbläsartillägg (kräver Internet Explorer, Chrome eller Firefox) som gör att du kan avbilda fälten manuellt.

När inloggningssidan avbildas, användare och grupper kan tilldelas och autentiseringsuppgifter policys kan ställas in som vanlig [lösenord SSO appar](manage-apps/what-is-single-sign-on.md).

> [!NOTE] 
> Du kan ladda upp en ikonlogotyp för program med den **överföra logotypen** knappen på den **konfigurera** för programmet. 
>

## <a name="existing-single-sign-on"></a>Befintliga enkel inloggning
Välj det här alternativet om du vill lägga till en länk till ett program till din organisations Azure AD-åtkomstpanelen eller Office 365-portalen. Du kan använda detta att lägga till länkar till anpassade webbprogram som använder Azure Active Directory Federation Services (eller andra federationstjänsten) i stället för Azure AD för autentisering. Eller, du kan lägga till djuplänkar till specifika SharePoint-sidor eller andra webbsidor som bara ska visas på användarnas åtkomst paneler. 

När du har valt **nästa**, uppmanas du att ange URL för det program du länkar till. När slutfört, användare och grupper kan tilldelas till programmet, vilket gör att programmet ska visas i den [Office 365 app programstart](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) för dessa användare.

> [!NOTE] 
> Du kan ladda upp en ikonlogotyp för program med den **överföra logotypen** knappen på den **konfigurera** för programmet. 
>

## <a name="related-articles"></a>Relaterade artiklar

- [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
- [Anpassa anspråk som utfärdats i SAML-Token för förintegrerade appar](active-directory-saml-claims-customization.md)
- [Felsökning av SAML-baserade enkel inloggning](active-directory-saml-debugging.md)

