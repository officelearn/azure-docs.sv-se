---
title: "Självstudier: Azure Active Directory-integrering med Teamphoria | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Teamphoria."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 2a35efb04d7fe22abc6894c149caf090666ce016
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Självstudier: Azure Active Directory-integrering med Teamphoria

I kursen får lära du att integrera Teamphoria med Azure Active Directory (AD Azure).

Integrera Teamphoria med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Teamphoria
- Du kan aktivera användarna att automatiskt hämta loggat in på Teamphoria (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Teamphoria, behöver du följande:

- En Azure AD-prenumeration
- En Teamphoria enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Teamphoria från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-teamphoria-from-the-gallery"></a>Att lägga till Teamphoria från galleriet
Du måste lägga till Teamphoria från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Teamphoria i Azure AD.

**Utför följande steg för att lägga till Teamphoria från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Teamphoria**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. Välj i resultatpanelen **Teamphoria**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Teamphoria baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Teamphoria motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Teamphoria upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Teamphoria.

Om du vill konfigurera och testa Azure AD enkel inloggning med Teamphoria, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Teamphoria](#creating-a-teamphoria-test-user)**  – du har en motsvarighet för Britta Simon i Teamphoria som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Teamphoria program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Teamphoria:**

1. I Azure-hanteringsportalen på den **Teamphoria** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. På den **Teamphoria domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. I den **inloggnings-URL** textruta Skriv URL-Adressen med följande mönster:`https://<sub-domain>.teamphoria.com/login`    

    > [!NOTE] 
    > Observera att detta inte är verkliga värden. Du måste uppdatera dessa värden med den faktiska inloggnings-URL. Kontakta [Teamphoria klienten supportteamet](https://www.teamphoria.com/) att hämta Webbadress för inloggning. 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatet på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. På den **Teamphoria Configuration** klickar du på **konfigurera Teamphoria** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. Konfigurera enkel inloggning på **Teamphoria** sida, logga in i tillämpningsprogrammet Teamphoria som administratör.

8. Gå till **ADMINISTRATIONSINSTÄLLNINGAR** alternativet i verktygsfältet till vänster och under den fliken Konfigurera klickar du på **enda SIGN-ON** att öppna fönstret för SSO-konfiguration.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. Klicka på **lägga till nya IDENTITETSLEVERANTÖR** alternativ i det övre högra hörnet för att öppna formuläret för att lägga till inställningarna för enkel inloggning.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. Ange information i fälten som beskrivs nedan-

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **VISNINGSNAMN** : Ange visningsnamnet för plugin-programmet på sidan.

    b. **KNAPPNAMN** : namnet på fliken som visas på inloggningssidan för att logga in via enkel inloggning.

    c. **CERTIFIKATET** : öppna certifikatet tidigare hämtade från Azure-portalen i anteckningar, kopiera innehållet i samma och klistra in den här rutan.

    d. **STARTPUNKTEN** : klistra in den **SAML inloggning tjänst-URL för enkel** kopieras tidigare formuläret Azure-portalen.

    e. Alternativet för att växla **ON** och klicka på **spara**.   

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-teamphoria-test-user"></a>Skapa en testanvändare Teamphoria

För att aktivera Azure AD-användare att logga in på Teamphoria etableras de i Teamphoria. När det gäller Teamphoria är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på webbplatsen Teamphoria företag som administratör.

2. Klicka på **ADMIN** inställningar i verktygsfältet till vänster och under den **hantera** fliken Klicka på **användare** att öppna sidan för användare.

    ![Lägga till medarbetare](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. Klicka på den **manuell bjuda in** alternativet.

    ![Bjud in personer](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)  

4. Utför följande åtgärder på den här sidan. 
    
    ![Bjud in personer](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)  

    a. I den **e-postadress** textruta den **e-postadress** av BrittaSimon.

    b. I den **Förnamn** textruta typen **Britta**.

    c. I den **efternamn** textruta typen **Simon**.

    d. Klicka på **inbjudan 1 användaren**. Användare måste acceptera inbjudan att skapas i systemet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till Teamphoria.

![Tilldela användare][200] 

**Om du vill tilldela Teamphoria Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Teamphoria**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png

