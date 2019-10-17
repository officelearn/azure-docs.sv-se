---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Netsuite | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438491"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Netsuite

I den här självstudien får du lära dig hur du integrerar Netsuite med Azure Active Directory (Azure AD). När du integrerar Netsuite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netsuite.
* Gör det möjligt för användarna att logga in automatiskt till Netsuite med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) med Netsuite-aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* NetSuite stöder **IDP**-initierad enkel inloggning

* NetSuite stöder **Just-in-time**-användaretablering

* NetSuite stöder [Automatiserad användaretablering](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-netsuite-from-the-gallery"></a>Lägga till NetSuite från galleriet

Om du vill konfigurera integrering av NetSuite till Azure AD så behöver du lägga till NetSuite från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Netsuite** i sökrutan.
1. Välj **Netsuite** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Konfigurera och testa enkel inloggning med Azure AD för Netsuite

Konfigurera och testa Azure AD SSO med Netsuite med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Netsuite.

Om du vill konfigurera och testa Azure AD SSO med Netsuite slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Netsuite SSO](#configure-netsuite-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Netsuite-test](#create-netsuite-test-user)** för att få en motsvarighet till B. Simon i Netsuite som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Netsuite** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [NetSuite-klientsupportteamet](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. NetSuite-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![mallar](common/edit-attribute.png)

1. Utöver ovan förväntar Netsuite-programmet fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet användar anspråk i dialog rutan användarattribut, utför följande steg för att lägga till SAML-token-attributet som visas i tabellen nedan:

    | Namn | Källattribut | 
    | ---------------| --------------- |
    | konto  | `account id` |

    1. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    1. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    1. Lämna **Namnrymd** tom.

    1. Välj Källa som **Attribut**.

    1. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    1. Klicka på **Ok**

    1. Klicka på **Save** (Spara).

    >[!NOTE]
    >Värdet för kontoattributet är inte verkligt. Du kommer att uppdatera det här värdet som förklaras senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Netsuite** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Netsuite.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Netsuite**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-netsuite-sso"></a>Konfigurera Netsuite SSO

1. Öppna en ny flik i din webbläsare och logga in på din NetSuite-företagswebbplats som administratör.

2. I verktygsfältet högst upp på sidan klickar du på **konfiguration**och navigerar sedan till **företag** och klickar på **aktivera funktioner**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Klicka på **SuiteCloud**i verktygsfältet i mitten av sidan.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Under avsnittet **hantera autentisering** väljer du **SAML enkel inloggning** för att aktivera alternativet för enkel inloggning för SAML i Netsuite.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-ticksaml.png)

5. I verktygsfältet högst upp på sidan klickar du på **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

6. Från listan **INSTALLATIONSÅTGÄRDER** klickar du på **Integrering**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-integration.png)

7. I avsnittet **Hantera autentisering** klickar du på **SAML enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml.png)

8. På sidan **SAML-konfiguration** under avsnittet **NetSuite-konfiguration** så utför du följande steg:

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Välj **Primär autentiseringsmetod**.

    b. För fältet som är märkt **Metadata för SAMLV2-identitetsprovider** så väljer du **Överför IDP-metadatafil**. Klicka därefter på **Bläddra** för att överföra den metadatafil som du hämtade från Azure-portalen.

    c. Klicka på **Skicka**.

9. I NetSuite, klickar du på **Konfiguration** och går sedan till **Företag** och klickar på **Företagsinformation** från den övre navigeringsmenyn.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-account-id.png)

    b. På sidan **Företagsinformation** i den högra kolumnen kopierar du **Konto-ID**.

    c. Klistra in det **Konto-ID** som du har kopierat från NetSuite-kontot till fältet **Attributvärde** i Azure AD. 

10. Innan användare kan utföra enkel inloggning till NetSuite så måste de först tilldelas lämpliga behörigheter i NetSuite. Följ anvisningarna nedan för att tilldela dessa behörigheter.

    a. I den övre navigeringsmenyn klickar du på **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    b. I den vänstra navigeringsmenyn väljer du **Användare/roller** och klickar sedan på **Hantera roller**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klicka på **Ny roll**.

    d. Ange ett **Namn** för din nya roll.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klicka på **Save** (Spara).

    f. I den översta menyn klickar du på **Behörigheter**. Klicka sedan på **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-sso.png)

    g. Välj **SAML enkel inloggning** och klicka sedan på **Lägg till**.

    h. Klicka på **Save** (Spara).

    i. I den övre navigeringsmenyn klickar du på **Konfigurera** och klickar sedan på **Konfigurationshanteraren**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    j. I den vänstra navigeringsmenyn väljer du **Användare/roller** och klickar sedan på **Hantera användare**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Välj en testanvändare. Klicka sedan på **Redigera** och gå till **Åtkomst**-fliken.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-edit-user.png)

    l. I dialogrutan Roller, tilldela den roll som du har skapat.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-add-role.png)

    m. Klicka på **Save** (Spara).

### <a name="create-netsuite-test-user"></a>Skapa NetSuite-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i NetSuite. NetSuite stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i NetSuite så skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på NetSuite-panelen i åtkomstpanelen så borde du automatiskt loggas in på den NetSuite som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Netsuite med Azure AD](https://aad.portal.azure.com/)

