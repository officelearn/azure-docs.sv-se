---
title: SAML-program med enkel inloggning – Microsoft Identity Platform | Microsoft Docs
description: Konfigurera enkel inloggning (SSO) till icke-Galleri program i Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cee2b9a0ea32a3b331849263c8a97f55930542d
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87379647"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Konfigurera SAML-baserad enkel inloggning till icke-galleriappar

När du [lägger till en Galleri-app](add-gallery-app.md) eller en [icke-Galleri-](add-non-gallery-app.md) WEBBAPP till dina Azure AD Enterprise-program, är ett av de alternativ för enkel inloggning som är tillgängligt för dig [SAML-baserad enkel inloggning](what-is-single-sign-on.md#saml-sso). Välj SAML när det är möjligt för program som autentiseras med ett av SAML-protokollen. Med enkel inloggning i SAML autentiserar Azure AD programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggnings informationen till programmet via ett anslutnings protokoll. Du kan mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk. I den här artikeln beskrivs hur du konfigurerar SAML-baserad enkel inloggning för en app som inte är en Galleri. 

> [!NOTE]
> Lägger du till en galleriapp? Hitta steg-för-steg-instruktioner i [listan med självstudier för SaaS-appar](../saas-apps/tutorial-list.md)

Om du vill konfigurera SAML enkel inloggning för ett program som inte är ett galleri program utan att skriva kod, måste du ha en Azure AD-prenumeration och programmet måste ha stöd för SAML 2,0. Mer information om Azure AD-versioner finns i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Innan du börjar

Om programmet inte har lagts till i din Azure AD-klient kan du läsa [Lägg till en app som inte är en Galleri](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Steg 1. Redigera den grundläggande SAML-konfigurationen

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

2. Gå till **Azure Active Directory**  >  **företags program** och välj programmet i listan. 
   
   - Om du vill söka efter programmet väljer du **alla program**i menyn **program typ** och väljer sedan **Använd**. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

3. Under avsnittet **Hantera** väljer du **enkel inloggning**. 

   - Observera att det finns vissa scenarier där alternativet för **enkel inloggning** inte är tillgängligt. Om programmet till exempel har registrerats med hjälp av **Appregistreringar** konfigureras funktionen för enkel inloggning så att den använder OIDC OAuth som standard. I det här fallet visas inte alternativet för **enkel inloggning** i navigeringen under **företags program**. När du använder **Appregistreringar** för att lägga till din anpassade app konfigurerar du alternativ i manifest filen. Mer information om manifest filen finns i ( https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) . Mer information om SSO-standarder finns i ( https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform) . Andra scenarier där **enkel inloggning** kommer att saknas i navigeringen är när ett program finns i en annan klient organisation eller om ditt konto inte har de behörigheter som krävs (global administratör, moln program administratör, program administratör eller ägare till tjänstens huvud namn). Behörigheter kan också orsaka ett scenario där du kan öppna **enkel inloggning** men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

4. Välj **SAML**. Sidan **Konfigurera enkel inloggning med SAML-Preview** visas.

   ![Steg 1 redigera den grundläggande SAML-konfigurationen](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Om du vill redigera de grundläggande konfigurations alternativen för SAML väljer du **redigerings** ikonen (en penna) i det övre högra hörnet i avsnittet **grundläggande SAML-konfiguration** .

1. Ange följande inställningar. Du bör hämta värdena från program leverantören. Du kan ange värdena manuellt eller ladda upp en metadatafil för att extrahera värdet för fälten.

    | Grundläggande konfigurations inställning för SAML | SP-initierad | idP-initierad | Description |
    |:--|:--|:--|:--|
    | **Identifierare (entitets-ID)** | Krävs för vissa appar | Krävs för vissa appar | Identifierar programmet unikt. Azure AD skickar identifieraren till programmet som målgruppsparametern för SAML-token. Programmet förväntas verifiera den. Detta värde visas även som entitets-ID i alla SAML-metadata som anges av programmet. Ange en URL som använder följande mönster: "https:// <subdomain> . contoso.com" *du hittar det här värdet som **Issuer** -element i **AuthnRequest** (SAML-begäran) som skickas av programmet.* |
    | **Svarswebbadress** | Obligatorisk | Obligatorisk | Anger var programmet förväntas ta emot SAML-token. Svars-URL:en kallas även för URL för konsumenttjänst för försäkran (ACS-URL). Du kan använda ytterligare svars-URL-fält för att ange flera svars-URL: er. Du kan till exempel behöva ytterligare svars-URL: er för flera under domäner. I test syfte kan du ange flera svars-URL: er (lokala värden och offentliga URL: er) i taget. |
    | **Inloggnings-URL** | Obligatorisk | Ange inte | När en användare öppnar den här URL:en omdirigerar tjänstleverantören till Azure AD för att autentisera och logga in användaren. Azure AD använder URL:en för att starta programmet från Office 365 eller Azure AD-åtkomstpanelen. Om det är tomt utför Azure AD IdP inloggning när en användare startar programmet från Office 365, Azure AD-åtkomst panelen eller URL: en för Azure AD SSO.|
    | **Vidarebefordransstatus** | Valfritt | Valfritt | Anger för programmet var användaren ska omdirigeras när autentiseringen har slutförts. Vanligt vis är värdet en giltig URL för programmet. Vissa program använder dock det här fältet på olika sätt. Kontakta programleverantören om du vill ha mer information.
    | **Utloggnings-URL** | Valfritt | Valfritt | Används för att skicka SAML-utloggnings Svaren tillbaka till programmet.

Mer information finns i [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Steg 2. Konfigurera användarattribut och anspråk 

När en användare autentiserar sig till programmet utfärdar Azure AD programmet en SAML-token med information (eller anspråk) om den användare som unikt identifierar dem. Som standard innehåller den här informationen användarens användar namn, e-postadress, förnamn och efter namn. Du kan behöva anpassa dessa anspråk om exempelvis programmet kräver specifika anspråks värden eller ett annat **namn** än användar namn. Kraven för Gallery-appar beskrivs i de [programspecifika självstudierna](../saas-apps/tutorial-list.md), eller så kan du be program leverantören. De allmänna stegen för att konfigurera användarattribut och anspråk beskrivs nedan.

1. I avsnittet **användarattribut och anspråk** väljer du **redigerings** ikonen (en penna) i det övre högra hörnet.

   ![Steg 2 Konfigurera användarattribut och anspråk](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verifiera **namn identifierarens värde**. Standardvärdet är *User. PrincipalName*. Användaridentifieraren identifierar unikt varje användare i programmet. Exempel: om e-postadressen är både användarnamnet och den unika identifieraren anger du värdet *user.mail*.

3. Om du vill ändra **namnet på namn identifieraren**väljer du **redigerings** ikonen (en penna) för fältet namn på **Identifier-värde** . Gör lämpliga ändringar i identifierare och källa, om det behövs. Mer information finns i [Redigera NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Spara ändringarna när du är klar. 
 
4. Om du vill konfigurera grupp anspråk väljer du **redigerings** ikonen för de **grupper som returneras i anspråks** fältet. Mer information finns i [Konfigurera grupp anspråk](../hybrid/how-to-connect-fed-group-claims.md).

5. Om du vill lägga till ett anspråk väljer du **Lägg till nytt anspråk** överst på sidan. Ange **namnet** och välj lämplig källa. Om du väljer käll- **attributet** måste du välja det **källattribut** som du vill använda. Om du väljer **översättnings** källan måste du välja den **omvandling** och **parameter 1** som du vill använda. Mer information finns i [lägga till programspecifika anspråk](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Spara ändringarna när du är klar. 

6. Välj **Spara**. Det nya anspråket visas i tabellen.

   > [!NOTE]
   > Ytterligare sätt att anpassa SAML-token från Azure AD till ditt program finns i följande resurser.
   >- Information om hur du skapar anpassade roller via Azure Portal finns i [Konfigurera roll anspråk](../develop/active-directory-enterprise-app-role-management.md).
   >- Information om hur du anpassar anspråk via PowerShell finns i [Anpassa anspråk – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Om du vill ändra applikations manifestet för att konfigurera valfria anspråk för programmet, se [Konfigurera valfria anspråk](../develop/active-directory-optional-claims.md).
   >- Om du vill ange livs längds principer för token för uppdateringstoken, åtkomsttoken, sessionstoken och ID-token, se [Konfigurera livs längd för token](../develop/active-directory-configurable-token-lifetimes.md). Eller, för att begränsa autentisering av sessioner via villkorlig åtkomst för Azure AD, se [hanterings funktioner för autentisering](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Steg 3. Hantera SAML-signerings certifikatet

Azure AD använder ett certifikat för att signera de SAML-token som skickas till programmet. Du behöver det här certifikatet för att ställa in förtroendet mellan Azure AD och programmet. Mer information om certifikat formatet finns i programmets SAML-dokumentation. Mer information finns i [Hantera certifikat för federerad enkel inloggning](manage-certificates-for-federated-single-sign-on.md) och [avancerade certifikat signerings alternativ i SAML-token](certificate-signing-options.md).

Från Azure AD kan du ladda ned det aktiva certifikatet i base64-eller RAW-format direkt från huvud sidan **Konfigurera enkel inloggning med SAML** . Du kan också hämta det aktiva certifikatet genom att ladda ned XML-filen med metadata för programmet eller genom att använda URL: en för app Federation-metadata. Följ dessa steg om du vill visa, skapa eller ladda ned dina certifikat (aktiva eller inaktiva).

1. Gå till avsnittet **SAML-signerings certifikat** . 

   ![Steg 3 hantera SAML-signerings certifikatet](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Kontrol lera att certifikatet har:

   - *Önskat förfallo datum.* Du kan konfigurera utgångs datumet för upp till tre år i framtiden.
   - *Status aktiv för det önskade certifikatet.* Om statusen är **inaktiv**ändrar du statusen till **aktiv**. Om du vill ändra statusen högerklickar du på det önskade certifikatets rad och väljer **gör certifikat aktivt**.
   - *Rätt signerings alternativ och algoritm.*
   - *Rätt e-postadress för e-post.* När det aktiva certifikatet är nära utgångs datumet skickar Azure AD ett meddelande till den e-postadress som kon figurer ATS i det här fältet.

2. Om du vill hämta certifikatet väljer du ett av alternativen för base64-format, RAW-format eller XML för federationsmetadata. Azure AD tillhandahåller även **URL: en för app Federation-Metadata** där du kan komma åt metadata som är specifika för programmet i formatet `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

3. Om du vill hantera, skapa eller importera ett certifikat väljer du **redigerings** ikonen (en penna) i det övre högra hörnet av certifikat avsnittet **SAML-signering** .

   ![SAML-signeringscertifikat](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Vidta någon av följande åtgärder:

   - Om du vill skapa ett nytt certifikat väljer du **nytt certifikat**, väljer **förfallo datum**och väljer sedan **Spara**. Om du vill aktivera certifikatet väljer du snabb menyn (**...**) och väljer **gör certifikat aktivt**.
   - Om du vill överföra ett certifikat med privat nyckel och PFX-autentiseringsuppgifter väljer du **Importera certifikat** och bläddrar till certifikatet. Ange **PFX-lösenordet**och välj sedan **Lägg till**.  
   - Använd följande alternativ för att konfigurera avancerade alternativ för certifikat signering. Beskrivningar av de här alternativen finns i artikeln [Avancerade alternativ för certifikat signering](certificate-signing-options.md) .
      - I list rutan **signerings alternativ väljer du** **signera SAML-svar**, **signera SAML-kontroll**eller **signera SAML-svar och kontroll**.
      - I list rutan **Signeringsalgoritm** väljer du **SHA-1** eller **SHA-256**.
   - Om du vill meddela fler personer när det aktiva certifikatet snart upphör att gälla, anger du e-postadresserna i fälten för **e-postadresserna för aviseringar** .

4. Om du har gjort ändringarna väljer du **Spara** längst upp i avsnittet **SAML-signerings certifikat** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Steg 4. Konfigurera programmet för att använda Azure AD

I avsnittet **Konfigurera \<applicationName> ** konfiguration visas de värden som måste konfigureras i programmet så att Azure AD används som en SAML-identitetsprovider. De värden som krävs varierar beroende på programmet. Mer information finns i programmets SAML-dokumentation. Du hittar dokumentationen genom att gå till sidan **Konfigurera \<application name> ** rubrik och välja **Visa steg-för-steg-instruktioner**. Dokumentationen visas på sidan **Konfigurera inloggning** . På den sidan får du hjälp att fylla i **inloggnings-URL**: en, **Azure AD-identifierare**och **Logga ut URL** - ** \<application name> värden i konfigurations** rubriken.

1. Rulla ned till **inställnings \<applicationName> ** avsnittet. 
   
   ![Steg 4 konfigurera programmet](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Kopiera värdet från varje rad i det här avsnittet efter behov och följ programspecifika instruktioner för att lägga till värdet i programmet. För Gallery-appar kan du Visa dokumentationen genom att välja **Visa steg-för-steg-instruktioner**. 
   - URL: en för **inloggnings** -och **utloggnings-URL: er** matchas båda till samma slut punkt, vilket är slut punkten för SAML Request-hantering för din instans av Azure AD. 
   - **Azure AD-identifieraren** är värdet för **UTFÄRDAren** i SAML-token som utfärdats till programmet.
2. När du har klistrat in alla värden i lämpliga fält väljer du **Spara**.

## <a name="step-5-validate-single-sign-on"></a>Steg 5. Verifiera enkel inloggning

När du har konfigurerat ditt program till att använda Azure AD som en SAML-baserad identitetsprovider, kan du testa inställningarna för att se om enkel inloggning fungerar för ditt konto. 

2. Bläddra till avsnittet **validera enkel inloggning med <applicationName> ** .

   ![Steg 5 validera enkel inloggning](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Välj **Verifiera**. Testningsalternativen visas.

4. Välj **Logga in som aktuell användare**. 

Om inloggningen är klar är du redo att tilldela användare och grupper till ditt SAML-program.
Om ett fel meddelande visas utför du följande steg:

1. Kopiera och klistra in informationen i rutan **Hur ser felet ut?**.

    ![Få råd om lösning](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Välj **Hämta lösnings vägledning**. Rotorsaken och råd om lösning visas.  I det här exemplet hade användaren inte tilldelats till programmet.

3. Läs lösnings vägledningen och åtgärda problemet om möjligt.

4. Kör testet igen tills det slutförts helt.

Mer information finns i [FELSÖKA SAML-baserad enkel inloggning till program i Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
