---
title: 'Självstudier: Azure Active Directory-integrering med Symantec Web Security Service (WSS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Symantec Web Security Service (VSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: dbf21c7c22a9b3273a65f7e186a2ac02ccae6ba2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436215"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Självstudier: Azure Active Directory-integrering med Symantec Web Security Service (VSS)

I den här självstudien får du lära dig hur du integrerar ditt Symantec Web Security Service (VSS)-konto med ditt Azure Active Directory (Azure AD) så att WSS kan autentisera en användare etableras i Azure AD med hjälp av SAML-autentisering och tvinga användaren eller säkerhetsnivå för reglerna.

Integrera Symantec Web Security Service (WSS) med Azure AD ger dig följande fördelar:

- Hantera alla användare och grupper som används av WSS-kontot från Azure AD-portalen. 

- Ge slutanvändarna att autentisera sig i WSS med sina autentiseringsuppgifter för Azure AD.

- Aktivera efterlevnad av användare och gruppen säkerhetsnivå för regler som definierats i WSS-konto.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Symantec Web Security Service (VSS), behöver du följande objekt:

- En Azure AD-prenumeration
- Ett konto för Symantec Web Security Service (VSS)

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med en WSS-konto som är för närvarande som används för produktion.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din WSS-konto som är för närvarande som används för produktion för det här testet om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien konfigurerar du din Azure AD för att aktivera enkel inloggning till WSS med autentiseringsuppgifterna för slutanvändare som definierats i din Azure AD-konto.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Symantec Web Security Service (VSS)-app från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Att lägga till Symantec Web Security Service (VSS) från galleriet
För att konfigurera integrering av Symantec Web Security Service (WSS) till Azure AD, som du behöver lägga till Symantec Web Security Service (VSS) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Symantec Web Security Service (VSS) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Symantec Web Security Service (WSS)** väljer **Symantec Web Security Service (WSS)** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![Symantec Web Security Service (WSS) i resultatlistan](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Symantec Web Security Service (WSS) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Symantec Web Security Service (VSS) är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Symantec Web Security Service (VSS) upprättas.

I Symantec Web Security Service (VSS), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Symantec Web Security Service (VSS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Symantec Web Security Service (WSS)](#create-a-symantec-web-security-service-wss-test-user)**  – du har en motsvarighet för Britta Simon i Symantec Web Security Service (WSS) som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Symantec Web Security Service (VSS)-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Symantec Web Security Service (VSS):**

1. I Azure-portalen på den **Symantec Web Security Service (WSS)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

1. På den **Symantec Web Security Service (VSS)-domän och URL: er** avsnittet, utför följande steg:

    ![Symantec Web Security Service (VSS)-domän och URL: er med enkel inloggning för information](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. I den **identifierare** textrutan anger du URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. I den **svars-URL** textrutan anger du URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Kontakta den [Symantec Web Security Service (WSS) klient-supportteamet](https://www.symantec.com/contact-us) om värdena för den **identifierare** och **svars-URL** fungerar inte av någon anledning.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/symantec-tutorial/tutorial_general_400.png)
    
1. Om du vill konfigurera enkel inloggning på Symantec Web Security Service (WSS) sida finns i online WSS-dokumentationen. Den hämtade **XML-Metadata för** fil måste importeras till WSS-portalen. Kontakta den [Symantec Web Security Service (WSS) supportteam](https://www.symantec.com/contact-us) om du behöver hjälp med konfigurationen på WSS-portalen.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/symantec-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/symantec-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/symantec-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/symantec-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Skapa en testanvändare Symantec Web Security Service (VSS)

I det här avsnittet skapar du en användare som kallas Britta Simon i Symantec Web Security Service (VSS). Motsvarande slutet användarnamnet kan skapas manuellt i WSS-portalen eller väntar du tills användare/grupper som etableras i Azure AD som ska synkroniseras med WSS portalen efter några minuter (~ 15 minuter). Användare måste skapas och aktiveras innan du använder enkel inloggning. Offentliga IP-adressen för den datorn för slutanvändaren som ska användas för att bläddra webbplatser måste också vara etablerade i Symantec Web Security Service (VSS)-portalen.

> [!NOTE]
> . [Klicka här](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) att hämta din dator är offentliga IP-adress.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Symantec Web Security Service (WSS).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Symantec Web Security Service (WSS), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Symantec Web Security Service (WSS)**.

    ![Länken Symantec Web Security Service (WSS) i listan med program](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska du testa funktionen för enkel inloggning nu när du har konfigurerat ditt WSS-konto för att använda din Azure AD för SAML-autentisering.

När du har konfigurerat din webbläsare att proxy trafik till WSS, när du öppnar webbläsaren och försök att bläddra till en plats och du kommer att omdirigeras till sidan för Azure-inloggning. Ange autentiseringsuppgifter för testanvändaren som har etablerats i Azure AD (det vill säga BrittaSimon) och tillhörande lösenord. När autentiseringen är klar kommer du att kunna bläddra till den webbplats som du har valt. Du bör skapa en regel på WSS-sida för att blockera BrittaSimon från att bläddra till en viss plats och du bör se sidan WSS block vid försök att bläddra till platsen som användare BrittaSimon.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/symantec-tutorial/tutorial_general_01.png
[2]: ./media/symantec-tutorial/tutorial_general_02.png
[3]: ./media/symantec-tutorial/tutorial_general_03.png
[4]: ./media/symantec-tutorial/tutorial_general_04.png

[100]: ./media/symantec-tutorial/tutorial_general_100.png

[200]: ./media/symantec-tutorial/tutorial_general_200.png
[201]: ./media/symantec-tutorial/tutorial_general_201.png
[202]: ./media/symantec-tutorial/tutorial_general_202.png
[203]: ./media/symantec-tutorial/tutorial_general_203.png

