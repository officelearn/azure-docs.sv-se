---
title: 'Självstudier: Azure Active Directory-integrering med Qlik Sense Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d5824708f6a99d0222ef5e236758b78a7eedafb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882186"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Självstudier: Azure Active Directory-integrering med Qlik Sense Enterprise

I den här självstudien lär du dig att integrera Qlik Sense Enterprise med Azure Active Directory (AD Azure) .
Genom att integrera Qlik Sense Enterprise med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Qlik Sense Enterprise.
* Du kan göra så att dina användare automatiskt loggas in på Qlik Sense Enterprise (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande saker för att konfigurera Azure AD-integrering med Qlik Sense Enterprise:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Qlik Sense Enterprise-prenumeration som är aktiverad för enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Qlik Sense Enterprise har stöd för **SP**-initierad enkel inloggning

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Lägga till Qlik Sense Enterprise från galleriet

För att konfigurera integreringen av Qlik Sense Enterprise med Azure AD behöver du lägga till Qlik Sense Enterprise från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Qlik Sense Enterprise från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Qlik Sense Enterprise**, väljer **Qlik Sense Enterprise** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Qlik Sense Enterprise i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD för Qlik Sense Enterprise baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Qlik Sense Enterprise upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för Qlik Sense Enterprise:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Qlik Sense Enterprise](#configure-qlik-sense-enterprise-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Qlik Sense Enterprise-testanvändare](#create-qlik-sense-enterprise-test-user)** – för att ha en motsvarighet till Britta Simon i Qlik Sense Enterprise som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Qlik Sense Enterprise:

1. På [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Qlik Sense Enterprise** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Qlik Sense Enterprise-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. I textrutan **Svars-URL** anger du en URL med följande mönster:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med de faktisk inloggnings-URL, identifierare och svars-URL, som beskrivs senare i den här självstudien, eller kontakta [supportteamet för Qlik Sense Enterprise-klienten](https://www.qlik.com/us/services/support) för att hämta värdena.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-single-sign-on"></a>Konfigurera enkel inloggning för Qlik Sense Enterprise

1. Förbered Federation Metadata-XML-filen så att du kan ladda upp den till Qlik Sense-servern.

    > [!NOTE]
    > Innan du laddar upp IdP-metadata till Qlik Sense-servern måste filen redigeras med informationsborttagning så att funktionen mellan Azure AD och Qlik Sense-servern fungerar korrekt.

    ![QlikSense][qs24]

    a. Öppna den FederationMetaData.xml-fil som du har laddat ned från Azure-portalen i ett redigeringsprogram.

    b. Sök efter värdet **RoleDescriptor**.  Det finns fyra poster (två par inledande och avslutande elementtaggar).

    c. Ta bort RoleDescriptor-taggarna och all information mellan dem från filen.

    d. Spara filen och ha den till hands för användning senare i det här dokumentet.

2. Gå till Qlik Sense Qlik Management Console (QMC) som en användare som kan skapa konfigurationer för virtuell proxy.

3. I QMC klickar du på menykommandot **Virtual Proxies** (Virtuella proxyservrar).

    ![QlikSense][qs6]

4. Längst ned på skärmen klickar du på knappen **Skapa ny**.

    ![QlikSense][qs7]

5. Redigeringsskärmen för virtuell proxy visas.  På skärmens högra sida finns en meny där du kan göra konfigurationsalternativ synliga.

    ![QlikSense][qs9]

6. Med menyalternativet Identification (Identifiering) markerat anger du den identifierande informationen för konfigurationen av virtuell Azure-proxy.

    ![QlikSense][qs8]  

    a. Fältet **Beskrivning** är ett eget namn för konfigurationen av virtuell proxy.  Ange ett värde för en beskrivning.

    b. Fältet **Prefix** identifierar den slutpunkten för virtuell proxy för anslutning till Qlik Sense via enkel inloggning med Azure AD.  Ange ett för unikt prefixnamn för den här virtuella proxyn.

    c. **Session inactivity timeout (minutes)** (Tidsgräns för sessionsinaktivitet (minuter) är tidsgränsen för anslutningar via den här virtuella proxyn.

    d. **Session cookie header name** (Namn på huvud för sessionscookie) är det cookienamn som lagrar sessionsidentifieraren för den Qlik Sense-session som en användare får efter en lyckad autentisering.  Det här namnet måste vara unikt.

7. Klicka på menyalternativet Autentisering så att det visas.  Skärmen Autentisering visas.

    ![QlikSense][qs10]

    a. Listrutan **Anonymous access mode** (Läge för anonym åtkomst) avgör huruvida anonyma användare kan komma åt Qlik Sense genom den virtuella proxyn.  Standardalternativet är No anonymous user (Ingen anonym användare).

    b. Listan **Autentiseringsmetod** bestämmer vilket autentiseringsschema som används för den virtuella proxyn.  Välj SAML i listrutan.  Då visas fler alternativ.

    c. I **URI-fältet för SAML-värd** anger du det värdnamn som användare anger för att komma åt Qlik Sense via den här virtuella SAML-proxyn.  Värdnamnet är URI:n för Qlik Sense-servern.

    d. I **SAML-entitets-ID** anger du samma värde som angavs för URI-fältet för SAML-värd.

    e. **IdP-metadata för SAML** är den fil som redigerades tidigare i avsnittet om att **redigera Federation Metadata från Azure AD-konfiguration**.  **Innan du laddar upp IdP-metadata måste filen redigeras** med informationsborttagning så att funktionen mellan Azure AD och Qlik Sense-servern fungerar korrekt.  **Se anvisningarna ovan om filen inte har redigerats ännu.**  Om filen har redigerats klickar du på knappen Bläddra och väljer den redigereade metadatafilen för att ladda upp den till konfigurationen för virtuell proxy.

    f. Ange attributnamn eller schemareferens för det SAML-attribut som representerar det **Användar-ID** som Azure AD skickar till Qlik Sense-servern.  Information om schemareferens finns i på Azure-appens skärmar efter konfiguration.  För att använda namnattributet anger du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Ange värdet för den **användarkatalog** som kommer att kopplas till användare när de autentiserar till Qlik Sense-servern via Azure AD.  Hårdkodade värden måste omges av **hakparenteser []**.  För att använda ett attribut som skickas i Azure AD SAML-försäkran anger du namnet på attributet i den här textrutan **utan** hakparenteser.

    h. **SAML-signeringsalgoritmen** anger certifikatsignering för serviceprovidern (i det här fallet Qlik Sense-servern) för konfigurationen av virtuell proxy.  Om Qlik Sense-servern använder ett betrott certifikat som genererats med hjälp av Microsoft Enhanced RSA and AES Cryptographic Provider ändrar du SAML-signeringsalgoritmen till **SHA-256**.

    i. Avsnittet för SAML-attributmappning möjliggör ytterligare attribut såsom grupper som ska skickas till Qlik Sense för användning i säkerhetsregler.

8. Klicka på menyalternativet **LOAD BALANCING** (Belastningsutjämning) så att det visas.  Skärmen för belastningsutjämning visas.

    ![QlikSense][qs11]

9. Klicka på knappen **Add new server node** (Lägg till ny servernod), välj motornod eller noder som Qlik Sense skickar sessioner till i belastningsutjämningssyfte och klicka på knappen **Lägg till**.

    ![QlikSense][qs12]

10. Klicka på menyalternativet Avancerat så att det visas. Skärmen Avancerat visas.

    ![QlikSense][qs13]

    Listan över tillåtna värdar identifierar värdnamn som accepteras vid anslutning till Qlik Sense-servern.  **Ange det värdnamn som användare anger när de ansluter till Qlik Sense-servern.** Värdnamnet är samma värde som SAML-värd-URI utan https://.

11. Klicka på knappen **Använd**.

    ![QlikSense][qs14]

12. Klicka på OK för att acceptera det varningsmeddelande där det står att proxyservrar som länkas till den virtuella proxyn kommer att startas om.

    ![QlikSense][qs15]

13. Menyn för associerade objekt visas till höger på skärmen.  Klicka på menyalternativ **Proxyservrar**.

    ![QlikSense][qs16]

14. Proxy-skärmen visas.  Klicka på knappen **Länka** längst ned för att länka en proxy till den virtuella proxyn.

    ![QlikSense][qs17]

15. Välj den proxynod som kommer att stödja den här virtuella proxyanslutningen och klicka på knappen **Länka**.  När du har länkat visas proxyn under associerade proxyservrar.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Efter cirka fem till tio sekunder visas meddelandet Refresh QMC (Uppdatera QMC).  Klicka på knappen **Refresh QMC** (Uppdatera QMC).

    ![QlikSense][qs20]

17. När QMC uppdateras klickar du på menykommandot **Virtual proxies** (Virtuella proxyservrar). Den nya posten för virtuell SAML-proxy visas i tabellen på skärmen.  Klicka en gång på posten för virtuell proxy.

    ![QlikSense][qs51]

18. Längst ned på skärmen aktiveras knappen Download SP metadata (Ladda ned SP-metadata).  Klicka på knappen **Download SP metadata** (Ladda ned SP-metadata) för att spara metadata till en fil.

    ![QlikSense][qs52]

19. Öppna sp-metadata-filen.  Notera posten **entityID** och posten **AssertionConsumerService**.  De här värdena är likvärdiga med **identifieraren**, **inloggnings-URL** och **svars-URL** i Azure AD-programkonfigurationen. Klistra in dessa värden i avsnittet **Qlik Sense Enterprise Domain and URLs** (domäner och URL:er för Qlik Sense Enterprise) i Azure AD-programkonfigurationen. Om de inte matchar bör du ersätta dem i guiden för konfiguration av Azure AD-app.

    ![QlikSense][qs53]

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Qlik Sense Enterprise.

1. I Azure-portalen väljer du **Företagsprogram** följt av **Alla program** och sedan **Qlik Sense Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan anger du och väljer **Qlik Sense Enterprise**.

    ![Qlik Sense Enterprise-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-qlik-sense-enterprise-test-user"></a>Skapa Qlik Sense Enterprise-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Qlik Sense Enterprise. Kontakta  [supportteamet för Qlik Sense Enterprise](https://www.qlik.com/us/services/support) för att lägga till användarna på Qlik Sense Enterprise-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Qlik Sense Enterprise-panelen i åtkomstpanelen bör du automatiskt loggas in på Qlik Sense Enterprise som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

