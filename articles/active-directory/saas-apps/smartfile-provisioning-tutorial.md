---
title: 'Självstudiekurs: Konfigurera SmartFile för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060243"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera SmartFile för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i SmartFile och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till SmartFile.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En SmartFile-klient](https://www.SmartFile.com/pricing/).
* Ett användarkonto i SmartFile med administratörsbehörighet.

## <a name="assigning-users-to-smartfile"></a>Tilldela användare till SmartFile

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till SmartFile. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till SmartFile genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Viktiga tips för att tilldela användare till SmartFile

* Vi rekommenderar att en enda Azure AD-användare tilldelas SmartFile för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till SmartFile måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-smartfile-for-provisioning"></a>Konfigurera SmartFile för etablering

Innan du konfigurerar SmartFile för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på SmartFile och samla in ytterligare information som behövs.

1. Logga in på smartfile-administratörskonsolen. Navigera till det övre högra hörnet i SmartFile-administratörskonsolen. Välj **produktnyckel**.

    ![Administratörskonsol för SmartFile](media/smartfile-provisioning-tutorial/login.png)

2. Om du vill generera en innehavartoken kopierar du **produktnyckeln** och **produktlösenordet**. Klistra in dem i ett anteckningsblock med ett kolon mellan dem.
    
     ![SmartFile Lägg till SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile Lägg till SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Lägga till SmartFile från galleriet

Om du vill konfigurera SmartFile för automatisk användaretablering med Azure AD måste du lägga till SmartFile från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till SmartFile från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **SmartFile,** väljer **SmartFile** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![SmartFile i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Konfigurera automatisk användaretablering till SmartFile 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i SmartFile baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för SmartFile, följ instruktionerna i [smartfile enkel inloggningshandledning](SmartFile-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för SmartFile i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SmartFile**.

    ![Länken för SmartFile i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5.  Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://<SmartFile sitename>.smartfile.com/ftp/scim` **klient-URL.** Ett exempel skulle `https://demo1test.smartfile.com/ftp/scim`se ut som . Ange **värdet för innehavartoken** (ProductKey:ProductPassword) som du hämtade tidigare i **Hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till SmartFile. Om anslutningen misslyckas kontrollerar du att SmartFile-kontot har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till SmartFile**.

    ![SmartFile-användarmappningar](media/smartfile-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till SmartFile i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i SmartFile för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper mot SmartFile**.

    ![SmartFile-gruppmappningar](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till SmartFile i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i SmartFile för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Attribut för SmartFile-grupp](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för SmartFile ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till SmartFile genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på SmartFile.

    Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Begränsningar för anslutning

* SmartFile stöder bara hårda borttagningar. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

 [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
