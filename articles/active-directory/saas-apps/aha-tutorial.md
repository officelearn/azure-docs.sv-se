---
title: 'Självstudier: Azure Active Directory-integrering med Aha! | Microsoft Docs'
description: Lär dig mer om att konfigurera enkel inloggning mellan Azure Active Directory och Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: d46467947116da6f6e5439a54e0315f216961819
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813227"
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Självstudier: Azure Active Directory-integrering med Aha!

I den här självstudien får du lära dig hur du integrerar Aha! med Azure Active Directory (AD Azure).

Integrera Aha! med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Aha!
- Du kan aktivera användarna att automatiskt få loggat in på Aha! (Enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Konfigurera Azure AD-integrering med Aha!, behöver du följande objekt:

- En Azure AD-prenumeration
- An Aha! enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägger till Aha! från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-aha-from-the-gallery"></a>Lägger till Aha! från galleriet
Konfigurera integreringen av Aha! i Azure AD som du behöver lägga till Aha! från galleriet i din lista över hanterade SaaS-appar.

**Att lägga till Aha! Utför följande steg från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

4. I sökrutan skriver **Aha!**.

    ![Skapa en Azure AD-användare för testning](./media/aha-tutorial/tutorial_aha_search.png)

5. I resultatpanelen väljer **Aha!**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/aha-tutorial/tutorial_aha_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Aha! baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i Aha! är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Aha! måste upprättas.

I Aha!, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Konfigurera och testa Azure AD enkel inloggning med Aha!, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Aha! Testa användare](#creating-an-aha-test-user)**  – du har en motsvarighet för Britta Simon i Aha! som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Aha! programmet.

**Konfigurera Azure AD enkel inloggning med Aha!, utför följande steg:**

1. I Azure-portalen på den **Aha!** program-integrering-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/aha-tutorial/tutorial_aha_samlbase.png)

3. På den **Aha! Domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/aha-tutorial/tutorial_aha_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.aha.io/session/new`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<companyname>.aha.io`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Aha! Klienten supportteamet](https://www.aha.io/company/contact) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/aha-tutorial/tutorial_aha_certificate.png) 

5. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/aha-tutorial/tutorial_general_400.png)

6. Logga in på din Aha i ett annat webbläsarfönster! företagets webbplats som administratör.

7. Klicka på menyn längst upp **inställningar**.

    ![Inställningar](./media/aha-tutorial/IC798950.png "Inställningar")

8. Klicka på **konto**.
   
    ![Profilen](./media/aha-tutorial/IC798951.png "profil")

9. Klicka på **säkerhet och enkel inloggning**.
   
    ![Säkerhet och enkel inloggning](./media/aha-tutorial/IC798952.png "säkerhet och enkel inloggning")

10. I **enkel inloggning** avsnittet som **identitetsprovidern**väljer **SAML2.0**.
   
    ![Säkerhet och enkel inloggning](./media/aha-tutorial/IC798953.png "säkerhet och enkel inloggning")

11. På den **enkel inloggning** configuration utför följande steg:
    
    ![Enkel inloggning](./media/aha-tutorial/IC798954.png "Enkel inloggning")
    
       a. I den **namn** textrutan anger du ett namn för din konfiguration.

       b. För **konfigurera med hjälp av**väljer **metadatafil**.
   
       c. Om du vill ladda upp din hämtade metadatafilen, klickar du på **Bläddra**.
   
       d. Klicka på **Uppdatera**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/aha-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/aha-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/aha-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/aha-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-aha-test-user"></a>Skapa en Aha! testanvändare

Aktivera Azure AD-användare att logga in på Aha!, de måste etableras i Aha!.  

När det gäller Aha!, etablering är en automatisk uppgift. Det finns inga uppgift åt dig.

Användare skapas automatiskt om det behövs under det första enkla inloggning för försöket.

>[!NOTE]
>Du kan använda andra Aha! Verktyg för att skapa användaren-konto eller API: er som tillhandahålls av Aha! att tillhandahålla AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Aha!.

![Tilldela användare][200] 

**Att tilldela Aha Britta Simon!, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Aha!**.

    ![Konfigurera enkel inloggning](./media/aha-tutorial/tutorial_aha_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Öppna panelen om du vill testa dina inställningar för enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/aha-tutorial/tutorial_general_01.png
[2]: ./media/aha-tutorial/tutorial_general_02.png
[3]: ./media/aha-tutorial/tutorial_general_03.png
[4]: ./media/aha-tutorial/tutorial_general_04.png

[100]: ./media/aha-tutorial/tutorial_general_100.png

[200]: ./media/aha-tutorial/tutorial_general_200.png
[201]: ./media/aha-tutorial/tutorial_general_201.png
[202]: ./media/aha-tutorial/tutorial_general_202.png
[203]: ./media/aha-tutorial/tutorial_general_203.png

