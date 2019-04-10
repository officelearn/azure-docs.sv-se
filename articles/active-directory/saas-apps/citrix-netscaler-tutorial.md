---
title: 'Självstudier: Azure Active Directory-integrering med Citrix Netscaler | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Citrix Netscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281683"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Självstudier: Azure Active Directory-integrering med Citrix Netscaler

I den här självstudien får lära du att integrera Citrix Netscaler med Azure Active Directory (AD Azure).
Integrera Citrix Netscaler med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Citrix Netscaler.
* Du kan aktivera användarna att vara automatiskt inloggad till Citrix Netscaler (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Citrix Netscaler, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Citrix Netscaler enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Citrix Netscaler stöder **SP** -initierad SSO

* Citrix Netscaler stöder **Just In Time** etableringen av användare

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Att lägga till Citrix Netscaler från galleriet

För att konfigurera integrering av Citrix Netscaler i Azure AD, som du behöver lägga till Citrix Netscaler från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Citrix Netscaler från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Citrix Netscaler**väljer **Citrix Netscaler** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Citrix Netscaler i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Citrix Netscaler baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Citrix Netscaler upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Citrix Netscaler, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Citrix Netscaler enkel inloggning](#configure-citrix-netscaler-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare Citrix Netscaler](#create-citrix-netscaler-test-user)**  – du har en motsvarighet för Britta Simon i Citrix Netscaler som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Citrix Netscaler:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Citrix Netscaler** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Citrix Netscaler domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<<Your FQDN>>`

    c. I den **svars-URL (försäkran URL för Konsumenttjänst)** text skriver en URL med hjälp av följande mönster: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Citrix Netscaler klienten supportteamet](https://www.citrix.com/contact/technical-support.html) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    > [!NOTE]
    > Dessa URL: er måste vara tillgängliga från offentliga webbplatser för att få enkel inloggning fungerar. Du måste aktivera brandväggen eller andra säkerhetsinställningar på Netscaler sida för att enble Azure AD för att publicera token till konfigurerade ACS-URL.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Citrix Netscaler** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-citrix-netscaler-single-sign-on"></a>Konfigurera Citrix Netscaler enkel inloggning

1. I ett annat webbläsarfönster inloggning till Citrix Netscaler-klienten som administratör.

2. Se till att den **NetScaler Version på inbyggd programvara = NS12.1: Skapa 48.13.nc**.

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure01.png)

3. På den **VPN Virtual Server** utför följande steg:

     ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure02.png)

    a. Ange Gatewayinställningar **ICA endast** som **SANT**.
    
    b. Ange **aktivera autentisering** som **SANT**.
    
    c. **DTLS** är valfritt.
    
    d. Se till att **SSLv3** som **inaktiverad**.

4. En anpassad **SSL-chiffer** gruppen har skapats för att uppnå A + på https://www.ssllabs.com enligt nedan:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure03.png)

5. På den **konfigurera SAML autentiseringsservern** utför följande steg:

      ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure04.png)

    a. I den **namn** textrutan skriver du namnet på din server.

    b. I den **omdirigerings-URL** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I den **URL för enkel utloggning** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen.

    d. I **IDP certifikatnamn**, klickar du på den **”+”** logga för att lägga till certifikatet som du har hämtat från Azure-portalen. När paketet har överförts Välj certifikatet i listrutan.

    e. Efter flera fält måste anges på den här sidan

      ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure24.png)

    f. Välj **begärt Autentiseringskontext** som **exakta**.

    g. Välj **signaturalgoritm** som **RSA-SHA256**.

    h. Välj **sammanfattad metoden** som **SHA256**.

    i. Kontrollera **framtvinga användarnamn**.

    j. Klicka på **OK**

6. Så här konfigurerar du den **Session profil**, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure06.png)

    a. I den **namn** textrutan skriver du namnet på din session-profil.

    b. På den **klientmiljö** fliken, göra önskade ändringar som visas i skärmbilden nedan.

    c. Fortsätt med ändringarna på den **fliken Allmänt** enligt nedan och klicka på **OK**

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure07.png)

    d. På den **publicerade program** fliken, gör ändringar som visas i skärmbilden nedan och klickar på **OK**.

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure08.png)

    e. På den **Security** fliken, gör ändringar som visas i skärmbilden nedan och klickar på **OK**.

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure09.png)

7. Skapa ICA-anslutningar som ansluter på sessionen tillförlitlighet Port **2598** enligt den skärmbilden nedan.

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure10.png)

8. På den **SAML** lägger du till den **servrar** som visas i skärmbilden nedan.

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure11.png)

9. På den **SAML** lägger du till den **principer** som visas i skärmbilden nedan.

     ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure12.png)

10. På den **globala inställningar** går du till den **binds åtkomst** avsnittet.

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure13.png)

11. På den **Configuration** fliken, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure14.png)

    a. Välj **Tillåt domäner**.

    b. I den **domännamn** textrutan väljer domänen.

    c. Klicka på **OK**.

12. Kontrollera den **StoreFront** inställningarna på den **mottagare för Web Sites** som visas i skärmbilden nedan:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure15.png)

13. På den **hantera autentiseringsmetoder - Corp** popup, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure16.png)

    a. Välj **användarnamn och lösenord**.

    b. Välj **direkt från NetScaler Gateway**.

    c. Klicka på **OK**.

14. På den **konfigurera betrodda domäner** popup, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure17.png)

    a. Välj **betrodda domäner endast**.

    b. Klicka på **Lägg till** att lägga till din domän i **betrodda domäner** textrutan.

    c. Välj standardprincipen för domäner från din **standarddomän** lista.

    d. Välj **Show domänlistan i inloggningssidan**.

    e. Klicka på **OK**.

15. På den **hantera NetScaler Gateways** popup, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure18.png)

    a. Klicka på **Lägg till** att lägga till dina NetScaler-gatewayer i **NetScaler gatewayer** textrutan.

    b. Klicka på **Stäng**.

16. På den **StoreFront allmänna inställningar för** fliken, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure19.png)

    a. I den **visningsnamn** textrutan skriver du namnet på din NetScaler-Gateway.

    b. I den **NetScaler gatewayens Webbadress** textrutan skriver din NetScaler Gateway-URL.

    c. Välj **användning eller roll** som **autentisering och HDX routning**.

    d. Klicka på **OK**.

17. På den **StoreFront säkra biljett utfärdare** fliken, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure20.png)

    a. Klicka på **Lägg till** för att lägga till din **säkra biljett utfärdare URL: er** i textrutan.

    b. Välj **aktivera session tillförlitlighet**.

    c. Klicka på **OK**.

18. På den **StoreFront autentiseringsinställningar** fliken, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure21.png)

    a. Välj din **Version**.

    b. Välj **inloggningstyp** som **domän**.

    c. Ange din **Motringnings-URL för**.

    d. Klicka på **OK**.

19. På den **StoreFront distribuera Citrix mottagare** fliken, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure22.png)

    a. Välj **distributionsalternativet** som **Använd mottagare för HTML5 om lokala mottagare är tillgänglig**.

    b. Klicka på **OK**.

20. På den **hantera Beacons** popup, utför följande steg:

    ![Konfigurera enkel inloggning](./media/citrix-netscaler-tutorial/configure23.png)

    a. Välj den **interna beacon** som **använder tjänstens URL**.

    b. Klicka på **Lägg till** att lägga till din URL i den **externa beacons** textrutan.

    c. Klicka på **OK**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Citrix Netscaler.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Citrix Netscaler**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Citrix Netscaler**.

    ![Citrix Netscaler-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-citrix-netscaler-test-user"></a>Skapa Citrix Netscaler testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Citrix Netscaler. Citrix Netscaler stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Citrix Netscaler, skapas en ny efter autentisering.

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [Citrix Netscaler klienten supportteamet](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Citrix Netscaler i åtkomstpanelen, bör det vara loggas in automatiskt till den Citrix Netscaler som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

