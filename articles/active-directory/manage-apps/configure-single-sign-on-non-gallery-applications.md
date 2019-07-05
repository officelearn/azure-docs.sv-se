---
title: Enkel - icke-galleriprogram - inloggning Microsoft identity-plattformen | Microsoft Docs
description: Konfigurera enkel inloggning (SSO) till icke-galleriprogram i Microsoft identity-plattformen (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95b7cbcf4e485dc93d49b9559dcb7d0d4f597ebe
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550341"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Konfigurera enkel inloggning till icke-galleriprogram i Microsoft identity-plattformen

Den här artikeln handlar om en funktion som gör att administratörer kan konfigurera enkel inloggning för program som saknas i appgalleriet för Microsoft identity-plattformen *utan att skriva kod*.

Om du söker i stället för vägledning för utvecklare om hur du integrerar anpassade appar med Azure AD med hjälp av kod, se [Autentiseringsscenarier för Azure AD](../develop/authentication-scenarios.md).

Programgalleriet för Microsoft identity-plattformen innehåller en lista över program som är kända för att stödja en form av enkel inloggning med Microsoft identity-plattformen, enligt beskrivningen i [i den här artikeln](what-is-single-sign-on.md). När du (som en IT-specialist eller system integrator i din organisation) har hittat programmet som du vill ansluta till, kan du komma igång genom att följa de stegvisa anvisningarna som visas i Azure-portalen för att aktivera enkel inloggning.

Följande funktioner är också tillgängliga, enligt ditt licensavtal. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/active-directory/).

- Integrering med självbetjäning för ett program som använder en modern protokollet som [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) att verifiera användare och hämta token för [Microsoft Graph](https://graph.microsoft.com).
- Integrering med självbetjäning för alla program som stöder [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) identitetsleverantörer (SP-initierat eller IdP-initierad)
- Integrering med självbetjäning för alla webbprogram som har en HTML-baserad på inloggningssidan med hjälp av [lösenordsbaserad SSO](what-is-single-sign-on.md#password-based-sso)
- Självbetjäning anslutning av program som använder den [System för domänerna Identity Management (SCIM)-protokollet för etableringen av användare](use-scim-to-provision-users-and-groups.md)
- Du kan lägga till länkar till alla program i den [Office 365-appstartaren](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](what-is-single-sign-on.md#linked-sign-on)

Dessa funktioner kan innehålla integrering med självbetjäning av en programvara som en tjänst (SaaS) som du använder, även om ingen har publicerat programmet till Azure AD-programgalleriet ännu. En annan funktion är integrering med självbetjäning för ett webbprogram från tredje part som din organisation har distribuerats till servrar som du styr, antingen i molnet eller lokalt.

Även känt som *mallar för integrering*, de här funktionerna ger standardbaserad anslutningspunkter för program som stöder SAML, SCIM eller formulärbaserad autentisering. Funktionerna innehåller flexibla alternativ och inställningar för kompatibilitet med ett stort antal program.

## <a name="adding-an-unlisted-application"></a>Lägga till ett program som inte finns i listan

Microsoft identity-plattformen ger två mekanismer för att registrera program.

Ett program som använder ett moderna protokoll som [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) att autentisera användarna har registrerats med hjälp av den [programregistreringsportalen](../develop/quickstart-register-app.md).

Registrera program med alla andra typer av [stöds autentiseringsmekanismer](what-is-single-sign-on.md), till exempel den [SAML](../develop/single-sign-on-saml-protocol.md) protokoll, Använd den **företagsprogram** bladet för att ansluta dem med Microsoft identity-plattformen.

Om du vill ansluta ett program som inte finns i listan med hjälp av en mall för integrering, gör du följande:

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/) med ditt administratörskonto för Microsoft identity-plattformen.
2. Välj **företagsprogram** > **nytt program**.
3. (Valfritt men rekommenderas) I den **Lägg till från galleriet** sökrutan, ange visningsnamnet för programmet. Om programmet visas i sökresultaten, markera den och hoppa över resten av den här proceduren.
4. Välj **icke-galleriprogram**. Den **lägga till ditt eget program** visas.

   ![Lägga till ett program](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Ange visningsnamnet för det nya programmet.
6. Välj **Lägg till**.

Du lägger till ett program på så sätt kan ange du samma möjligheter till den tillgängliga för redan integrerade program. Välj först **enkel inloggning** från programmets sidopanelen. Nästa sida (**väljer du en metod för enkel inloggning**) anger alternativ för att konfigurera enkel inloggning:

- **SAML**
- **Lösenordsbaserad**
- **Länkade**

![Välj en metod för enkel inloggning](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Mer information om dessa alternativ finns i följande avsnitt i den här artikeln.

## <a name="saml-based-single-sign-on"></a>SAML-baserad enkel inloggning

Välj den **SAML** möjlighet att konfigurera SAML-baserad autentisering för programmet. (Det här alternativet kräver att programmet har stöd för SAML 2.0). Den **ange in enkel inloggning med SAML** visas.

![Konfigurera enkel inloggning med SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Den här sidan har fem olika rubriker:

| Rubriknummer | Rubriknamnet | En sammanfattning av den här rubriken finns: |
| --- | --- | --- |
| 1 | **Grundläggande SAML-konfiguration** | [Ange grundläggande SAML-konfiguration](#enter-basic-saml-configuration) |
| 2 | **Användarattribut och anspråk** | [Granska eller anpassa anspråk som utfärdats i SAML-token](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **SAML-signeringscertifikat** | [Data om certifikatet upphör att gälla, status och e-postmeddelande](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Konfigurera \<programnamn >** | [Konfigurera målprogrammet](#set-up-target-application) |
| 5 | **Testa enkel inloggning med \<programnamn >** | [Testa SAML-program](#test-the-saml-application) |

Samla in information om hur du använder SAML-funktioner i programmet innan du fortsätter. Gå igenom följande avsnitt för att konfigurera enkel inloggning mellan programmet och Azure AD.

### <a name="enter-basic-saml-configuration"></a>Ange grundläggande SAML-konfiguration

Om du vill konfigurera Azure AD går du till den **SAML grundkonfiguration** rubrik- och välj dess **redigera** ikon (en penna). Du kan manuellt ange värdena eller överföra en metadatafil för att extrahera värdet för respektive fält.

![Grundläggande SAML-konfiguration](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Följande två fält är obligatoriska:

- **Identifieraren**. Det här värdet ska identifiera programmet som enkel inloggning konfigureras. Du hittar det här värdet som den **utfärdare** elementet i den **AuthnRequest** (SAML begäran) som skickas av programmet. Det här värdet visas också som den **entitets-ID** i alla SAML-metadata som tillhandahålls av programmet. Kontrollera programmets SAML-dokumentationen för mer information om vad dess **entitets-ID** eller **målgrupp** värdet är.

  Följande kod visar hur **identifierare** eller **utfärdare** visas i SAML-begäran som programmet skickar till Azure AD:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **Svars-URL**. Svars-URL är där programmet förväntas ta emot SAML-token. Denna URL kallas även URL: en försäkran konsument service (ACS). Kontrollera programmets SAML-dokumentationen för mer information på dess SAML-token svars-URL eller ACS URL är.

  Du kan använda följande PowerShell-skript för att konfigurera flera svars-URL.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

Följande tre fält är valfria:

- **Inloggnings-URL (SP-initierat endast)** . Det här värdet anger där användaren går för att logga in på det här programmet. Om programmet utför SP-initierad SSO, och utför när en användare navigerar till den här URL: en, SP nödvändiga omdirigering till Azure AD för att autentisera och loggar in användaren. Om du anger det här fältet, använder Azure AD URL: en för att starta program från Office 365 och Azure AD-åtkomstpanelen. Om du utelämnar det här fältet Azure AD i stället utför IdP-initierad inloggning under programstarter från Office 365, Azure AD-åtkomstpanelen eller Azure AD SSO-URL: en (som du kan kopiera från den **instrumentpanelen** sida).

- **Vidarebefordransstatus**. Du kan ange ett relay-tillstånd i SAML som instruerar programmet var användare ska omdirigeras efter autentisering. Värdet är vanligtvis en URL eller URL: en sökväg som tar användare till en specifik plats i programmet.

- **URL för utloggning**. Det här värdet används för att skicka SAML-utloggning svar tillbaka till programmet.

Mer information finns i [enkel inloggning SAML-protokoll](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Granska eller anpassa anspråk som utfärdats i SAML-token

När en användare autentiseras till programmet skickar Azure AD programmet en SAML-token med information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller den här informationen användarens användarnamn, e-postadress, Förnamn och efternamn.

Om du vill visa eller redigera anspråk skickas i SAML-token för programmet:

- Gå till den **användarattribut och anspråk** rubrik- och väljer den **redigera** ikon. Den **användarattribut och anspråk** visas.

![Användarattribut och anspråk](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Du kan behöva redigera de anspråk som utfärdats i SAML-token av två skäl:

- Programmet kräver en annan uppsättning anspråk URI: er eller anspråksvärden.
- Programmet kräver den **namnge identifierarvärde** påstår sig vara något annat än användarnamnet (även kallat user principal name) lagras i Microsoft identity-plattformen.

Mer information finns i [Gör så här: Anpassa anspråk som utfärdats i SAML-token för företagsprogram](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Data om certifikatet upphör att gälla, status och e-postmeddelande

När du skapar ett galleri eller en icke-galleriprogram, skapar ett programspecifika certifikat som upphör att gälla tre år från dess skapad i Azure AD. Du behöver det här certifikatet för att upprätta förtroende mellan Azure AD och programmet. Information om certifikat-format finns i programmets SAML-dokumentationen.

Azure AD kan du hämta det aktiva certifikatet i Base64 eller Raw-format direkt från huvudsakliga **ange in enkel inloggning med SAML** sidan. Du kan också hämta det aktiva certifikatet genom att hämta metadata för XML-filen för programmet eller genom att använda Appfederationsmetadata.

Om du vill visa, skapa eller hämta dina certifikat (aktiv eller inaktiv), går du till den **SAML-signeringscertifikat** rubrik- och väljer den **redigera** ikon. Den **SAML-signeringscertifikat** visas.

![SAML-signeringscertifikat](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Kontrollera att certifikatet har:

- *Önskad förfallodatumet.* Du kan konfigurera ett sista giltighetsdatum för upp till tre år i framtiden.
- *Statusen aktiv för önskad certifikatet.* Om statusen är **inaktiv**, ändrar du statusen till **Active**. Om du vill ändra status, högerklickar du på önskad certifikatets raden och välj **aktivera certifikatet**.
- *Rätt alternativ för signering och algoritmen.*
- *Korrekt meddelande e-postadresserna.* När det aktiva certifikatet närmar sig förfallodatum, skickar Azure AD ett meddelande till den e-postadress som konfigurerats i det här fältet.  

Mer information finns i [hantera certifikat för federerad enkel inloggning](manage-certificates-for-federated-single-sign-on.md) och [avancerade alternativ i SAML-token för certifikatsignering](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Konfigurera målprogrammet

Leta upp programmets dokumentation för att konfigurera programmet för enkel inloggning. Du hittar dokumentationen genom att gå till den **konfigurera \<programnamn >** rubrik- och väljer **visa stegvisa instruktioner**. Dokumentationen som visas i den **konfigurera inloggning** sidan. Sidan beskriver hur du fyller i den **inloggnings-URL**, **Azure AD-identifierare**, och **URL för utloggning** värdena i den **konfigurera \<programnamn >** rubrik.

Värdena som krävs varierar beroende på program. Mer information finns i programmets SAML-dokumentationen. Den **inloggnings-URL** och **URL för utloggning** värden både matcha till samma slutpunkt, vilket är slutpunkten för SAML-hantering av begäranden för din instans av Azure AD. Den **Azure AD-identifierare** är värdet för den **utfärdare** i SAML-token som utfärdats till programmet.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Tilldela användare och grupper till SAML-program

När du har konfigurerat ditt program att använda Azure AD som en SAML-baserad identitetsprovider, är det nästan redo att testa. Azure AD utfärdar endast en token som tillåter en användare att logga in i programmet om Azure AD har beviljats åtkomst till användaren som en säkerhetskontroll. Användare kan komma åt direkt eller via en gruppmedlemskap.

Tilldela en ny användare eller grupp i ditt program:

1. I program-sidopanelen väljer **användare och grupper**. Den  **\<programnamn >-användare och grupper** visas, som visar den aktuella listan över tilldelade användare och grupper.
2. Välj **lägga till användare**. Den **lägga till tilldelningar** visas.
3. Välj **användare och grupper (\<Number > valda)** . Den **användare och grupper** visas, som visar en lista över tillgängliga användare och grupper.
4. Skriv eller Bläddra för att hitta användaren eller gruppen som du vill tilldela i listan.
5. Välj alla användare eller grupp som du vill lägga till och välj sedan den **Välj** knappen. Den **användare och grupper** sidan försvinner.
6. I den **lägga till tilldelningar** väljer **tilldela**. Den  **\<programnamn >-användare och grupper** visas med ytterligare användare visas i listan.

   ![Programanvändare och grupper](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

I den här listan kan du:

- Ta bort en användare.
- Redigera deras roll.
- Uppdatera sina autentiseringsuppgifter (användarnamn och lösenord) så att användaren kan autentisera till programmet från åtkomstpanelen för användaren.

Du kan redigera eller ta bort flera användare eller grupper i taget.

Tilldela en användare kan Azure AD för att utfärda en token för användaren. Det gör även en panel för det här programmet ska visas i användarens åtkomstpanelen. Ett program sida vid sida visas också i startprogrammet för Office 365 om användaren använder Office 365.

> [!NOTE]
> Du kan ladda upp en ikonlogotyp för programmet med den **ladda upp logotyp** knappen på den **konfigurera** fliken för programmet.

### <a name="test-the-saml-application"></a>Testa SAML-program

Innan du testar programmet SAML, måste du redan har registrerat programmet med Azure AD och tilldelade användare eller grupper till programmet. För att testa SAML-program, Välj **enkel inloggning**, vilket tillbaka till den **SAML-baserad inloggning** sidan. (Om en annan metod för enkel inloggning var gäller väljer **ändra lägen för enkel inloggning** > **SAML** för.) I den **testa enkel inloggning med \<programnamn >** väljer **Test**. Mer information finns i [Felsök SAML-baserad enkel inloggning till program i Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Lösenord för enkel inloggning

Välj det här alternativet för att konfigurera [lösenordsbaserad enkel inloggning](what-is-single-sign-on.md) för ett webbprogram med en HTML-inloggningssida. Lösenordsbaserad SSO, även kallat lösenord vaulting, kan du hantera användaråtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbart för scenarier där flera användare behöva dela ett enda konto, till exempel till din organisations konton för sociala medier.

När du har valt **lösenordsbaserad**, uppmanas du att ange Webbadressen till programmets webbaserade inloggningssidan.

![Lösenordsbaserad enkel inloggning](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Sedan gör du följande:

1. Ange URL: en. Den här strängen måste vara den sida som innehåller indatafältet för användarnamn.
2. Välj **Spara**. Azure AD försöker parsa inloggningssidan för ett användarnamn som indata och ett lösenord som indata.
3. Om Azure AD parsning försöket misslyckas, väljer **konfigurera \<programnamn > Inställningar för lösenord för enkel inloggning** att visa den **konfigurera inloggning** sidan. (Om försöket lyckas, du kan bortse från resten av den här proceduren.)
4. Välj **identifieras manuellt inloggningsfält**. Ytterligare instruktioner för manuell identifiering av inloggningsfält visas.

   ![Manuell konfiguration av lösenordsbaserad enkel inloggning](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Välj **fånga in inlogggningsfält**. En avbildning statussida öppnas i en ny flik, som visar meddelandet **metadatafångst pågår just nu**.
6. Om den **krävs för åtkomst till panelen tillägget** visas i en ny flik i rutan Välj **installera nu** att installera den **Mina appar skyddat Inloggningstillägg** webbläsartillägg. (Webbläsartillägget kräver Microsoft Edge, Chrome eller Firefox.) Installera, starta, aktivera tillägget och uppdatera sidan capture status.

   Webbläsartillägget öppnas en annan flik som visar den angivna Webbadressen.
7. Gå igenom hur du loggar in på fliken med den angivna Webbadressen. Fyll i fälten användarnamn och lösenord och försök att logga in. (Du behöver ange rätt lösenord.)

   En uppmaning som frågar dig att spara de hämtade inloggningsfält.
8. Välj **OK**. Fliken stängs, webbläsartillägget uppdaterar på statussidan för avbildning med meddelandet **Metadata har uppdaterats för programmet**, och den webbläsaren som också fliken stängs.
9. I Azure AD **konfigurera inloggning** väljer **Ok, jag kunde logga in på appen har**.
10. Välj **OK**.

När avbildningen av på inloggningssidan, kan du tilldela användare och grupper och du kan ställa in autentiseringsuppgifter principer precis som vanliga [lösenord SSO-program](what-is-single-sign-on.md).

> [!NOTE]
> Du kan ladda upp en ikonlogotyp för programmet med den **ladda upp logotyp** knappen på den **konfigurera** fliken för programmet.

## <a name="existing-single-sign-on"></a>Befintlig enkel inloggning

Välj det här alternativet om du vill lägga till en länk till programmet i din organisations Azure AD-åtkomstpanelen eller Office 365-portalen. Du kan använda den här metoden för att lägga till länkar till anpassade webbprogram som använder Active Directory Federation Services (eller annan federationstjänst) i stället för Azure AD för autentisering. Eller du kan lägga till djuplänkar till specifika SharePoint-sidor eller andra webbsidor som du vill visas på användarnas Åtkomstpaneler.

När du har valt **länkade**, uppmanas du att ange Webbadressen till programmet för att länka till. Ange URL: en och välj **spara**. Du kan tilldela användare och grupper till programmet, vilket gör att programmet ska visas i den [Office 365-appstartaren](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](end-user-experiences.md) för dessa användare.

> [!NOTE]
> Du kan ladda upp en ikonlogotyp för programmet med den **ladda upp logotyp** knappen på den **konfigurera** fliken för programmet.

## <a name="related-articles"></a>Relaterade artiklar

- [Anvisningar: Anpassa anspråk som utfärdats i SAML-token för företagsprogram](../develop/active-directory-saml-claims-customization.md)
- [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Microsoft identity-plattformen (tidigare Azure Active Directory för utvecklare)](../develop/index.yml)
