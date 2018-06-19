---
title: 'Självstudier: Azure Active Directory-integrering med Pega system | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Pega system och Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: e56a738c22d763bfc85e5223fa6c4640ebf35092
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968880"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Självstudier: Azure Active Directory-integrering med Pega system

I kursen får lära du att integrera Pega system med Azure Active Directory (AD Azure).

Integrera Pega system med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Pega system.
- Du kan aktivera användarna att automatiskt hämta loggat in på Pega system (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pega system, behöver du följande:

- En Azure AD-prenumeration
- Ett system för Pega enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Pega system från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pega-systems-from-the-gallery"></a>Att lägga till Pega system från galleriet
Du måste lägga till Pega system från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Pega system i Azure AD.

**Utför följande steg för att lägga till Pega system från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Pega system**väljer **Pega system** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Pega system i resultatlistan](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Pega system baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Pega system motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Pega system upprättas.

I Pega system, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pega system, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Pega system](#create-a-pega-systems-test-user)**  – du har en motsvarighet för Britta Simon i Pega system som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Pega system.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Pega system:**

1. I Azure-portalen på den **Pega system** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. På den **Pega system domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Pega system domän med enkel inloggning information](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Pega system domän med enkel inloggning information](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    I den **Relay tillstånd** textruta Skriv en URL med följande mönster: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare, Reply URL och Relay tillstånd URL. Du kan hitta värdena för identifierare och Reply URL från Pega program som beskrivs senare i den här kursen. Relay tillstånd, kontakta [Pega system klienten supportteamet](https://www.pega.com/contact-us) värdet hämtas. 

5. Programmet Pega system förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Dessa anspråk kundspecifika och beror på dina behov. Följande valfria anspråk är exempel som som du kan konfigurera för ditt program. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. 

    ![Konfigurera enkel inloggning](./media/pegasystems-tutorial/tutorial_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | UID | *********** |
    | CN  | *********** |
    | E-post | *********** |
    | accessgroup | *********** |
    | Organisation | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Arbetsgrupp | *********** |
    | Telefon | *********** |

    > [!NOTE]
    > Detta är kund specifika värden. Ange din lämpliga värden.

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/pegasystems-tutorial/tutorial_general_400.png)
    
9. Konfigurera enkel inloggning på **Pega system** sida, öppna den **Pega Portal** med administratörskonto i ett nytt webbläsarfönster.

10. Välj **skapa** -> **SysAdmin** -> **Autentiseringstjänsten**.

    ![Konfigurera enkel inloggning spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Utför följande åtgärder på **skapa Aauthentication tjänst** skärm:

    ![Konfigurera enkel inloggning spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Välj **SAML 2.0** från typen

    b. I den **namn** textruta ange alla namn t.ex Azure AD-SSO

    c. I den **kort beskrivning** textruta, ange en beskrivning  

    d. Klicka på **skapa och öppna** 
    
12. I **identitetsprovider (IdP) information** klickar du på **importera IdP metadata** och bläddra i metadatafilen som du har hämtat från Azure-portalen. Klicka på **skicka** att läsa in metadata.

    ![Konfigurera enkel inloggning spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Detta kommer att fylla i IdP-data som visas nedan.

    ![Konfigurera enkel inloggning spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Utför följande åtgärder på **Service Provider (SP) inställningar** avsnitt:

    ![Konfigurera enkel inloggning spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopiera den **enhetens identifiering** värdet och klistrar in tillbaka i Azure Portal **identifierare** textruta.

    b.  Kopiera den **Assertion konsumenten Service (ACS) var** värdet och klistrar in tillbaka i Azure Portal **Reply URL** textruta.

    c. Välj **inaktivera begäran signering**.

15. Klicka på **Spara**
    
> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/pegasystems-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/pegasystems-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/pegasystems-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-pega-systems-test-user"></a>Skapa en testanvändare Pega system

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pega system. Se tillsammans med [Pega system klienten supportteamet](https://www.pega.com/contact-us) att skapa användare i Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pega system.

![Tilldela rollen][200] 

**Om du vill tilldela Pega system Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Pega system**.

    ![Länken Pega system i listan med program](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Pega system på åtkomstpanelen du ska hämta automatiskt loggat in på ditt program Pega system.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

