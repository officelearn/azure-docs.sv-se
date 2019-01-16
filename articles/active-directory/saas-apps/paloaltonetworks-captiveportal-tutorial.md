---
title: 'Självstudier: Azure Active Directory-integrering med Palo Alto Networks – Captive Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks – Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064737"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Självstudier: Azure Active Directory-integrering med Palo Alto Networks – Captive Portal

I den här självstudien lär du dig att integrera Palo Alto Networks – Captive Portal med Azure Active Directory (AD Azure).
Genom att integrera Palo Alto Networks – Captive Portal med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Palo Alto Networks – Captive Portal.
* Du kan göra så att dina användare automatiskt loggas in på Palo Alto Networks – Captive Portal (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Palo Alto Networks – Captive Portal behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks – Captive Portal-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Palo Alto Networks – Captive Portal stöder **IDP**-initierad enkel inloggning

* Palo Alto Networks – Captive Portal stöder **just-in-time**-användaretablering

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Lägga till Palo Alto Networks – Captive Portal från galleriet

För att konfigurera integreringen av Palo Alto Networks – Captive Portal till Azure AD behöver du lägga till Palo Alto Networks – Captive Portal från galleriet till listan över hanterade SaaS-appar.

**Lägg till Palo Alto Networks – Captive Portal från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Palo Alto Networks – Captive Portal**, väljer **Palo Alto Networks – Captive Portal** från resultatpanelen och klickar därefter på **Lägg till** för att lägga till programmet.

     ![Palo Alto Networks – Captive Portal i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet så konfigurerar och testar du enkel inloggning i Azure AD med Palo Alto Networks – Captive Portal baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Palo Alto Networks – Captive Portal upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Palo Alto Networks – Captive Portal behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Palo Alto Networks – Captive Portal](#configure-palo-alto-networks---captive-portal-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Palo Alto Networks – Captive Portal-testanvändare](#create-palo-alto-networks---captive-portal-test-user)** – du har en motpart för Britta Simon i Palo Alto Networks – Captive Portal som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Palo Alto Networks – Captive Portal:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Palo Alto Networks – Captive Portal**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I dialogrutan **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Palo Alto Networks – Captive Portal-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för Palo Alto Networks – Captive Portal-klienten](https://support.paloaltonetworks.com/support). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Konfigurera enkel inloggning för Palo Alto Networks – Captive Portal

1. Öppna Palo Alto-platsen som administratör i ett annat webbläsarfönster.

2. Klicka på **enhet**.

    ![Konfigurera enkel inloggning med Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Välj **SAML-identitetsprovider** från det vänstra navigeringsfönstret och klicka på Importera om du vill importera metadatafilen.

    ![Konfigurera enkel inloggning med Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Utför följande åtgärder i Importera-fönstret

    ![Konfigurera enkel inloggning med Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. I textrutan **Profilnamn** anger du ett namn, till exempel Azure AD Admin UI.
    
    b. I **Metadata för identitetsprovider** klickar du på **Bläddra** och väljer metadata XML-filen som du hämtade från Azure Portal
    
    c. Klicka på **OK**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att ge åtkomst till Palo Alto Networks – Captive Portal.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Palo Alto Networks – Captive Portal**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver du in och väljer **Palo Alto Networks – Captive Portal**.

    ![Palo Alto Networks – Captive Portal-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Skapa Palo Alto Networks – Captive Portal-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Palo Alto Networks – Captive Portal. Palo Alto Networks – Captive Portal stöder **just-in-time-användaretablering**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns en användare i Palo Alto Networks – Captive Portal skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för Palo Alto Networks – Captive Portal-klienten](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Captive Portal är konfigurerat bakom brandväggen på Windows-VM. För att testa enkel inloggning på Captive Portal loggar du in på Windows-VM med hjälp av Remote Desktop Protocol (RDP). Från RDP-sessionen öppnar du ett webbläsarfönster till valfri webbplats. Det bör automatiskt öppna URL:en för enkel inloggning och fråga efter autentisering. När autentiseringen är klar bör du kunna navigera till webbplatser.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

