---
title: 'Självstudier: Azure Active Directory-integrering med Palo Alto nätverk - Admin UI | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto nätverk - Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 7a296c1e358bffa70ed88ba40380266e8731afd8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211595"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integrera Azure Active Directory med Palo Alto nätverk - Admin-Gränssnittet

I kursen får lära du att integrera Azure Active Directory (AD Azure) med Palo Alto nätverk - Admin UI.

Av integrerande Azure AD med Palo Alto nätverk - Admin UI, får du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Palo Alto nätverk - Admin UI.
- Du kan aktivera användarna att få inloggad automatiskt till Palo Alto nätverk - Admin UI (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats, Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD, se [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo Alto nätverk - Admin UI, behöver du följande:

- En Azure AD-prenumeration
- En Palo Alto nätverk nästa generations brandvägg eller Panorama (centraliserad hanteringssystem för brandväggarna)

> [!NOTE]
> När du testar stegen i den här självstudiekursen, rekommenderar vi att du *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

* Lägga till nätverk Palo Alto - Admin UI från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Lägg till Palo Alto nätverk - Admin UI från galleriet
Om du vill konfigurera Azure AD-integrering med Palo Alto nätverk - Admin UI, lägger du till Palo Alto nätverk - Admin UI från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret ”företagsprogram”][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **Palo Alto nätverk - Admin UI**väljer **Palo Alto nätverk - Admin UI** i resultatlistan och välj sedan **Lägg till**.

    ![Palo Alto nätverk - Admin UI i resultatlistan](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Palo Alto nätverk - Admin UI, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD identifiera Palo Alto nätverk - Admin UI användar- och sin motsvarighet i Azure AD. Med andra ord upprättas en länk förhållandet mellan en Azure AD-användare och samma användare i Palo Alto nätverk - Admin UI.

Om du vill upprätta en länk relation, tilldela som Palo Alto nätverk - Admin UI *användarnamn* värdet för den *användarnamn* i Azure AD.

Slutför byggblock i följande fem avsnitt för att konfigurera och testa Azure AD enkel inloggning med Palo Alto nätverk - Admin UI.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i nätverket Palo Alto - Admin UI-program genom att göra följande:

1. I Azure-portalen på den **Palo Alto nätverk - Admin UI** programmet integration anger **enkel inloggning**.

    ![Länken ”enkel inloggning”][4]

2. I den **enkel inloggning** fönster i den **läget för enkel inloggning** väljer **SAML-baserade inloggning**.
 
    ![Fönstret ”enkel inloggning”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. Under **Palo Alto nätverk - URL: er och domänen för Admin-Användargränssnittet**, gör du följande:

    ![”Palo Alto nätverk - URL: er och domänen för Admin-UI” enkel inloggning information](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. I den **inloggnings-URL** skriver en URL i följande format: *https://\<kunden brandväggen FQDN > /php/login.php*.

    b. I den **identifierare** skriver en URL i följande format: *https://\<kunden brandväggen FQDN >: 443/SAML20/SP*.
    
    c. I den **Reply URL** Skriv Assertion konsumenten Service (ACS) Webbadressen i följande format: *https://\<kunden brandväggen FQDN >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med de faktiska inloggnings-URL och identifierare. Kontakta för att få värdena [Palo Alto nätverk - Användargränssnittet administratörsklient supportteamet](https://support.paloaltonetworks.com/support). 
 
4. Eftersom Palo Alto nätverk - Admin UI program förväntar SAML-intyg i ett specifikt format, konfigurera anspråk som visas i följande bild. Hantera attributvärden i den **användarattribut** avsnitt i den **Programintegrationstyp** sidan genom att göra följande:
    
    ![Listan attribut för SAML-Token](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Eftersom attributvärden är bara exempel, mappa lämpliga värden för *användarnamn* och *adminrole*. Det finns ett annat valfritt attribut *accessdomain*, som används för att begränsa administratörsåtkomst till specifika virtuella datorer i brandväggen.
   >
        
    | Attributets namn | Attributvärdet |
    | --- | --- |    
    | användarnamn | User.userPrincipalName |
    | adminrole | customadmin |

    a. Välj **Lägg till attributet**.  
    
    ![Knappen ”Lägg till attributet”](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    Den **lägga till attributet** öppnas.

    ![Fönstret ”Lägg till attributet”](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. I den **namnet** skriver attributets namn som visas för den raden.
    
    c. I den **värdet** skriver attributvärdet som visas för den raden.
    
    d. Välj **OK**.

    > [!NOTE]
    > Mer information om attribut finns i följande artiklar:
    > * [Administrativ roll profil för Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Enheten åtkomst domän för Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**, och välj sedan **spara**.

    ![Den XML-Metadata för Hämta länk](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Knappen Spara](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

6. Öppna Palo Alto nätverk brandväggen Admin användargränssnitt som en administratör i ett nytt fönster.

7. Välj den **enhet** fliken.

    ![Fliken enhet](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. I den vänstra rutan, Välj **SAML-identitetsprovider**, och välj sedan **importera** metadata importeras.

    ![Knappen Importera metadata-fil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. I den **SAML identifiera providern Server profil importera** fönster, gör du följande:

    ![Fönstret ”SAML identifiera providern Server profil importera”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. I den **profilnamn** ange ett namn (till exempel **AzureAD Admin UI**).
    
    b. Under **identitet providern Metadata**väljer **Bläddra**, och välj metadata.xml-fil som du tidigare hämtade från Azure-portalen.
    
    c. Avmarkera den **Validera providern identitetscertifikat** kryssrutan.
    
    d. Välj **OK**.
    
    e. För att genomföra konfigurationerna i brandväggen, Välj **Commit**.

10. I den vänstra rutan, Välj **SAML-identitetsprovider**, och välj SAML identitet providern profilen (till exempel **AzureAD Admin UI**) som du skapade i föregående steg. 

    ![SAML identitet providern profil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. I den **SAML identitet providern Server-profil** fönster, gör du följande:

    ![Fönstret ”SAML identitet providern Server-profil”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. I den **identitet providern Servicenivåmål URL** rutan ersätter den tidigare importerat SLO-URL med följande URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Välj **OK**.

12. Välj på Palo Alto nätverk brandväggen Admin UI **enhet**, och välj sedan **administratörsroller**.

13. Välj den **Lägg till** knappen. 

14. I den **Admin-profil för användarroller** fönster i den **namn** ange ett namn för administratörsrollen (till exempel **fwadmin**).  
    Rollnamnet administratör ska matcha administratörsroll för SAML-attributnamn som skickades av identitetsleverantören. Administratörens rollnamn och värde skapades i steg 4.

    ![Konfigurera Palo Alto administratörsroll för nätverk](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. Markera på i brandväggen Admin UI **enhet**, och välj sedan **autentiseringsprofil**.

16. Välj den **Lägg till** knappen. 

17. I den **autentiseringsprofil** fönster, gör du följande: 

    ![Fönstret ”autentiseringsprofil”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. I den **namn** ange ett namn (till exempel **AzureSAML_Admin_AuthProfile**).
    
    b. I den **typen** listrutan, Välj **SAML**. 
   
    c. I den **IdP Server-profil** listrutan väljer du lämpliga SAML identitet Provider-Server-profil (till exempel **AzureAD Admin UI**).
   
    c. Välj den **aktivera enkel logga ut** kryssrutan.
    
    d. I den **Admin Role-attributet** ange attributets namn (till exempel **adminrole**). 
    
    e. Välj den **Avancerat** fliken och under **listan Tillåt**väljer **Lägg till**. 
    
    ![Knappen Lägg till på fliken Avancerat](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Välj den **alla** kryssrutan eller Välj användare och grupper som kan autentisera med den här profilen.  
    När en användare autentiseras matchar brandväggen associerade användarnamn eller grupp mot posterna i listan. Om du inte lägga till poster, kan ingen användare autentiseras.

    g. Välj **OK**.

18. Välj för att aktivera administratörer använder SAML SSO med hjälp av Azure **enhet** > **installationsprogrammet**. I den **installationsprogrammet** väljer den **Management** fliken och under **autentiseringsinställningar**, Välj den **inställningar** knappen (”redskap”) . 

 ![Knappen Inställningar](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Välj den profil för SAML-autentisering som du skapade i steg 17 (till exempel **AzureSAML_Admin_AuthProfile**).

 ![Fältet autentiseringsprofil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Välj **OK**.

21. För att utföra konfigurationen, Välj **Commit**.


> [!TIP]
> När du konfigurerar appen, kan du läsa en kortare version av föregående anvisningarna i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen i den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan ansluta till den inbäddade dokumentation i den **Configuration** avsnittet längst ned. Mer information om funktionen inbäddade dokumentation finns [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen genom att göra följande:

![Skapa en testanvändare i Azure AD][100]

1. I Azure-portalen i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-länk](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över aktuella användare, Välj **användare och grupper** > **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. Längst upp i den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Den **användaren** öppnas.

4. I den **användaren** fönster, gör du följande:

    ![Fönstret användare](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Skapa ett nätverk för Palo Alto - Admin UI testanvändare

Palo Alto nätverk - Admin UI stöder just-in-time-användaretablering. Om en användare inte redan finns, skapas den automatiskt i systemet efter en lyckad autentisering. Ingen åtgärd krävs för att skapa användaren.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du låta användare Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto nätverk - Admin UI. Det gör du genom att göra följande:

![Tilldela rollen][200] 

1. I Azure-portalen öppnar den **program** visa, gå till den **Directory** visa och välj sedan **företagsprogram** > **alla program**.

    ![”Företagsprogram” och ”alla program” länkar][201] 

2. I den **program** väljer **Palo Alto nätverk - Admin UI**.

    ![Palo Alto nätverk - Admin UI-länk](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. I den vänstra rutan, Välj **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj **Lägg till** och sedan, i den **Lägg uppdrag** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** fönster i den **användare** väljer **Britta Simon**.

6. Välj den **Välj** knappen.

7. I den **Lägg uppdrag** väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer Palo Alto nätverk - Admin UI-panelen på panelen åtkomst bör du vara inloggad automatiskt till nätverket Palo Alto - Admin UI-programmet.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoadmin-tutorial/tutorial_general_203.png

