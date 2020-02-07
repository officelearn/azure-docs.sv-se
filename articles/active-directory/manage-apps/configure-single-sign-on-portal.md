---
title: Konfigurera enkel inloggning – Azure Active Directory | Microsoft Docs
description: I den här självstudiekursen används Azure Portal till att konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: a415ac749d0d322bc2f71f64d4bec6e32ad1f12e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063500"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Så här konfigurerar du SAML-baserad enkel inloggning

När du har lagt till en app i Azure AD Enterprise-programmen konfigurerar du inställningar för enkel inloggning. I den här artikeln beskrivs hur du konfigurerar SAML-baserad enkel inloggning för en app som inte är en Galleri. 

> [!NOTE]
> Lägger du till en galleriapp? Hitta steg-för-steg-instruktioner i [listan med självstudier för SaaS-appar](../saas-apps/tutorial-list.md)

Om du vill konfigurera enkel inloggning för ett program som inte är ett galleri program *utan att skriva kod*, måste du ha en prenumeration eller Azure AD Premium och programmet måste ha stöd för SAML 2,0. Mer information om Azure AD-versioner finns i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Innan du börjar

- Om programmet inte har lagts till i din Azure AD-klient kan du läsa [Lägg till en Galleri app](add-gallery-app.md) eller [lägga till en app som inte är en Galleri](add-non-gallery-app.md).
- Kontakta program leverantören för att få rätt information för följande inställningar:

    | Grundläggande konfigurations inställning för SAML | SP-initierad | idP-initierad | Beskrivning |
    |:--|:--|:--|:--|
    | Identifierare (entitets-ID) | Krävs för vissa appar | Krävs för vissa appar | Identifierar unikt programmet som enkel inloggning har konfigurerats för. Azure AD skickar identifieraren till programmet som målgruppsparametern för SAML-token. Programmet förväntas verifiera den. Detta värde visas även som entitets-ID i alla SAML-metadata som anges av programmet. *Du kan hitta det här värdet som **Issuer** -element i **AuthnRequest** (SAML-begäran) som skickas av programmet.* |
    | Svars-URL | Valfri | Krävs | Anger var programmet förväntas ta emot SAML-token. Svars-URL:en kallas även för URL för konsumenttjänst för försäkran (ACS-URL). |
    | Inloggnings-URL | Krävs | Ange inte | När en användare öppnar den här URL:en omdirigerar tjänstleverantören till Azure AD för att autentisera och logga in användaren. Azure AD använder URL:en för att starta programmet från Office 365 eller Azure AD-åtkomstpanelen. Om det är tomt använder Azure AD identitets leverantören för att starta enkel inloggning när en användare startar programmet.|
    | Vidarebefordransstatus | Valfri | Valfri | Anger för programmet var användaren ska omdirigeras när autentiseringen har slutförts. Vanligt vis är värdet en giltig URL för programmet. Vissa program använder dock det här fältet på olika sätt. Kontakta programleverantören om du vill ha mer information.
    | Utloggnings-URL | Valfri | Valfri | Används för att skicka SAML-utloggnings Svaren tillbaka till programmet.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Steg 1. Redigera den grundläggande SAML-konfigurationen

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

1. Gå till **Azure Active Directory** > **företags program** och välj programmet i listan. 
   
   - Om du vill söka efter programmet väljer du **alla program**i menyn **program typ** och väljer sedan **Använd**. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

1. Under avsnittet **Hantera** väljer du **enkel inloggning**. 

1. Välj **SAML**. Sidan **Konfigurera enkel inloggning med SAML-Preview** visas.

1. Om du vill redigera de grundläggande konfigurations alternativen för SAML väljer du **redigerings** ikonen (en penna) i det övre högra hörnet i avsnittet **grundläggande SAML-konfiguration** .

     ![Konfigurera certifikat](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. I lämpliga fält anger du den information som beskrivs i avsnittet [innan du börjar](#before-you-begin) .

1. Välj **Spara**längst upp på sidan.

## <a name="step-2-configure-user-attributes-and-claims"></a>Steg 2. Konfigurera användarattribut och anspråk 

Ett program kan kräva specifika användarattribut eller anspråk i SAML-token som tas emot från Azure AD när en användare loggar in. Till exempel kan vissa URI: er eller anspråks värden krävas, eller så kan **namnet** behöva vara något annat än användar namnet som lagras i Microsoft Identity Platform. Kraven för Gallery-appar beskrivs i de [programspecifika självstudierna](../saas-apps/tutorial-list.md), eller så kan du be program leverantören. De allmänna stegen för att konfigurera användarattribut och anspråk beskrivs nedan.

1. I avsnittet **användarattribut och anspråk** väljer du **redigerings** ikonen (en penna) i det övre högra hörnet.

1. Verifiera **namn identifierarens värde**. Standardvärdet är *User. PrincipalName*. Användaridentifieraren identifierar unikt varje användare i programmet. Exempel: om e-postadressen är både användarnamnet och den unika identifieraren anger du värdet *user.mail*.

1. Om du vill ändra **namnet på namn identifieraren**väljer du **redigerings** ikonen (en penna) för fältet namn på **Identifier-värde** . Gör lämpliga ändringar i identifierare och källa, om det behövs. Mer information finns i [Redigera NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Spara ändringarna när du är klar. 
 
1. Om du vill konfigurera grupp anspråk väljer du **redigerings** ikonen för de **grupper som returneras i anspråks** fältet. Mer information finns i [Konfigurera grupp anspråk](../hybrid/how-to-connect-fed-group-claims.md).

3. Om du vill lägga till ett anspråk väljer du **Lägg till nytt anspråk** överst på sidan. Ange **namnet** och välj lämplig källa. Om du väljer käll- **attributet** måste du välja det **källattribut** som du vill använda. Om du väljer **översättnings** källan måste du välja den **omvandling** och **parameter 1** som du vill använda. Mer information finns i [lägga till programspecifika anspråk](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Spara ändringarna när du är klar. 

4. Välj **Spara**. Det nya anspråket visas i tabellen.

   > [!NOTE]
   > Ytterligare sätt att anpassa SAML-token från Azure AD till ditt program finns i följande resurser.
   >- Information om hur du skapar anpassade roller via Azure Portal finns i [Konfigurera roll anspråk](../develop/active-directory-enterprise-app-role-management.md).
   >- Information om hur du anpassar anspråk via PowerShell finns i [Anpassa anspråk – PowerShell](../develop/active-directory-claims-mapping.md).
   >- Om du vill ändra applikations manifestet för att konfigurera valfria anspråk för programmet, se [Konfigurera valfria anspråk](../develop/active-directory-optional-claims.md).
   >- Om du vill ange livs längds principer för token för uppdateringstoken, åtkomsttoken, sessionstoken och ID-token, se [Konfigurera livs längd för token](../develop/active-directory-configurable-token-lifetimes.md). Eller, för att begränsa autentisering av sessioner via villkorlig åtkomst för Azure AD, se [hanterings funktioner för autentisering](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Steg 3. Hantera SAML-signerings certifikatet

Azure AD använder ett certifikat för att signera de SAML-token som skickas till programmet. På sidan **Konfigurera enkel inloggning med SAML** kan du Visa eller hämta det aktiva certifikatet. Du kan också uppdatera, skapa eller importera ett certifikat. För Galleri program finns information om certifikat formatet i programmets SAML-dokumentation (se de [programspecifika självstudierna](../saas-apps/tutorial-list.md)). 

1. Gå till avsnittet **SAML-signerings certifikat** . Beroende på typen av program visas alternativ för att ladda ned certifikatet i base64-format, oformaterat format eller XML för federationsmetadata. Azure AD tillhandahåller även **URL: en för app Federation-Metadata** där du kan komma åt de metadata som är specifika för programmet i formatet `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

1. Om du vill hantera, skapa eller importera ett certifikat väljer du **redigerings** ikonen (en penna) i det övre högra hörnet av certifikat avsnittet **SAML-signering** och gör sedan något av följande:

   - Om du vill skapa ett nytt certifikat väljer du **nytt certifikat**, väljer **förfallo datum**och väljer sedan **Spara**. Om du vill aktivera certifikatet väljer du snabb menyn ( **...** ) och väljer **gör certifikat aktivt**.
   - Om du vill överföra ett certifikat med privat nyckel och PFX-autentiseringsuppgifter väljer du **Importera certifikat** och bläddrar till certifikatet. Ange **PFX-lösenordet**och välj sedan **Lägg till**.  
   - Använd följande alternativ för att konfigurera avancerade alternativ för certifikat signering. Beskrivningar av de här alternativen finns i artikeln [Avancerade alternativ för certifikat signering](certificate-signing-options.md) .
      - I list rutan **signerings alternativ väljer du** **signera SAML-svar**, **signera SAML-kontroll**eller **signera SAML-svar och kontroll**.
      - I list rutan **Signeringsalgoritm** väljer du **SHA-1** eller **SHA-256**.
   - Om du vill meddela fler personer när det aktiva certifikatet snart upphör att gälla, anger du e-postadresserna i fälten för **e-postadresserna för aviseringar** .

1. Välj **Spara** längst upp i avsnittet **SAML-signerings certifikat** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Steg 4. Konfigurera programmet för att använda Azure AD

I avsnittet **konfigurera \<applicationName >** visas de värden som måste konfigureras i programmet så att Azure AD används som en SAML-identitetsprovider. De värden som krävs varierar beroende på programmet. Mer information finns i programmets SAML-dokumentation.

1. Rulla ned till avsnittet **konfigurera \<applicationName >** . 
2. Kopiera värdet från varje rad i det här avsnittet efter behov och följ programspecifika instruktioner för att lägga till värdet i programmet. För Gallery-appar kan du Visa dokumentationen genom att välja **Visa steg-för-steg-instruktioner**. 
   - URL: en för **inloggnings** -och **utloggnings-URL: er** matchas båda till samma slut punkt, vilket är slut punkten för SAML Request-hantering för din instans av Azure AD. 
   - **Azure AD-identifieraren** är värdet för **UTFÄRDAren** i SAML-token som utfärdats till programmet.
1. När du har klistrat in alla värden i lämpliga fält väljer du **Spara**.

## <a name="step-5-validate-single-sign-on"></a>Steg 5. Verifiera enkel inloggning

Du är redo att testa inställningarna för att se om enkel inloggning fungerar för dig, administratören.  

1. Öppna inställningarna för enkel inloggning för programmet. 
2. Bläddra till avsnittet **Verifiera enkel inloggning med <applicationName>** . I den här självstudien kallas det här avsnittet **Konfigurera GitHub-test**.
3. Välj **test**. Testningsalternativen visas.
4. Välj **Logga in som aktuell användare**. 

Om inloggningen är klar är du redo att tilldela användare och grupper till ditt SAML-program.
Om ett fel meddelande visas utför du följande steg:

1. Kopiera och klistra in informationen i rutan **Hur ser felet ut?** .

    ![Använd rutan "Vad ser felet ut så här" för att få lösnings vägledning](media/configure-single-sign-on-portal/error-guidance.png)

1. Välj **Hämta lösnings vägledning**. Rotorsaken och råd om lösning visas.  I det här exemplet hade användaren inte tilldelats till programmet.
1. Läs lösnings vägledningen och åtgärda problemet om möjligt.
1. Kör testet igen tills det slutförts helt.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
