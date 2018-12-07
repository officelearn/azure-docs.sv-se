---
title: 'Självstudier: Azure Active Directory-integrering med Qlik Sense Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2018
ms.author: jeedes
ms.openlocfilehash: 24353f89f7c833a16675841f741a4a81ac34597c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015236"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Självstudier: Azure Active Directory-integrering med Qlik Sense Enterprise

Lär dig hur du integrerar Qlik Sense Enterprise med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Qlik Sense Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Qlik Sense Enterprise.
- Du kan aktivera användarna att automatiskt få loggat in på Qlik Sense Enterprise (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Qlik Sense Enterprise, behöver du följande objekt:

- En Azure AD-prenumeration
- En Qlik Sense Enterprise enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här: [– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Qlik Sense Enterprise från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Att lägga till Qlik Sense Enterprise från galleriet
Om du vill konfigurera integreringen av Qlik Sense Enterprise till Azure AD, som du behöver lägga till Qlik Sense Enterprise från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Qlik Sense Enterprise från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Qlik Sense Enterprise**väljer **Qlik Sense Enterprise** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Qlik Sense Enterprise i resultatlistan](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Qlik Sense Enterprise baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Qlik Sense Enterprise till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Qlik Sense företag upprättas.

I Qlik Sense Enterprise, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Qlik Sense Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i Qlik Sense företag som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Qlik Sense Enterprise-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Qlik Sense Enterprise:**

1. I Azure-portalen på den **Qlik Sense Enterprise** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. På den **Qlik Sense Enterprise domän och URL: er** avsnittet, utför följande steg:

    ![Qlik Sense Enterprise domän och URL: er med enkel inloggning för information](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL, identifierare och svars-URL som beskrivs senare i den här självstudien eller kontakta [Qlik Sense Enterprise Client supportteamet](https://www.qlik.com/us/services/support) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Förbered Federation Metadata-XML-filen så att du kan ladda upp som till Qlik Sense-servern.

    > [!NOTE]
    > Innan du laddar upp metadata som IDP: N till Qlik Sense-servern filen måste redigeras för att ta bort information för att fungera korrekt mellan Azure AD och Qlik Sense-servern.

    ![QlikSense][qs24]

    a. Öppna filen FederationMetaData.xml som du har hämtat från Azure-portalen i en textredigerare.

    b. Sök efter värdet **RoleDescriptor**.  Det finns fyra poster (två par inledande och avslutande prvku).

    c. Ta bort RoleDescriptor taggar och all information mellan från filen.

    d. Spara filen och förvara den närliggande för användning senare i det här dokumentet.

7. Navigera till Qlik Sense Qlik Management Console (QMC) som en användare som kan skapa virtuella proxykonfigurationer.

8. I QMC, klickar du på den **virtuella proxyservrar** menyalternativ.

    ![QlikSense][qs6]

9. Längst ned på skärmen klickar du på den **Skapa nytt** knappen.

    ![QlikSense][qs7]

10. Redigeringsskärmen virtuella proxy visas.  Är en meny för att göra alternativ visas på höger sida av skärmen.

    ![QlikSense][qs9]

11. Ange identitetsinformationen för den virtuella Azure-proxykonfigurationen med menyalternativet identifiering kontrolleras.

    ![QlikSense][qs8]  

    a. Den **beskrivning** fält är ett eget namn för den virtuella proxykonfigurationen.  Ange ett värde för en beskrivning.

    b. Den **prefixet** fältet identifierar virtuella proxy-slutpunkten för att ansluta till Qlik Sense med Azure AD enkel inloggning.  Ange ett för unikt Prefixnamn för den här virtuella proxyn.

    c. **Tidsgräns för inaktivitet session (minuter)** är tidsgränsen för anslutningar via den här virtuella proxyn.

    d. Den **Session cookie rubriknamn** är cookie-namn som lagrar sessions-ID för den Qlik Sense-sessionen som en användare får efter en lyckad autentisering.  Det här namnet måste vara unikt.

12. Klicka på menyalternativet autentisering så att den visas.  Skärmbilden autentisering visas.

    ![QlikSense][qs10]

    a. Den **anonym åtkomstläge** nedrullningsbara avgör om anonyma användare kan komma åt Qlik Sense genom den virtuella proxyn.  Standardalternativet är ingen anonym användare.

    b. Den **autentiseringsmetod** listrutan styr autentiseringsschemat virtuella proxy används.  Välj SAML från den nedrullningsbara listan.  Fler alternativ visas som ett resultat.

    c. I den **SAML URI värdfältet**, ange värdnamn användare anger för att komma åt Qlik Sense via den här virtuella SAML-proxy.  Värdnamnet är URI: n för Qlik Sense-servern.

    d. I den **SAML entitets-ID**, anger du samma värde som har angetts för fältet SAML värd URI.

    e. Den **IDP: N för SAML-metadata** är den fil som redigeras tidigare i den **redigera Federation Metadata från Azure AD-konfiguration** avsnittet.  **Innan du laddar upp metadata som IdP filen måste redigeras** att ta bort informationen för att fungera korrekt mellan Azure AD och Qlik Sense-servern.  **Se anvisningarna ovan om filen har ännu som ska redigeras.**  Om filen har redigerats klickar du på knappen Bläddra och välj metadatafilen redigerade för att överföra den till den virtuella proxykonfigurationen.

    f. Ange namn eller schemat attributreferensen för de attribut som SAML representerar den **UserID** Azure AD skickar till Qlik Sense-servern.  Referensinformation för schemat är tillgänglig i Azure app skärmar efter konfigurationen.  Om du vill använda attributet, ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Ange värdet för den **användarkatalog** som kommer att kopplas till användare när de autentiserar till Qlik Sense-servern via Azure AD.  Hårdkodade värden måste omges av **hakparentes []**.  Om du vill använda ett attribut som skickas i Azure AD SAML-försäkran, anger du namnet på attributet i den här textrutan **utan** hakparenteser.

    h. Den **SAML Signeringsalgoritm** anger service provider (i det här fallet Qlik Sense-server) om certifikatsignering för den virtuella proxykonfigurationen.  Om Qlik Sense-servern använder ett betrott certifikat som genereras med hjälp av Microsoft Enhanced RSA och AES Cryptographic Provider, ändra SAML Signeringsalgoritm till **SHA-256**.

    i. Avsnittet för SAML-attributet mappning möjliggör ytterligare attribut som grupper som ska skickas till Qlik Sense för användning i säkerhetsregler.

13. Klicka på den **Utjämning av nätverksbelastning** att göra den synlig.  Utjämning av nätverksbelastning skärmen visas.

    ![QlikSense][qs11]

14. Klicka på den **Lägg till ny servernoden** knappen, väljer motor eller flera andra noder Qlik Sense skickar sessioner för att läsa in belastningsutjämning syften och klicka på den **Lägg till** knappen.

    ![QlikSense][qs12]

15. Klicka på Avancerat-menyn kan göra den synlig. Skärmbilden visas.

    ![QlikSense][qs13]

    White värdlistan identifierar värdnamn som accepteras när du ansluter till Qlik Sense-servern.  **Ange värdnamnet som användare anger när du ansluter till Qlik Sense-servern.** Värdnamnet är samma värde som URI: n för SAML-värden utan https://.

16. Klicka på den **tillämpa** knappen.

    ![QlikSense][qs14]

17. Klicka på OK om du vill acceptera varningsmeddelandet som säger proxyservrar som är länkad till den virtuella proxyn kommer att startas om.

    ![QlikSense][qs15]

18. Associerade objekt menyn visas till höger på skärmen.  Klicka på den **proxyservrar** menyalternativ.

    ![QlikSense][qs16]

19. Proxy-skärmen visas.  Klicka på den **länk** längst ned att länka en proxy till virtuella proxyn.

    ![QlikSense][qs17]

20. Välj den proxyserver som stöder den här virtuella proxyanslutning och klicka på den **länk** knappen.  När du har länkat, kännedom proxyn under associerade proxyservrar.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Uppdatera QMC meddelandet visas efter fem till tio sekunder.  Klicka på den **uppdatera QMC** knappen.

    ![QlikSense][qs20]

22. När QMC har uppdaterats klickar du på den **virtuella proxyservrar** menyalternativ. Den nya SAML virtuella proxy posten visas i tabellen på skärmen.  Klicka på den virtuella proxy-posten.

    ![QlikSense][qs51]

23. Längst ned på skärmen aktiveras knappen Hämta SP metadata.  Klicka på den **hämta SP metadata** för att spara metadata till en fil.

    ![QlikSense][qs52]

24. Öppna filen sp metadata.  Notera den **entityID** posten och **AssertionConsumerService** posten.  De här värdena är likvärdiga med den **identifierare**, **inloggnings-URL** och **svars-URL** i konfigurationen av Azure AD-program. Klistra in dessa värden i den **Qlik Sense Enterprise domän och URL: er** avsnitt i Azure AD-programkonfiguration om de inte matchar, så bör du ersätta dem i guiden för konfiguration av Azure AD-App.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

   ![Azure Active Directory-knappen](./media/qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

   ![”Användare och grupper” och ”alla användare”-länkar](./media/qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

   ![Knappen Lägg till](./media/qliksense-enterprise-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

   ![Dialogrutan användare](./media/qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. I den **namn** skriver **BrittaSimon**.

   b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

   c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

   d. Klicka på **Skapa**.

### <a name="create-a-qlik-sense-enterprise-test-user"></a>Skapa en testanvändare Qlik Sense Enterprise

I det här avsnittet skapar du en användare som kallas Britta Simon i Qlik Sense företag. Arbeta med [Qlik Sense Enterprise Client supportteamet](https://www.qlik.com/us/services/support) att lägga till användare i Qlik Sense Enterprise-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Qlik Sense Enterprise.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Qlik Sense Enterprise, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Qlik Sense Enterprise**.

    ![Länken Qlik Sense Enterprise i listan med program](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Qlik Sense Enterprise i åtkomstpanelen du bör få automatiskt loggat in på dina Qlik Sense företagsprogram.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/qliksense-enterprise-tutorial/tutorial_general_203.png

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