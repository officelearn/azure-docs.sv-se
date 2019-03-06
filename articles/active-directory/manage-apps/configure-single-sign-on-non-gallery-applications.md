---
title: Enkel inloggning – icke-galleriprogram - Azure Active Directory | Microsoft Docs
description: Konfigurera enkel inloggning (SSO) till icke-galleriprogram i Azure Active Directory (AD Azure)
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: celested
ms.reviewer: asmalser,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93e33ea311a064058e739bfcf6d63dc2d5462110
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435073"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-azure-active-directory"></a>Konfigurera enkel inloggning till icke-galleriprogram i Azure Active Directory

Den här artikeln handlar om en funktion som gör att administratörer kan konfigurera enkel inloggning till program som inte finns i appgalleriet för Azure Active Directory *utan att skriva kod*. Om du söker i stället för vägledning för utvecklare om hur du integrerar anpassade appar med Azure AD med hjälp av kod, se [Autentiseringsscenarier för Azure AD](../develop/authentication-scenarios.md).

Azure Active Directory-programgalleriet innehåller en lista över program som är kända för att stödja en form av enkel inloggning med Azure Active Directory, enligt beskrivningen i [i den här artikeln](what-is-single-sign-on.md). När du (som en IT-specialist eller system integrator i din organisation) har hittat programmet som du vill ansluta till, kan du komma igång genom att följa de stegvisa anvisningarna som visas i Azure-portalen för att aktivera enkel inloggning.

Dessa funktioner är också tillgängliga, enligt ditt licensavtal. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/active-directory/). 

- Integrering med självbetjäning för alla program som stöder SAML 2.0-Identitetsproviders (SP-initierat eller IdP-initierad)
- Integrering med självbetjäning för alla webbprogram som har en HTML-baserad på inloggningssidan med hjälp av [lösenordsbaserad SSO](what-is-single-sign-on.md#password-based-sso)
* Självbetjäning anslutning av program som använder protokollet SCIM för användaretablering ([som beskrivs här](use-scim-to-provision-users-and-groups.md))
* Du kan lägga till länkar till alla program i den [Office 365-appstartaren](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](what-is-single-sign-on.md#linked-sso)

Detta kan inkludera inte bara SaaS-program som du använder men har inte ännu har publicerat till Azure AD-programgalleriet, men andra leverantörers webb-program som din organisation har distribuerats till servrar som du styr, antingen i molnet eller lokalt.

De här funktionerna kallas även *mallar för integrering*anger standardbaserad anslutningspunkter för appar som stöder SAML, SCIM eller formulärbaserad autentisering och innehåller flexibla alternativ och inställningar för kompatibilitet med ett stort antal program. 

## <a name="adding-an-unlisted-application"></a>Lägga till ett program som inte finns i listan
Logga in på Azure portal med ditt konto i Azure Active Directory-administratör om du vill ansluta ett program med en mall för integrering. Bläddra till den **Active Directory > företagsprogram > Nytt program > icke-galleriprogram** väljer **Lägg till**, och sedan **lägga till ett program från galleriet** .

  ![Lägga till ett program](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

I app-galleriet kan du lägga till en app som inte finns i listan genom att välja den **icke-galleriprogram** panel som visas i sökresultaten om din önskade app inte hittades. När du har angett ett namn för ditt program, kan du konfigurera alternativ för enkel inloggning och beteende. 

**Snabbtips**:  Ett bra tips är att använda sökfunktionen för att kontrollera om programmet redan finns i programgalleriet. Om hitta appen och dess beskrivning nämner enkel inloggning, stöds redan programmet för federerad enkel inloggning.

  ![Search](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

Lägga till ett program på så sätt är liknande som tillgänglig för redan integrerade program. För att starta, markerar **Konfigurera enkel inloggning** eller klicka på **enkel inloggning** från programmets vänstra navigeringsmenyn. Nästa skärm anger alternativ för att konfigurera enkel inloggning. Alternativen beskrivs i nästa avsnitt av den här artikeln.
  
![Konfigurationsalternativ](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>SAML-baserad enkel inloggning
Välj det här alternativet för att konfigurera SAML-baserad autentisering för programmet. Detta kräver att programmet har stöd för SAML 2.0. Du bör samla in information om hur du använder SAML-funktioner i programmet innan du fortsätter. Gå igenom följande avsnitt för att konfigurera enkel inloggning mellan programmet och Azure AD.

### <a name="enter-basic-saml-configuration"></a>Ange grundläggande SAML-konfiguration

Ange den grundläggande SAML-konfigurationen om du vill konfigurera Azure AD. Du kan manuellt ange värdena eller överföra en metadatafil för att extrahera värdet för respektive fält.

  ![Litware domän och URL: er](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **Inloggning på URL: en (SP-initierat endast)** – där användaren går för att logga in till det här programmet. Om programmet har konfigurerats för tjänsten tjänstprovider-initierad enkel inloggning, och sedan när användarna navigerar till den här URL: en, tjänstleverantören gör nödvändiga omdirigeringen med Azure AD för att autentisera och logga in användaren i. Om det här fältet fylls sedan Azure AD ska använda den här URL: en starta program från Office 365 och Azure AD-åtkomstpanelen. Om det här fältet utelämnas kommer Azure AD i stället utför identitetsprovider-initierad inloggning när appen startas från Office 365, Azure AD-åtkomstpanelen eller från Azure AD enkel inloggnings-URL (kan kopieras från fliken instrumentpanel).
- **Identifieraren** -ska identifiera programmet som enkel inloggning konfigureras. Du hittar det här värdet som elementet utfärdaren i AuthRequest (SAML begäran) som skickas av programmet. Det här värdet visas också som den **entitets-ID** i alla SAML-metadata som tillhandahålls av programmet. Kontrollera programmets SAML-dokumentationen för information om vad som är attributvärdet entitets-ID eller målgrupp. 

    Följande är ett exempel på hur ID- eller Utfärdarnamn visas i SAML-begäran som skickas av programmet till Azure AD:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Svars-URL** -svars-URL är där programmet förväntas ta emot SAML-token. Detta kallas också Assertion konsument Service (ACS)-URL. Kontrollera programmets SAML-dokumentationen för mer information på dess SAML-token svars-URL eller ACS URL är. 

    Du kan använda följande PowerShell-skript för att konfigurera flera svarsurl: er.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

Mer information finns i [SAML 2.0-autentiseringsbegäranden och svar som har stöd för Azure Active Directory (AD Azure)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Granska eller anpassa anspråk som utfärdats i SAML-token

När en användare autentiseras till programmet utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller detta användarens användarnamn, e-postadress, Förnamn och efternamn. 

Du kan visa eller redigera anspråk som skickats i SAML-token till program under den **attribut** fliken.

  ![Attribut](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

Det finns två orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

- Programmet har skrivits för att kräva en annan uppsättning anspråk URI: er eller anspråksvärden.
- Programmet har distribuerats på ett sätt som kräver NameIdentifier-anspråket ska vara något annat än användarnamnet (AKA användarens huvudnamn) lagras i Azure Active Directory. 

Mer information finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram](./../develop/../develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Data om certifikatet upphör att gälla, status och e-postmeddelande

När du skapar ett galleri eller en icke-galleriprogram, skapar Azure AD ett programspecifika certifikat med ett förfallodatum i 3 år från skapandedatum. Du behöver det här certifikatet för att upprätta förtroende mellan Azure AD och programmet. Information om certifikat-format finns i programmets SAML-dokumentationen. 

Du kan hämta certifikatet i Base64 eller Raw-format från Azure AD. Du kan dessutom få certifikatet genom att hämta metadata för XML-filen för programmet eller genom att använda Appfederationsmetadata.

  ![Certifikat](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

Kontrollera att certifikatet har:

- Önskad förfallodatumet. Du kan konfigurera ett sista giltighetsdatum för högst 3 år.
- Statusen aktiv. Om statusen är inaktiva, ändrar du statusen till aktiv. Om du vill ändra status, kontrollera **Active** och spara konfigurationen. 
- Rätt e-postmeddelandet. När det aktiva certifikatet närmar sig förfallodatum, skickar Azure AD ett meddelande till den e-postadress som konfigurerats i det här fältet.  

Mer information finns i [hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Konfigurera målprogrammet

Leta upp programmets dokumentation för att konfigurera programmet för enkel inloggning. Rulla till slutet av sidan för SAML-baserad inloggning konfiguration för att hitta i dokumentationen, och klicka sedan på **konfigurera <application name>** . 

Värdena som krävs varierar beroende på program. Mer information finns i programmets SAML-dokumentationen. Inloggning och utloggning tjänst-URL för matcha både till samma slutpunkt, vilket är slutpunkten för SAML-hantering av begäranden för din instans av Azure AD. SAML entitets-ID är det värde som visas som utfärdaren i SAML-token som utfärdas till programmet.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Tilldela användare och grupper till SAML-program

När ditt program har konfigurerats för att använda Azure AD som en SAML-baserad identitetsprovider, är det nästan redo att testa. Som en säkerhetskontroll utfärdar Azure AD inte en token som tillåter en användare att logga in på programmet, såvida inte Azure AD har beviljat åtkomst till användaren. Användare kan beviljas åtkomst direkt eller via en gruppmedlemskap. 

Om du vill tilldela en användare eller grupp i ditt program, klickar du på den **tilldela användare** knappen. Välj den användare eller grupp som du vill tilldela och väljer sedan den **tilldela** knappen.

  ![Tilldela användare](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

Tilldela en användare kan Azure AD för att utfärda en token för användaren. Det gör även en panel för det här programmet ska visas i användarens åtkomstpanelen. Ett program sida vid sida visas också i startprogrammet för Office 365 om användaren använder Office 365. 

> [!NOTE] 
> Du kan ladda upp en ikonlogotyp för programmet med den **ladda upp logotyp** knappen på den **konfigurera** fliken för programmet. 


### <a name="test-the-saml-application"></a>Testa SAML-program

Innan du testar programmet SAML, måste du har registrerat programmet med Azure AD och tilldelade användare eller grupper till programmet. För att testa SAML-program, se [Felsök SAML-baserad enkel inloggning till program i Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Lösenord för enkel inloggning

Välj det här alternativet för att konfigurera [lösenordsbaserad enkel inloggning](what-is-single-sign-on.md) för ett webbprogram som har en HTML-inloggningssida. Lösenordsbaserad SSO, även kallat lösenord vaulting, kan du hantera användaråtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbart för scenarier där flera användare behöva dela ett enda konto, till exempel till din organisations konton för sociala medier. 

När du har valt **nästa**, uppmanas du att ange Webbadressen till programmets webbaserade inloggningssidan. Observera att detta måste vara den sida som innehåller användarnamn och lösenord indatafält. När du har angett, startar en process för att parsa inloggningssidan för ett användarnamn som indata och ett lösenord i Azure AD. Om processen inte lyckas, sedan vägleder den dig genom en annan process för att installera ett webbläsartillägg (kräver Internet Explorer, Chrome eller Firefox) som gör att du manuellt samla in fälten.

När avbildas på inloggningssidan, användare och grupper tilldelas och principer för autentiseringsuppgifter kan ställas in precis som vanliga [lösenord SSO appar](what-is-single-sign-on.md).

> [!NOTE] 
> Du kan ladda upp en ikonlogotyp för programmet med den **ladda upp logotyp** knappen på den **konfigurera** fliken för programmet. 
>

## <a name="existing-single-sign-on"></a>Befintlig enkel inloggning
Välj det här alternativet om du vill lägga till en länk till ett program till din organisations Azure AD-åtkomstpanelen eller Office 365-portalen. Du kan använda detta att lägga till länkar i anpassade webbappar som använder Active Directory Federation Services (eller annan federationstjänst) i stället för Azure AD för autentisering. Eller du kan lägga till djuplänkar till specifika SharePoint-sidor eller andra webbsidor som du vill visas på användarnas Åtkomstpaneler. 

När du har valt **nästa**, uppmanas du att ange Webbadressen till programmet för att länka till. När slutfört, användare och grupper kan tilldelas till programmet, vilket gör att programmet ska visas i den [Office 365-appstartaren](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](end-user-experiences.md) för dessa användare.

> [!NOTE] 
> Du kan ladda upp en ikonlogotyp för programmet med den **ladda upp logotyp** knappen på den **konfigurera** fliken för programmet. 
>

## <a name="related-articles"></a>Relaterade artiklar
- [Anpassa anspråk som utfärdats i SAML-Token för förintegrerade appar](../develop/active-directory-saml-claims-customization.md)
- [Felsöka SAML-baserad enkel inloggning](../develop/howto-v1-debug-saml-sso-issues.md)

