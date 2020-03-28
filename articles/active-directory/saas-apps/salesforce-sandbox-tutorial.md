---
title: 'Självstudiekurs: Azure Active Directory-integrering med Salesforce Sandbox | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76290011"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Salesforce Sandbox

I den här självstudien får du lära dig hur du integrerar Salesforce Sandbox med Azure Active Directory (Azure AD). När du integrerar Salesforce Sandbox med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Salesforce Sandbox.
* Gör att användarna automatiskt loggas in i Salesforce Sandbox med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Salesforce Sandbox enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Salesforce Sandbox stöder **SP och IDP** initierade SSO
* Salesforce Sandbox stöder just **in time-användares** etablering
* Salesforce Sandbox stöder [ **automatiserad** användaretablering](salesforce-sandbox-provisioning-tutorial.md)
* När du har konfigurerat Salesforce Sandbox kan du framtvinga sessionskontroller som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Lägga till Salesforce Sandbox från galleriet

Om du vill konfigurera integreringen av Salesforce Sandbox i Azure AD måste du lägga till Salesforce Sandbox från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Salesforce Sandbox** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Salesforce Sandbox** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Konfigurera och testa Azure AD enkel inloggning för Salesforce Sandbox

Konfigurera och testa Azure AD SSO med Salesforce Sandbox med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Salesforce Sandbox.

Om du vill konfigurera och testa Azure AD SSO med Salesforce Sandbox slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Salesforce Sandbox SSO](#configure-salesforce-sandbox-sso)** – för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa Salesforce Sandbox-testanvändare](#create-salesforce-sandbox-test-user)** – om du vill ha en motsvarighet till B.Simon i Salesforce Sandbox som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Salesforce Sandbox-program** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du har metadatafil för **Service Provider** och vill konfigurera i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    > [!NOTE]
    > Du får metadatafilen för tjänsteleverantören från administrationsportalen för Salesforce Sandbox, som förklaras senare i självstudien.

    c. När metadatafilen har överförts fylls värdet **för svars-URL-värdet** i textrutan **Svara på URL.**

    ![image](common/both-replyurl.png)

    > [!Note]
    > Om värdet **svara på webbadressen** inte blir automatiskt polulated fyller du i värdet manuellt enligt dina krav.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Salesforce Sandbox.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce Sandbox.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Salesforce Sandbox**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-salesforce-sandbox-sso"></a>Konfigurera Salesforce Sandbox SSO

1. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce Sandbox-administratörskonto.

2. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

3. Bläddra ned till **INSTÄLLNINGAR** i det vänstra navigeringsfönstret och klicka på **Identitet** för att expandera det relaterade avsnittet. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

5. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klicka på **Välj fil** för att ladda upp XML-filen med metadata som du har laddat ned från Azure-portalen och klicka på **Skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. På sidan med **inställningar för enkel inloggning med SAML** fylls fälten i automatiskt. Klicka på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. På sidan **Inställningar för enkel inloggning** klickar du på knappen Hämta **metadata** för att hämta metadatafilen för tjänsteleverantören. Använd den här filen i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen för att konfigurera nödvändiga url:er enligt beskrivningen ovan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure4.png)

10. Om du vill konfigurera **SP** programmet i sp-initierat läge följer följande förutsättningar för detta:

    a. Du bör ha en verifierad domän.

    b. Du måste konfigurera och aktivera din domän på Salesforce Sandbox, steg för detta förklaras senare i den här självstudien.

    c. Klicka på **Ange ytterligare webbadresser** i avsnittet **Grundläggande SAML-konfiguration i Azure-portalen:**
  
    ![Salesforce Sandbox Domain och URL:er enkel inloggningsinformation](common/both-signonurl.png)

    Skriv värdet med följande mönster i **textrutan Sign-on URL:**`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Det här värdet ska kopieras från Salesforce Sandbox-portalen när du har aktiverat domänen.

11. Klicka på **XML för federationsmetadata** i avsnittet **SAML-signeringscertifikat** och spara xml-filen på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

12. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce Sandbox-administratörskonto.

13. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

14. Bläddra ned till **INSTÄLLNINGAR** i det vänstra navigeringsfönstret och klicka på **Identitet** för att expandera det relaterade avsnittet. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

16. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klicka på **Välj fil** om du vill ladda upp metadata-XML-filen och klicka på **Skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. På sidan **Saml-inställningar för enkel inloggning** fyller fälten automatiskt, skriver du namnet på konfigurationen (till *exempel: SPSSOWAAD_Test*), i textrutan **Namn** och klickar på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Så här aktiverar du domänen i Salesforce Sandbox:

    > [!NOTE]
    > Innan du aktiverar domänen måste du skapa samma på Salesforce Sandbox. Mer information finns i [Definiera ditt domännamn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). När domänen har skapats kontrollerar du att den är korrekt konfigurerad.

21. I det vänstra navigeringsfönstret i Salesforce Sandbox klickar du på **Företagsinställningar** för att expandera det relaterade avsnittet och klickar sedan på **Min domän**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Klicka på **Redigera**i avsnittet **Autentiseringskonfiguration** .

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. I avsnittet **Autentiseringskonfiguration,** som **autentiseringstjänst,** väljer du namnet på den SAML-inställning för enkel inloggning som du har angett under SSO-konfigurationen i Salesforce Sandbox och klickar på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Skapa Salesforce Sandbox-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Salesforce Sandbox. Salesforce Sandbox stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox skapas en ny när du försöker komma åt Salesforce Sandbox. Salesforce Sandbox stöder också automatisk användaretablering, du kan hitta mer information [här](salesforce-sandbox-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Salesforce Sandbox på åtkomstpanelen bör du automatiskt loggas in i den Salesforce Sandbox som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Salesforce Sandbox med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Konfigurera etablering av användare](salesforce-sandbox-provisioning-tutorial.md)

- [Så här skyddar du Salesforce Sandbox med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
