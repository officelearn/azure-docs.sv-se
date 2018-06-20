---
title: 'Självstudier: Azure Active Directory-integrering med Qlik mening Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Qlik mening Enterprise.
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
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 09e1850956150eab6bd7fea365a17c12fa15bdc3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230108"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Självstudier: Azure Active Directory-integrering med Qlik mening Enterprise

I kursen får lära du att integrera Qlik mening Enterprise med Azure Active Directory (AD Azure).

Integrera Qlik mening Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Qlik mening Enterprise.
- Du kan aktivera användarna att automatiskt hämta loggat in på Qlik mening Enterprise (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Qlik mening Enterprise, behöver du följande:

- En Azure AD-prenumeration
- En Qlik mening Enterprise enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Qlik mening Enterprise från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Att lägga till Qlik mening Enterprise från galleriet
Du måste lägga till Qlik mening Enterprise från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Qlik mening Enterprise i Azure AD.

**Utför följande steg för att lägga till Qlik mening Enterprise från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Qlik mening Enterprise**väljer **Qlik mening Enterprise** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Qlik mening i resultatlistan företag](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Qlik mening Enterprise baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Qlik mening företag till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Qlik mening Enterprise upprättas.

I Qlik mening Enterprise, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Qlik mening Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Qlik mening Enterprise](#create-a-qlik-sense-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i Qlik mening företag som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Qlik mening Enterprise-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Qlik mening Enterprise:**

1. I Azure-portalen på den **Qlik mening Enterprise** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. På den **Qlik mening företagsdomänen och URL: er** avsnittet, utför följande steg:

    ![URL: er och Qlik mening företagsdomänen enkel inloggning information](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Observera avslutande snedstreck i slutet av den här URI. Det krävs.
    
    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare, som beskrivs senare i den här kursen eller kontakta [Qlik mening Enterprise-klienten supportteamet](https://www.qlik.com/us/services/support) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Förbereda Federation Metadata XML-filen så att du kan ladda upp som Qlik mening servern.
   
    > [!NOTE]
    > Innan du laddar upp IdP metadata till servern Qlik mening filen måste redigeras för att ta bort information för att säkerställa att programmet fungerar mellan Azure AD och Qlik mening server.
    
    ![QlikSense][qs24]
   
    a. Öppna filen FederationMetaData.xml som du har hämtat från Azure-portalen i en textredigerare.
   
    b. Sök efter värdet **RoleDescriptor**.  Det finns fyra poster (två par inledande och avslutande elementtaggar).
   
    c. Ta bort RoleDescriptor-taggar och all information mellan från filen.
   
    d. Spara filen och spara den i närheten för användning senare i det här dokumentet.

7. Navigera till Qlik meningsfullt Qlik Management Console (QMC) som en användare som kan skapa virtuella proxykonfigurationer.

8. I QMC, klickar du på den **virtuella proxyservrar** menyalternativet.
   
    ![QlikSense][qs6] 

9. Längst ned på skärmen klickar du på den **Skapa nytt** knappen.
   
    ![QlikSense][qs7]

10. Den virtuella proxy redigera visas.  Är en meny för att göra konfigurationsalternativ visas till höger på skärmen.
   
    ![QlikSense][qs9]

11. Ange identitetsinformationen för den virtuella Azure-proxykonfigurationen med menyalternativet identifiering kontrolleras.
    
    ![QlikSense][qs8]  
    
    a. Den **beskrivning** fältet är ett eget namn för den virtuella proxykonfigurationen.  Ange ett värde för en beskrivning.
    
    b. Den **prefixet** identifierar fältet virtuella proxy-slutpunkten för att ansluta till Qlik mening med Azure AD enkel inloggning.  Ange ett unikt prefix-namn för den här virtuella proxy.
    
    c. **Tidsgräns för inaktivitet (minuter)** är tidsgränsen för anslutningar via den här virtuella proxy.
    
    d. Den **sessionsnamnet cookie-huvud** är cookie-namn som lagrar sessions-ID för en användare tar emot efter en lyckad autentisering Qlik mening sessionen.  Det här namnet måste vara unikt.

12. Klicka på menyalternativet autentisering för att göra den synlig.  Autentisering visas.
    
    ![QlikSense][qs10]
    
    a. Den **anonym åtkomstläge** nedrullningsbara avgör om anonyma användare kan komma åt Qlik mening genom den virtuella proxyn.  Standardalternativet är ingen anonym användare.
    
    b. Den **autentiseringsmetod** nedrullningsbara styr autentiseringsschema virtuella proxy används.  Välj SAML från den nedrullningsbara listan.  Fler alternativ visas som ett resultat.
    
    c. I den **SAML URI värdfältet**, ange värdnamnet användare anger för att komma åt Qlik mening via den här virtuella SAML-proxyn.  Värdnamnet är URI: n för Qlik mening-servern.
    
    d. I den **SAML enhets-ID**, ange samma värde som angetts för fältet SAML värden URI.
    
    e. Den **SAML IdP metadata** är filen redigeras tidigare i den **redigera Federation Metadata från Azure AD-konfiguration** avsnitt.  **Innan du laddar upp IdP-metadata i filen måste redigeras** att ta bort informationen om du vill säkerställa att programmet fungerar mellan Azure AD och Qlik mening server.  **Se anvisningarna ovan om filen är ännu som ska redigeras.**  Om filen har redigerats klickar du på knappen Bläddra och välj den redigerade metadatafilen att överföra den till den virtuella proxykonfigurationen.
    
    f. Ange namn eller schemat attributreferensen för SAML-attributet representerar den **UserID** Azure AD skickar till servern Qlik mening.  Referensinformation för schemat finns i Azure app skärmar efter konfiguration.  Om du vill använda namnattributet ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Ange värdet för den **användarkatalog** som ska anslutas till användarna när de autentiserar till Qlik mening server via Azure AD.  Hårdkodad värden måste omges av **hakparentes []**.  Om du vill använda ett attribut som skickas i Azure AD SAML-kontroll, ange namnet på attributet i den här textrutan **utan** hakparenteser.
    
    h. Den **SAML Signeringsalgoritm** anger service provider (i det här fallet Qlik mening server) om certifikatsignering för den virtuella proxykonfigurationen.  Om Qlik mening servern använder ett betrott certifikat som genereras med hjälp av Microsoft Enhanced RSA och AES Cryptographic Provider, ändra SAML Signeringsalgoritm till **SHA-256**.
    
    i. Mappning med avsnittet SAML attribut kan för ytterligare attribut som grupper som ska skickas till Qlik mening för användning i säkerhetsregler.

13. Klicka på den **BELASTNINGSUTJÄMNING** att göra den synlig.  Skärmbilden belastningsutjämning visas.
    
    ![QlikSense][qs11]

14. Klicka på den **Lägg till ny servernoden** knappen, Välj motorn eller flera andra noder Qlik mening skickar sessioner för Utjämning av nätverksbelastning ändamål och klicka på den **Lägg till** knappen.
    
    ![QlikSense][qs12]

15. Klicka på menyalternativet avancerade att göra den synlig. Skärmbilden visas.
    
    ![QlikSense][qs13]
    
    Värden vitlista identifierar värdnamn som accepteras när du ansluter till servern Qlik mening.  **Ange värdnamnet användarna anger vid anslutning till Qlik mening server.** Värdnamnet är samma värde som URI: n för SAML-värden utan https://.

16. Klicka på den **tillämpa** knappen.
    
    ![QlikSense][qs14]

17. Klicka på OK om du vill acceptera varningsmeddelandet om proxyservrar som är kopplad till virtuella proxyservern kommer att startas om.
    
    ![QlikSense][qs15]

18. Associerade objekt menyn visas till höger på skärmen.  Klicka på den **proxyservrar** menyalternativet.
    
    ![QlikSense][qs16]

19. Proxy visas.  Klicka på den **länk** längst ned att länka en proxy till virtuella proxy.
    
    ![QlikSense][qs17]

20. Välj den proxy-nod som stöder den här virtuella proxyanslutningen och klicka på den **länk** knappen.  När du länkar, visas proxy under associerade proxyservrar.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Uppdatera QMC meddelandet visas efter fem till tio sekunder.  Klicka på den **uppdatera QMC** knappen.
    
    ![QlikSense][qs20]

22. När QMC uppdaterar, klicka på den **virtuella proxyservrar** menyalternativet. Den nya SAML virtuella proxy posten visas i tabellen på skärmen.  Klicka på posten virtuella proxy.
    
    ![QlikSense][qs51]

23. Längst ned på skärmen aktiveras knappen ladda ned SP metadata.  Klicka på den **hämta SP metadata** för att spara metadata till en fil.
    
    ![QlikSense][qs52]

24. Öppna filen sp metadata.  Se den **ID för entiteterna** transaktionen och **AssertionConsumerService** transaktionen.  Värdena är likvärdiga med den **identifierare** och **inloggning URL** i Azure AD tillämpningsprogrammets konfiguration. Klistra in dessa värden i den **Qlik mening företagsdomänen och URL: er** avsnitt i Azure AD-programkonfigurationen om de matchar inte varandra, och sedan bör du ersätta dem i guiden för konfiguration av Azure AD-App.
    
    ![QlikSense][qs53]

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

   ![Azure Active Directory-knappen](./media/qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

   ![”Användare och grupper” och ”alla användare” länkar](./media/qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

   ![Knappen Lägg till](./media/qliksense-enterprise-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

   ![Dialogrutan användare](./media/qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. I den **namn** skriver **BrittaSimon**.

   b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

   c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

   d. Klicka på **Skapa**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Skapa en testanvändare Qlik mening Enterprise

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Qlik mening Enterprise. Arbeta med [Qlik mening Enterprise-klienten supportteamet](https://www.qlik.com/us/services/support) att lägga till användare i Qlik mening Enterprise-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Qlik mening Enterprise.

![Tilldela rollen][200] 

**Om du vill tilldela Qlik mening Enterprise Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Qlik mening Enterprise**.

    ![Länken Qlik mening Enterprise i listan med program](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Qlik mening Enterprise på åtkomstpanelen du ska hämta automatiskt loggat in på ditt Qlik mening företagsprogram. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

