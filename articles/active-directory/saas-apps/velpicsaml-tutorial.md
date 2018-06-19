---
title: 'Självstudier: Azure Active Directory-integrering med Velpic SAML | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 8f0521125a8445ef3d34208d713d7030fa16457b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964124"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Självstudier: Azure Active Directory-integrering med Velpic SAML

I kursen får lära du att integrera Velpic SAML med Azure Active Directory (AD Azure).

Integrera Velpic SAML med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Velpic SAML
- Du kan aktivera användarna att automatiskt hämta loggat in på Velpic SAML (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Velpic SAML behöver du följande:

- En Azure AD-prenumeration
- En Velpic SAML enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Velpic SAML från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-velpic-saml-from-the-gallery"></a>Att lägga till Velpic SAML från galleriet
Du måste lägga till Velpic SAML från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Velpic SAML i Azure AD.

**Utför följande steg för att lägga till Velpic SAML från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Velpic SAML**.

    ![Skapa en testanvändare i Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. Välj i resultatpanelen **Velpic SAML**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Du konfigurera och testa Azure AD enkel inloggning med Velpic SAML baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Velpic SAML motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Velpic SAML upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Velpic SAML.

Om du vill konfigurera och testa Azure AD enkel inloggning med Velpic SAML, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Velpic SAML](#creating-a-velpic-saml-test-user)**  – har en motsvarighet för Britta Simon Velpic SAML som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Velpic SAML-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Velpic SAML:**

1. I Azure-hanteringsportalen på den **Velpic SAML** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Ange information i den **Velpic SAML domän och URL: er** avsnittet -

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. I den **inloggnings-URL** textruta Skriv värdet som: `https://<sub-domain>.velpicsaml.net`

    b. I den **identifierare** textruta klistra in den **'Enkel inloggning på URL'** värde `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Observera att URL: en inloggning kommer att tillhandahållas av Velpic SAML-teamet och identifierarvärde blir tillgänglig när du konfigurerar enkel inloggning plugin-programmet på Velpic SAML-sida. Du måste kopiera värdet från Velpic SAML program sida och klistra in den här.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_general_400.png)

6. Klicka på Konfigurera Velpic SAML så öppnas konfigurera inloggning i konfigurationsavsnittet Velpic SAML. Kopiera SAML enhets-ID från avsnittet Snabbreferens.

7. Logga in på webbplatsen Velpic SAML företag som en administratör i en annan webbläsarfönster.

8. Klicka på **hantera** fliken och gå till **integrering** avsnitt där du måste klicka på **plugin-program** för att skapa nytt plugin-program för inloggning.

    ![plugin-programmet](./media/velpicsaml-tutorial/velpic_1.png)

9. Klicka på den **'Lägga till plugin-programmet'** knappen.
    
    ![plugin-programmet](./media/velpicsaml-tutorial/velpic_2.png)

10. Klicka på den **SAML** panelen i lägga till Plugin-sida.
    
    ![plugin-programmet](./media/velpicsaml-tutorial/velpic_3.png)

11. Ange namnet på den nya SAML-plugin-programmet och klicka på den **'Add-** knappen.

    ![plugin-programmet](./media/velpicsaml-tutorial/velpic_4.png)

12. Ange information på följande sätt:

    ![plugin-programmet](./media/velpicsaml-tutorial/velpic_5.png)

    a. I den **namn** textruta skriver du namnet på SAML-plugin-programmet.

    b. I den **utfärdar-URL** textruta klistra in den **SAML enhets-ID** du kopierade från den **konfigurera inloggning** fönstret i Azure-portalen.

    c. I den **providern Metadata Config** överföra Metadata XML-filen som du hämtade från Azure-portalen.

    d. Du kan också välja att aktivera SAML precis i tid etablering genom att aktivera den **'Automatiskt skapa nya användare'** kryssrutan. Om en användare finns inte i Velpic och den här flaggan är inte aktiverad, misslyckas inloggningen från Azure. Om flaggan är aktiverad kommer automatiskt att etableras i Velpic användaren vid inloggning. 

    e. Kopiera den **enkel inloggning på URL: en** från texten rutan och klistra in den i Azure-portalen.
    
    f. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/velpicsaml-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/velpicsaml-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/velpicsaml-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/velpicsaml-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Skapa en testanvändare Velpic SAML

Det här steget krävs vanligtvis inte eftersom programmet stöder bara i tid användaretablering. Om automatisk användaretablering inte är aktiverat kan sedan skapa manuella användare göras som beskrivs nedan.

Logga in på webbplatsen Velpic SAML företag som administratör och utför följande steg:
    
1. Klickar du på Hantera och gå till avsnittet användare, klicka sedan på nytt för att lägga till användare.

    ![Lägg till användare](./media/velpicsaml-tutorial/velpic_7.png)

2. På den **”skapa nya användare”** dialogrutan utför följande steg.

    ![Användare](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. I den **Förnamn** textruta Ange först namnet på Britta Simon.

    b. I den **efternamn** textruta skriver Britta Simon efternamn.

    c. I den **användarnamn** textruta skriver du namnet på Britta Simon.

    d. I den **e-post** textruta skriver Britta Simon konto e-postadress.

    e. Resten av informationen är valfritt, Fyll den vid behov.
    
    f. Klicka på **SPARA**.  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till Velpic SAML.

![Tilldela användare][200] 

**Om du vill tilldela Velpic SAML Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Velpic SAML**.

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

1. Du bör få inloggningssidan för Velpic SAML-program när du klickar på panelen Velpic SAML på åtkomstpanelen. Du bör se den **”logga in med Azure AD-** knappen på inloggningssidan.

    ![plugin-programmet](./media/velpicsaml-tutorial/velpic_6.png)

2. Klicka på den **”logga in med Azure AD-** knappen Logga in på Velpic med hjälp av Azure AD-kontot.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/velpicsaml-tutorial/tutorial_general_203.png

