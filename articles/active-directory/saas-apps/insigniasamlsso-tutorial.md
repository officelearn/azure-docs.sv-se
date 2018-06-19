---
title: 'Självstudier: Azure Active Directory-integrering med bröstknappar SAML SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och bröstknappar SAML SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 828c981c-c3dd-4eb2-8699-0f732baa43f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: jeedes
ms.openlocfilehash: 5e87c331161e299eeba88202e7c7e9882192d9de
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968047"
---
# <a name="tutorial-azure-active-directory-integration-with-insignia-saml-sso"></a>Självstudier: Azure Active Directory-integrering med bröstknappar SAML SSO

I kursen får lära du att integrera bröstknappar SAML SSO med Azure Active Directory (AD Azure).

Integrera bröstknappar SAML SSO med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till bröstknappar SAML SSO.
- Du kan aktivera användarna att automatiskt hämta loggat in på bröstknappar SAML SSO (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med bröstknappar SAML SSO, behöver du följande:

- En Azure AD-prenumeration
- En bröstknappar SAML SSO enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till bröstknappar SAML SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-insignia-saml-sso-from-the-gallery"></a>Att lägga till bröstknappar SAML SSO från galleriet
Du måste lägga till bröstknappar SAML SSO från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av bröstknappar SAML SSO i Azure AD.

**Utför följande steg för att lägga till bröstknappar SAML SSO från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **bröstknappar SAML SSO**väljer **bröstknappar SAML SSO** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Bröstknappar SAML SSO i resultatlistan](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med bröstknappar SAML SSO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren bröstknappar SAML SSO till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren bröstknappar SAML SSO upprättas.

Bröstknappar SAML SSO, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med bröstknappar SAML SSO, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare bröstknappar SAML SSO](#create-an-insignia-saml-sso-test-user)**  – du har en motsvarighet för Britta Simon bröstknappar SAML SSO som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt bröstknappar SAML SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med bröstknappar SAML SSO:**

1. I Azure-portalen på den **bröstknappar SAML SSO** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_samlbase.png)

3. På den **bröstknappar SAML SSO domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och bröstknappar SAML SSO domän med enkel inloggning information](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<customername>.insigniails.com/ils` |
    | `https://<customername>.insigniails.com/` |
    | `https://<customername>.insigniailsusa.com/ ` |

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<customername>.insigniailsusa.com/<uniqueid>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [bröstknappar SAML SSO klienten supportteamet](http://www.insigniasoftware.com/insignia/Techsupport.aspx) att hämta dessa värden. 
 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/insigniasamlsso-tutorial/tutorial_general_400.png)

6. På den **bröstknappar SAML SSO Configuration** klickar du på **konfigurera bröstknappar SAML SSO** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Bröstknappar SAML SSO-konfiguration](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_configure.png) 

7. Konfigurera enkel inloggning på **bröstknappar SAML SSO** sida, måste du skicka den hämtade **certifikat (Base64)**, **Sign-Out URL och SAML inloggning tjänst-URL för enkel** till [Bröstknappar SAML SSO supportteamet](http://www.insigniasoftware.com/insignia/Techsupport.aspx). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/insigniasamlsso-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/insigniasamlsso-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/insigniasamlsso-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/insigniasamlsso-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-insignia-saml-sso-test-user"></a>Skapa en testanvändare bröstknappar SAML SSO

I det här avsnittet kan du skapa en användare som kallas Britta Simon i bröstknappar biblioteket System. Arbeta med [bröstknappar biblioteket System supportteamet](http://www.insigniasoftware.com/insignia/Techsupport.aspx) att lägga till användare i den bröstknappar biblioteket plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till bröstknappar SAML SSO.

![Tilldela rollen][200] 

**Om du vill tilldela bröstknappar SAML SSO Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **bröstknappar SAML SSO**.

    ![Länken bröstknappar SAML SSO i listan med program](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen bröstknappar SAML SSO på åtkomstpanelen du bör få automatiskt loggat in på ditt bröstknappar SAML SSO-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/insigniasamlsso-tutorial/tutorial_general_01.png
[2]: ./media/insigniasamlsso-tutorial/tutorial_general_02.png
[3]: ./media/insigniasamlsso-tutorial/tutorial_general_03.png
[4]: ./media/insigniasamlsso-tutorial/tutorial_general_04.png

[100]: ./media/insigniasamlsso-tutorial/tutorial_general_100.png

[200]: ./media/insigniasamlsso-tutorial/tutorial_general_200.png
[201]: ./media/insigniasamlsso-tutorial/tutorial_general_201.png
[202]: ./media/insigniasamlsso-tutorial/tutorial_general_202.png
[203]: ./media/insigniasamlsso-tutorial/tutorial_general_203.png

