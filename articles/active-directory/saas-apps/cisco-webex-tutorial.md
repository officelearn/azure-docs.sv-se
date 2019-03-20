---
title: 'Självstudier: Azure Active Directory-integrering med Cisco Webex möten | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex möten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 308f745489fba2e2b539a2f2615b65228565dcf9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850017"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Självstudier: Azure Active Directory-integrering med Cisco Webex-möten

Lär dig hur du integrerar Cisco Webex möten med Azure Active Directory (AD Azure) i den här självstudien.
Integrera Cisco Webex möten med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Cisco Webex möten.
* Du kan aktivera användarna att vara automatiskt inloggad till Cisco Webex-möten (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cisco Webex möten, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex möten med enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Cisco Webex möten **SP** -initierad SSO

* Har stöd för Cisco Webex möten **Just In Time** etableringen av användare

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Att lägga till Cisco Webex möten från galleriet

För att konfigurera integrering av Cisco Webex möten i Azure AD, som du behöver lägga till Cisco Webex möten från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cisco Webex möten från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Cisco Webex möten**väljer **Cisco Webex möten** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Cisco Webex möten i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cisco Webex möten baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Cisco Webex möten ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cisco Webex möten, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Cisco Webex möten enkel inloggning](#configure-cisco-webex-meetings-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Cisco Webex möten testanvändare](#create-cisco-webex-meetings-test-user)**  – du har en motsvarighet för Britta Simon i Cisco Webex möten som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Cisco Webex möten:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Cisco Webex möten** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På Azure-portalen går du till sidan **Konfigurera enkel inloggning med SAML** och klickar på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp den nedladdade filen med **tjänstleverantörmetadata** och konfigurerar programmet med följande steg:

    >[!Note]
    >Du får Service Provider metadatafilen, som beskrivs senare i den **konfigurera Cisco Webex möten enkel inloggning** avsnittet av självstudiekursen. 

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

    ![Cisco Webex möten domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    I den **inloggnings-URL** textrutan klistra in värdet för **svars-URL** som hämtar fylls i automatiskt med SP metadata filuppladdningen.

5. Cisco Webex möten program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera**  för att lägga till attributen.

    ![image](common/edit-attribute.png)

6. Ta bort standardattribut från **användaranspråk** avsnittet och Cisco Webex möten program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Namn | Källattribut|
    | ---------------|  --------- |
    |   förnamn    | user.givenname |
    |   lastname    | user.surname |
    |   e-post       | user.mail |
    |   uid    | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. På den **konfigurera Cisco Webex möten** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Konfigurera Cisco Webex möten enkel inloggning

1. Gå till [Cisco Molnhantering samarbete](https://www.webex.com/go/connectadmin) med dina autentiseringsuppgifter för administration.

2. Gå till **säkerhetsinställningar** och gå till **Federerad Web SSO-konfiguration**.
 
    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. På den **Federerad Web SSO-konfiguration** utför följande steg:

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. Skriv namnet på ditt protokoll i textrutan Federation-protokollet.

    b. Klicka på **importera SAML-Metadata** länk för att ladda upp metadatafilen som du har hämtat från Azure-portalen.

    c. Klicka på **exportera** knappen för att hämta Metadata för Service Provider-fil och ladda upp den i den **grundläggande SAML-konfiguration** avsnittet på Azure-portalen.

    d. I den **AuthContextClassRef** textrutan typ `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` och om du vill aktivera MFA med hjälp av Azure AD anger du de två värdena som `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Välj **automatiskt Kontoskapandet**.

    >[!NOTE]
    >För att aktivera **just-in-time** användaretablering du behöver kontrollera den **automatisk Kontoskapandet**. Utöver det ska SAML-tokenattribut skickas i SAML-svar.

    f. Klicka på **Spara**. 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cisco Webex möten.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Cisco Webex möten**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Cisco Webex möten**.

    ![Cisco Webex möten länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cisco-webex-meetings-test-user"></a>Skapa Cisco Webex möten testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Cisco Webex möten. Har stöd för Cisco Webex möten **just-in-time** etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Cisco Webex möten, skapas en ny när du försöker komma åt Cisco Webex möten.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Cisco Webex möten på åtkomstpanelen, bör det vara loggas in automatiskt till Cisco Webex-möten som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

