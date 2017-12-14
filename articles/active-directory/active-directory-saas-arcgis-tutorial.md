---
title: "Självstudier: Azure Active Directory-integrering med ArcGIS Online | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ArcGIS Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: b09dd977cbf5c4273667167217e86bb79ac2a9d8
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Självstudier: Azure Active Directory-integrering med ArcGIS Online

I kursen får lära du att integrera ArcGIS Online med Azure Active Directory (AD Azure).

Integrera ArcGIS Online med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ArcGIS Online.
- Du kan aktivera användarna att automatiskt hämta loggat in på ArcGIS Online (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med ArcGIS Online, behöver du följande:

- En Azure AD-prenumeration
- En ArcGIS Online enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ArcGIS Online från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-arcgis-online-from-the-gallery"></a>Att lägga till ArcGIS Online från galleriet
Du måste lägga till ArcGIS Online från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ArcGIS Online i Azure AD.

**Om du vill lägga till ArcGIS Online från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ArcGIS Online**väljer **ArcGIS Online** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![ArcGIS Online i resultatlistan](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ArcGIS Online baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ArcGIS Online motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i ArcGIS Online upprättas.

I ArcGIS Online, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med ArcGIS Online, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en ArcGIS Online testanvändare](#create-a-arcgis-online-test-user)**  – du har en motsvarighet för Britta Simon i ArcGIS Online som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet ArcGIS Online.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ArcGIS Online:**

1. I Azure-portalen på den **ArcGIS Online** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. På den **ArcGIS Online domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och ArcGIS Online domän med enkel inloggning information](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<companyname>.maps.arcgis.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [ArcGIS Online klienten supportteamet](http://support.esri.com/en/) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. Logga in på webbplatsen ArcGIS företag som en administratör i en annan webbläsarfönster.

7. Klicka på **redigera inställningar för**.

    ![Redigera inställningar för](./media/active-directory-saas-arcgis-tutorial/ic784742.png "redigera inställningar")

8. Klicka på **säkerhet**.

    ![Säkerhet](./media/active-directory-saas-arcgis-tutorial/ic784743.png "säkerhet")

9. Under **Enterprise inloggningar**, klickar du på **ange IDENTITETSLEVERANTÖR**.

    ![Enterprise-inloggningar](./media/active-directory-saas-arcgis-tutorial/ic784744.png "Enterprise-inloggningar")

10. På den **ange identitetsleverantör** konfigurationen utför följande steg:
   
    ![Ange identitetsleverantör](./media/active-directory-saas-arcgis-tutorial/ic784745.png "ange identitetsleverantören.")
   
    a. I den **namn** textruta Skriv ditt organisationsnamn.

    b. För **Metadata för Enterprise-identitetsleverantör ska anges med hjälp av**väljer **A filen**.

    c. Om du vill överföra din hämtade metadatafil klickar du på **Välj fil**.

    d. Klicka på **SET IDENTITETSLEVERANTÖR**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-arcgis-online-test-user"></a>Skapa en ArcGIS Online testanvändare

För att aktivera Azure AD-användare att logga in på ArcGIS Online, måste de etableras i ArcGIS Online.  
När det gäller ArcGIS Online är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **ArcGIS** klient.

2. Klicka på **bjuda in MEDLEMMAR**.
   
    ![Bjud in medlemmar](./media/active-directory-saas-arcgis-tutorial/ic784747.png "bjuda in medlemmar")

3. Välj **lägga till medlemmar automatiskt utan att skicka ett e-postmeddelande**, och klicka sedan på **nästa**.
   
    ![Lägga till medlemmar automatiskt](./media/active-directory-saas-arcgis-tutorial/ic784748.png "automatiskt lägga till medlemmar")

4. På den **medlemmar** dialogrutan utför följande steg:
   
     ![Lägg till och granska](./media/active-directory-saas-arcgis-tutorial/ic784749.png "Lägg till och granska")
    
     a. Ange den **e-post**, **Förnamn**, och **efternamn** av en giltig AAD-konto som du vill etablera.
  
     b. Klicka på **Lägg till och granska**.
5. Granska de data du har angett och klicka sedan på **lägga till MEDLEMMAR**.
   
    ![Lägg till medlem](./media/active-directory-saas-arcgis-tutorial/ic784750.png "Lägg till medlem")
        
    > [!NOTE]
    > Innehavaren för Azure Active Directory-konto ett e-postmeddelande och länken för att bekräfta sina konton innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ArcGIS Online.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon ArcGIS Online, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ArcGIS Online**.

    ![Länken ArcGIS Online i listan med program](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ArcGIS Online på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet ArcGIS Online.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png

