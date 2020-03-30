---
title: SAML enkel inloggning - icke-galleriprogram - Microsoft identity platform | Microsoft-dokument
description: Konfigurera enkel inloggning (SSO) till program som inte är gallerier i Microsoft identity platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244217"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Konfigurera SAML-baserad enkel inloggning till program som inte är gallerier

När du [lägger till en galleriapp](add-gallery-app.md) eller en [webbapp som inte är galleri](add-non-gallery-app.md) i dina Azure AD Enterprise-program är ett av de enskilda inloggningsalternativ som är tillgängliga för dig [SAML-baserad enkel inloggning](what-is-single-sign-on.md#saml-sso). Välj SAML när det är möjligt för program som autentiserar med något av SAML-protokollen. Med SAML enkel inloggning autentiserar Azure AD till programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggningsinformationen till programmet via ett anslutningsprotokoll. Du kan mappa användare till specifika programroller baserat på regler som du definierar i dina SAML-anspråk. I den här artikeln beskrivs hur du konfigurerar SAML-baserad enkel inloggning för en app som inte är galleri. 

> [!NOTE]
> Lägger du till en galleriapp? Hitta steg-för-steg-installationsinstruktioner i [listan över Självstudier för SaaS-appar](../saas-apps/tutorial-list.md)

Om du vill konfigurera SAML-enkel inloggning för ett program som inte är galleri utan att skriva kod måste du ha en prenumeration tillsammans med en Azure AD Premium-licens och programmet måste stödja SAML 2.0. Mer information om Azure AD-versioner finns i [Azure AD-priser](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Innan du börjar

Om programmet inte har lagts till i din Azure [AD-klient](add-non-gallery-app.md)finns i Lägga till en app som inte är galleri.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Steg 1. Redigera den grundläggande SAML-konfigurationen

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

2. Navigera till **Azure Active Directory** > **Enterprise-program** och välj programmet i listan. 
   
   - Om du vill söka efter programmet väljer du **Alla program**på **menyn Programtyp** och väljer sedan **Använd**. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

3. Under avsnittet **Hantera** väljer du **Enkel inloggning**. 

4. Välj **SAML**. Sidan **Konfigurera enkel inloggning med SAML - Förhandsgranska** visas.

   ![Steg 1 Redigera den grundläggande SAML-konfigurationen](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Om du vill redigera de grundläggande SAML-konfigurationsalternativen markerar du **ikonen Redigera** (en penna) i det övre högra hörnet i avsnittet **Grundläggande SAML-konfiguration.**

1. Ange följande inställningar. Du bör hämta värdena från programleverantören. Du kan ange värdena eller ladda upp en metadatafil manuellt för att extrahera värdet för fälten.

    | Grundläggande KONFIGURATION AV SAML | SP-initierad | idP-initierad | Beskrivning |
    |:--|:--|:--|:--|
    | **Identifierare (entitets-ID)** | Krävs för vissa appar | Krävs för vissa appar | Identifierar programmet unikt. Azure AD skickar identifieraren till programmet som målgruppsparametern för SAML-token. Programmet förväntas verifiera den. Detta värde visas även som entitets-ID i alla SAML-metadata som anges av programmet. Ange en URL som använder följande mönster: 'https://<subdomain>.contoso.com' Du hittar det här värdet som * **utfärdarelementet** i **AuthnRequest** (SAML-begäran) som skickas av programmet.* |
    | **Svarswebbadress** | Krävs | Krävs | Anger var programmet förväntas ta emot SAML-token. Svars-URL:en kallas även för URL för konsumenttjänst för försäkran (ACS-URL). Du kan använda de ytterligare url-fälten för svar för att ange flera svarsadresser. Du kan till exempel behöva ytterligare svarsadresser för flera underdomäner. Du kan också ange flera svarsadresser (lokala värd- och offentliga webbadresser) åt gången. |
    | **URL för inloggning** | Krävs | Ange inte | När en användare öppnar den här URL:en omdirigerar tjänstleverantören till Azure AD för att autentisera och logga in användaren. Azure AD använder URL:en för att starta programmet från Office 365 eller Azure AD-åtkomstpanelen. När det är tomt utför Azure AD IdP-initierad inloggning när en användare startar programmet från Office 365, Azure AD Access Panel eller Azure AD SSO-URL:en.|
    | **Vidarebefordransstatus** | Valfri | Valfri | Anger för programmet var användaren ska omdirigeras när autentiseringen har slutförts. Vanligtvis är värdet en giltig URL för programmet. Vissa program använder dock det här fältet på ett annat sätt. Kontakta programleverantören om du vill ha mer information.
    | **Utloggnings-URL** | Valfri | Valfri | Används för att skicka SAML Logout svar tillbaka till programmet.

Mer information finns i [Saml-protokollet för enkel inloggning](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Steg 2. Konfigurera användarattribut och anspråk 

När en användare autentiserar till programmet utfärdar Azure AD programmet en SAML-token med information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller den här informationen användarens användarnamn, e-postadress, förnamn och efternamn. Du kan behöva anpassa dessa anspråk om programmet till exempel kräver specifika anspråksvärden eller ett **namnformat** annat än användarnamn. Krav för galleriappar [beskrivs i de programspecifika självstudierna](../saas-apps/tutorial-list.md)eller så kan du fråga programleverantören. De allmänna stegen för att konfigurera användarattribut och anspråk beskrivs nedan.

1. I avsnittet **Användarattribut och anspråk** väljer du **ikonen Redigera** (en penna) i det övre högra hörnet.

   ![Steg 2 Konfigurera användarattribut och anspråk](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verifiera **namnidentifieringsvärdet**. Standardvärdet är *user.principalname*. Användaridentifieraren identifierar unikt varje användare i programmet. Exempel: om e-postadressen är både användarnamnet och den unika identifieraren anger du värdet *user.mail*.

3. Om du vill ändra **namnidentifieringsvärdet**markerar du **ikonen Redigera** (en penna) för fältet **Namnidentifieringsvärde.** Gör lämpliga ändringar i identifierarformat och källa efter behov. Mer information finns i [Redigera NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Spara ändringarna när du är klar. 
 
4. Om du vill konfigurera gruppanspråk väljer du ikonen **Redigera** för de **grupper som returneras i anspråksfältet.** Mer information finns i [Konfigurera gruppanspråk](../hybrid/how-to-connect-fed-group-claims.md).

5. Om du vill lägga till ett anspråk väljer du **Lägg till nya anspråk** högst upp på sidan. Ange **namnet** och välj lämplig källa. Om du väljer **attributkällan** måste du välja det **källattribut** som du vill använda. Om du väljer **översättningskällan** måste du välja den **omvandling** och parameter **1** som du vill använda. Mer information finns i [Lägga till programspecifika anspråk](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Spara ändringarna när du är klar. 

6. Välj **Spara**. Det nya anspråket visas i tabellen.

   > [!NOTE]
   > Mer information om hur du anpassar SAML-token från Azure AD till ditt program finns i följande resurser.
   >- Information om hur du skapar anpassade roller via Azure-portalen finns i [Konfigurera rollanspråk](../develop/active-directory-enterprise-app-role-management.md).
   >- Om du vill anpassa anspråken via PowerShell finns i [Anpassa anspråk - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Hur du ändrar programmanifestet för att konfigurera valfria anspråk för ditt program finns i [Konfigurera valfria anspråk](../develop/active-directory-optional-claims.md).
   >- Om du vill ange principer för tokenlivstid för uppdateringstoken, åtkomsttoken, sessionstoken och ID-token finns [i Konfigurera tokenlivstid .](../develop/active-directory-configurable-token-lifetimes.md) Eller, för att begränsa autentiseringssessioner via Azure AD Villkorlig åtkomst, se [autentiseringssessionshanteringsfunktioner](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Steg 3. Hantera SAML-signeringscertifikatet

Azure AD använder ett certifikat för att signera SAML-token som skickas till programmet. Du behöver det här certifikatet för att ställa in förtroendet mellan Azure AD och programmet. Mer information om certifikatformatet finns i programmets SAML-dokumentation. Mer information finns i [Hantera certifikat för federerade alternativ för enkel inloggning](manage-certificates-for-federated-single-sign-on.md) och Avancerad [certifikatsignering i SAML-token](certificate-signing-options.md).

Från Azure AD kan du hämta det aktiva certifikatet i Base64- eller Raw-format direkt från huvudsidan **Konfigurera enkel inloggning med SAML.** Du kan också hämta det aktiva certifikatet genom att hämta XML-filen för programmetadata eller med hjälp av URL:en för appfederationsmetadata. Så här visar, skapar eller hämtar du dina certifikat (aktiva eller inaktiva).

1. Gå till avsnittet **SAML-signeringscertifikat.** 

   ![Steg 3 Hantera SAML-signeringscertifikatet](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Kontrollera att certifikatet har:

   - *Önskat utgångsdatum.* Du kan konfigurera utgångsdatumet i upp till tre år in i framtiden.
   - *Statusen aktiv för önskat certifikat.* Om statusen är **Inaktiv**ändrar du statusen till **Aktiv**. Om du vill ändra status högerklickar du på det önskade certifikatets rad och väljer **Gör certifikatet aktivt**.
   - *Rätt signeringsalternativ och algoritm.*
   - *Rätt e-postadress för anmälan.* När det aktiva certifikatet är nära utgångsdatumet skickar Azure AD ett meddelande till den e-postadress som konfigurerats i det här fältet.

2. Om du vill hämta certifikatet väljer du ett av alternativen för Base64-format, Raw-format eller FEDERATION Metadata XML. Azure AD tillhandahåller också **url:en för metadata för appfederationen** där du kan komma åt metadata som är specifika för programmet i formatet `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Om du vill hantera, skapa eller importera ett certifikat markerar du **ikonen Redigera** (en penna) i det övre högra hörnet i avsnittet **SAML-signeringscertifikat.**

   ![INTYG om SAML-signering](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Vidta någon av följande åtgärder:

   - Om du vill skapa ett nytt certifikat väljer du **Nytt certifikat,** väljer **utgångsdatum**och väljer sedan **Spara**. Om du vill aktivera certifikatet markerar du snabbmenyn (**...**) och väljer **Gör certifikatet aktivt**.
   - Om du vill ladda upp ett certifikat med privata nyckel- och pfx-autentiseringsuppgifter väljer du **Importera certifikat** och bläddrar till certifikatet. Ange **PFX-lösenordet**och välj sedan **Lägg till**.  
   - Om du vill konfigurera avancerade alternativ för certifikatsignering använder du följande alternativ. Beskrivningar av dessa alternativ finns i artikeln [Alternativ för avancerade certifikatsigneringsalternativ.](certificate-signing-options.md)
      - I listrutan **Signeringsalternativ** väljer du **Signera SAML-svar,** **Signera SAML-påstående**eller **Signera SAML-svar och -- -eller signera SAML-svar och -- -eller signera SAML-svar och -- -eller signera SAML-svar och -- -eller signera SAML-svar och -- -.**
      - I listrutan **Signeringsalgoritm** väljer du **SHA-1** eller **SHA-256**.
   - Om du vill meddela ytterligare personer när det aktiva certifikatet är nära utgångsdatumet anger du e-postadresserna i fälten **E-postadresser till meddelanden.**

4. Om du har gjort ändringar väljer du **Spara** högst upp i avsnittet **SAML-signeringscertifikat.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Steg 4. Konfigurera programmet så att det använder Azure AD

Avsnittet **Konfigurera \<applicationName>** visar de värden som måste konfigureras i programmet så att azure AD används som SAML-identitetsprovider. De värden som krävs varierar beroende på programmet. Mer information finns i programmets SAML-dokumentation. Om du vill hitta dokumentationen går du till rubriken **Konfigurera \<programnamn>** och väljer **Visa steg-för-steg-instruktioner**. Dokumentationen visas på sidan **Konfigurera inloggning.** Den sidan hjälper dig att fylla i **värdena för inloggnings-URL,** **Azure AD-identifierare**och **utloggning** i rubriken **Konfigurera \<programnamn>.**

1. Bläddra ned till avsnittet **Konfigurera \<applicationName>.** 
   
   ![Steg 4 Konfigurera programmet](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Kopiera värdet från varje rad i det här avsnittet efter behov och följ de programspecifika instruktionerna för att lägga till värdet i programmet. För galleriappar kan du visa dokumentationen genom att välja **Visa steg-för-steg-instruktioner**. 
   - Url-värden för **inloggnings-URL** och **utloggning** matchas till samma slutpunkt, vilket är slutpunkten för HANTERING AV SAML för din instans av Azure AD. 
   - **Azure AD-identifieraren** är värdet för **Utfärdaren** i SAML-token som utfärdats till programmet.
2. När du har klistrat in alla värden i lämpliga fält väljer du **Spara**.

## <a name="step-5-validate-single-sign-on"></a>Steg 5. Validera enkel inloggning

När du har konfigurerat ditt program så att det använder Azure AD som SAML-baserad identitetsleverantör kan du testa inställningarna för att se om enkel inloggning fungerar för ditt konto. 

2. Bläddra till avsnittet **Validera enkel <applicationName> inloggning med.**

   ![Steg 5 Validera enkel inloggning](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Välj **Verifiera**. Testningsalternativen visas.

4. Välj **Logga in som aktuell användare**. 

Om inloggningen lyckas är du redo att tilldela användare och grupper till ditt SAML-program.
Om ett felmeddelande visas gör du följande:

1. Kopiera och klistra in informationen i rutan **Hur ser felet ut?**.

    ![Få råd om lösning](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Välj **Hämta lösningsvägledning**. Rotorsaken och råd om lösning visas.  I det här exemplet hade användaren inte tilldelats till programmet.

3. Läs lösningsvägledningen och åtgärda problemet om möjligt.

4. Kör testet igen tills det slutförts helt.

Mer information finns i [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användarkonton](../app-provisioning/configure-automatic-user-provisioning-portal.md)
