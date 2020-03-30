---
title: 'Självstudiekurs: Konfigurera Robin för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Robin Powered.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: fabd8a1953bedf6c3db6da443903a6dbd965b01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061021"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Robin för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Robin och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Robin.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Robin-hyresgäst](https://robinpowered.com/pricing/)
* Ett användarkonto i Robin med administratörsbehörighet.

## <a name="assigning-users-to-robin"></a>Tilldela användare till Robin

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Robin. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Robin genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Viktiga tips för att tilldela användare till Robin

* Vi rekommenderar att en enda Azure AD-användare tilldelas Robin för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Robin måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-robin-for-provisioning"></a>Ställ in Robin för etablering

1. Logga in på [Robins administratörskonsol](https://dashboard.robinpowered.com/login). Navigera till **Hantera >-integreringar > SCIM-> hantera**.

    ![Robin powered Admin Console](media/robin-provisioning-tutorial/robin-admin.png)

2.  Generera en ny organisationstoken. Om du förlorar den här token kan du alltid skapa en ny utan att påverka befintliga användare.

    ![robin powered Lägg till SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  Kopiera **SCIM-autentiseringstoken**. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt Robin-program i Azure-portalen.



## <a name="add-robin-from-the-gallery"></a>Lägg till Robin från galleriet

Innan du konfigurerar Robin för automatisk användaretablering med Azure AD måste du lägga till Robin från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Robin från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Robin**, väljer **Robin** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Robin i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Konfigurera automatisk användaretablering till Robin 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Robin baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Robin , enligt instruktionerna i [Robin Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Robin i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Robin**i programlistan .

    ![Robin-driv länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://api.robinpowered.com/v1.0/scim-2` **klient-URL.** Ange värdet **för SCIM-autentiseringstoken** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Robin. Om anslutningen misslyckas kontrollerar du att Ditt Robin-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Robin**.

    ![Robin-drivna användarmappningar](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till Robin i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Robin för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Robin-drivna användarattribut](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Robin**.

    ![Robin powered Gruppmappningar](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Robin i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Robin för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Robin-motordrivna gruppattribut](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Robin ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Robin genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Robin.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

