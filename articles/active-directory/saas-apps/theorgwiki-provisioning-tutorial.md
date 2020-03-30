---
title: 'Självstudiekurs: Konfigurera TheOrgWiki för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063161"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera TheOrgWiki för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i TheOrgWiki och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till TheOrgWiki.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En OrgWiki-klient](https://www.theorgwiki.com/welcome/).
* Ett användarkonto i TheOrgWiki med administratörsbehörighet.

## <a name="assign-users-to-theorgwiki"></a>Tilldela användare till TheOrgWiki

Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till TheOrgWiki. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till TheOrgWiki genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Viktiga tips för att tilldela användare till TheOrgWiki

* Vi rekommenderar att en enda Azure AD-användare tilldelas TheOrgWiki för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till TheOrgWiki måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-theorgwiki-for-provisioning"></a>Konfigurera TheOrgWiki för etablering

Innan du konfigurerar TheOrgWiki för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på TheOrgWiki.

1. Logga in på [administratörskonsolen TheOrgWiki](https://www.theorgwiki.com/login/). Klicka på **Admin Console**.

    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. Klicka på fliken Inställningar i **Administratörskonsolen**. 

    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navigera till **tjänstkonton**.

    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klicka på **+Service-konto**. Under **Tjänstkontotyp**väljer du **Tokenbaserad**. Klicka på **Spara**.

    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Kopiera aktiva **tokens**. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt TheOrgWiki-program i Azure-portalen.
     
    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Lägg till TheOrgWiki från galleriet

Om du vill konfigurera TheOrgWiki för automatisk användaretablering med Azure AD måste du lägga till TheOrgWiki från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **TheOrgWiki**, välj **TheOrgWiki** på resultatpanelen. 

    ![TheOrgWiki i resultatlistan](common/search-new-app.png)

5. Välj **knappen Registrera dig för TheOrgWiki** som omdirigerar dig till TheOrgWikis inloggningssida. 

    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  I det övre högra hörnet väljer du **Logga in**.

    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Eftersom TheOrgWiki är en OpenIDConnect-app väljer du att logga in på OrgWiki med ditt Microsoft-arbetskonto.

    ![TheOrgWiki Lägg TILL SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Efter en lyckad autentisering läggs programmet automatiskt till din klient och du omdirigeras till ditt TheOrgWiki-konto.

    ![OrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurera automatisk användaretablering till TheOrgWiki 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TheOrgWiki baserat på användar- och/eller grupptilldelningar i Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för TheOrgWiki i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **TheOrgWiki**i programlistan .

    ![Länken OrgWiki i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **klient-URL.** 

    Exempel: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> **Underdomänvärdet** kan bara ställas in under den första registreringsprocessen för TheOrgWiki.
 
6. Mata in tokenvärdet i **fältet Hemlig token** som du hämtade tidigare från TheOrgWiki. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till TheOrgWiki. Om anslutningen misslyckas kontrollerar du att ditt TheOrgWiki-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till TheOrgWiki**.

    ![TheOrgWiki Användarmappningar](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Granska användarattributen som synkroniseras från Azure AD till TheOrgWiki i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i TheOrgWiki för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![TheOrgWiki-användarattribut](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD-etableringstjänsten för TheOrgWiki ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till OrgWiki genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [Hur lång tid tar det att etablera användare](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **Aktuell status** för att övervaka förloppet och följa länkar till din etableringsaktivitetsrapport, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på TheOrgWiki. Mer information finns [i Kontrollera status för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md).