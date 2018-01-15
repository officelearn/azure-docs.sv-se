---
title: "Självstudier: Azure Active Directory-integrering med återgivning NetBenefits | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och återgivning NetBenefits."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: 007d3c894731560423e2dde0572793a4282a4654
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Självstudier: Azure Active Directory-integrering med återgivning NetBenefits

I kursen får lära du att integrera återgivning NetBenefits med Azure Active Directory (AD Azure).

Integrera återgivning NetBenefits med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till återgivning NetBenefits.
- Du kan aktivera användarna att automatiskt hämta loggat in på återgivning NetBenefits (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med återgivning NetBenefits behöver du följande:

- En Azure AD-prenumeration
- En återgivning NetBenefits enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till återgivning NetBenefits från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Att lägga till återgivning NetBenefits från galleriet
Du måste lägga till återgivning NetBenefits från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av återgivning NetBenefits i Azure AD.

**Utför följande steg för att lägga till återgivning NetBenefits från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **återgivning NetBenefits**väljer **återgivning NetBenefits** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Återgivning NetBenefits i resultatlistan](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med återgivning NetBenefits baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i återgivning NetBenefits motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i återgivning NetBenefits upprättas.

I återgivning NetBenefits **användare** mappning ska göras med **Azure AD-användare** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med återgivning NetBenefits, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare återgivning NetBenefits](#create-a-fidelity-netbenefits-test-user)**  – har en motsvarighet för Britta Simon återgivning NetBenefits som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för återgivning NetBenefits.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med återgivning NetBenefits:**

1. I Azure-portalen på den **återgivning NetBenefits** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. På den **återgivning NetBenefits domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och återgivning NetBenefits domän med enkel inloggning information](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. I den **identifierare** textruta, ange ett URL-Adressen:

    För miljöer med testning:`urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    För produktionsmiljö:`urn:sp:fidelity:geninbndnbparts20`

    b. I den **Reply URL** textruta, ange ett URL-Adressen:

    För miljöer med testning:`https://loginxq1.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`

    För produktionsmiljö:`https://login.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`
 
4. Återgivning NetBenefits program förväntar SAML-intyg i ett specifikt format. Vi har mappat den **användar-ID** med den **user.userprincipalname**. Du kan mappa detta med **employeeid** eller andra krav som gäller för din organisation som **användar-ID**. Följande skärmbild visar bara ett exempel för det här.

    ![Återgivning NetBenefits attribut](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >Återgivning NetBenefits stöd för statisk och dynamisk Federation. Statisk innebär inte använder SAML precis i tid användaren etablering och dynamiska sätt stöder bara i tid användaretablering. För att använda JIT baserat etablering kunder som har att lägga till vissa flera anspråk i Azure AD som användarens födelsedatum osv. Dessa uppgifter som tillhandahålls av den [återgivning NetBenefits supportteam](mailto:SSOMaintenance@fmr.com) och de måste aktivera den här dynamiska federation för din instans.
    
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_400.png)

6. På den **återgivning NetBenefits Configuration** klickar du på **konfigurera återgivning NetBenefits** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Återgivning NetBenefits konfiguration](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png) 

7. Konfigurera enkel inloggning på **återgivning NetBenefits** sida, måste du skicka den hämtade **XML-Metadata för**, **SAML enkel inloggning Tjänstwebbadress** och  **SAML enhets-ID** till [återgivning NetBenefits supportteam](mailto:SSOMaintenance@fmr.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Skapa en återgivning NetBenefits testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i återgivning NetBenefits. Om du skapar statiska federation arbeta med [återgivning NetBenefits supportteam](mailto:SSOMaintenance@fmr.com) att skapa användare i återgivning NetBenefits plattform. Dessa användare måste skapas och aktiveras innan du använder enkel inloggning. 

För dynamiska Federation skapas användare med bara In Time användaretablering. För att använda JIT baserat etablering kunder som har att lägga till vissa flera anspråk i Azure AD som användarens födelsedatum osv. Dessa uppgifter som tillhandahålls av den [återgivning NetBenefits supportteam](mailto:SSOMaintenance@fmr.com) och de måste aktivera den här dynamiska federation för din instans.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till återgivning NetBenefits.

![Tilldela rollen][200] 

**Om du vill tilldela återgivning NetBenefits Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **återgivning NetBenefits**.

    ![Länken återgivning NetBenefits i listan med program](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen återgivning NetBenefits på åtkomstpanelen du bör få automatiskt loggat in på ditt återgivning NetBenefits program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_203.png

