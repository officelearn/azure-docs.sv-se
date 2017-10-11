---
title: "Självstudier: Azure Active Directory-integrering med Zscaler privat åtkomst (ZPA) | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler privat åtkomst (ZPA)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 5c598bfa5b6725d21a89df54dbcb3314cc631d80
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Självstudier: Azure Active Directory-integrering med Zscaler privat åtkomst (ZPA)

I kursen får lära du att integrera Zscaler privat åtkomst (ZPA) med Azure Active Directory (AD Azure).

Integrera Zscaler privat åtkomst (ZPA) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler privat åtkomst (ZPA)
- Du kan aktivera användarna att automatiskt hämta inloggade till Zscaler privat åtkomst (ZPA) (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Zscaler privat åtkomst (ZPA) behöver du följande:

- En Azure AD-prenumeration
- En Zscaler privat åtkomst (ZPA) enkel inloggning på aktiverade prenumeration


> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Zscaler privat åtkomst (ZPA) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Att lägga till Zscaler privat åtkomst (ZPA) från galleriet
Du måste lägga till Zscaler privat åtkomst (ZPA) från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Zscaler privat åtkomst (ZPA) i Azure AD.

**Utför följande steg för att lägga till Zscaler privat åtkomst (ZPA) från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Zscaler privat åtkomst (ZPA)**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. Välj i resultatpanelen **Zscaler privat åtkomst (ZPA)**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Zscaler privat åtkomst (ZPA) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Zscaler privat åtkomst (ZPA) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Zscaler privat åtkomst (ZPA) upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Zscaler privat åtkomst (ZPA).

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler privat åtkomst (ZPA), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Zscaler privat åtkomst (ZPA)](#creating-a-zscaler-private-access-(zpa)-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler privat åtkomst (ZPA) som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i tillämpningsprogrammet Zscaler privat åtkomst (ZPA).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler privat åtkomst (ZPA):**

1. I Azure-hanteringsportalen på den **Zscaler privat åtkomst (ZPA)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. På den **Zscaler privat åtkomst (ZPA)-domän och URL: er** avsnittet, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. I den **logga URL** textruta Skriv en URL med följande mönster:`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. I den **identifierare** textruta typ:`https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med den faktiska logga URL och identifierare. Här rekommenderar vi att du om du vill använda det unika värdet för URL: en i identifieraren. Kontakta [Zscaler privat åtkomst (ZPA) supportteamet](https://help.zscaler.com/zpa-submit-ticket) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Skapa nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_400.png)   

5. På den **skapa nya certifikat** dialogrutan, klicka på kalenderikonen och välj en **förfallodatum**. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. På den **SAML-signeringscertifikat** väljer **aktivera nya certifikatet** och på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. På popup-fönstret **förnyelsecertifikat** -fönstret klickar du på **OK**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. Logga in på webbplatsen för företagets Zscaler privat åtkomst (ZPA) som en administratör i en annan webbläsarfönster.

10. Gå till **administratör** och klicka sedan på **Idp Configuration**.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. I den **Idp Configuration** klickar du på **lägga till nya IDP konfigurationen**.

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. I den **nya IDP konfigurationen** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Klicka på **Välj fil** och ladda upp din hämtade metadatafil.

    b. Klicka på **spara** knappen.
    


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Skapa en testanvändare Zscaler privat åtkomst (ZPA)

I det här avsnittet skapar du en användare som kallas Britta Simon i Zscaler privat åtkomst (ZPA). Se tillsammans med [Zscaler privat åtkomst (ZPA) supportteamet](https://help.zscaler.com/zpa-submit-ticket) att lägga till användare i Zscaler privat åtkomst (ZPA)-plattformen.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning med sin åtkomst beviljas till Zscaler privat åtkomst (ZPA).

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon till Zscaler privat åtkomst (ZPA), utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Zscaler privat åtkomst (ZPA)**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Zscaler privat åtkomst (ZPA) på åtkomstpanelen du bör få automatiskt loggat in på ditt program Zscaler privat åtkomst (ZPA).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_203.png