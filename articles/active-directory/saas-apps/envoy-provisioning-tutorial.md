---
title: 'Självstudiekurs: Konfigurera sändebud för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Envoy.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 30faae80f1af4ff63924a76b26a03b8fe354a7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058033"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera sändebud för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Envoy och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Envoy.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En sändebud hyresgäst](https://envoy.com/pricing/)
* Ett användarkonto i Sändebud med administratörsbehörighet.

## <a name="add-envoy-from-the-gallery"></a>Lägg till sändebud från galleriet

Innan du konfigurerar sändebud för automatisk användaretablering med Azure AD måste du lägga till sändebud från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Envoy från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Sändebud,** väljer **Sändebud** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Envoy i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Tilldela användare till Sändebud

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Envoy. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Envoy genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Viktiga tips för att tilldela användare till Envoy

* Vi rekommenderar att en enda Azure AD-användare tilldelas envoy för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Sändebud måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Konfigurera automatisk användaretablering till Sändebud 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Envoy baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Sändebud, i enlighet med instruktionerna i [envoy single sign-on tutorial](envoy-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för envoy i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Envoy**.

    ![Länken för Envoy i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://app.envoy.com/scim/v2` **klient-URL.** Om du vill hämta den **hemliga token för** ditt envoy-konto följer du genomgången enligt beskrivningen i steg 6.

6. Logga in på [administratörskonsolen för sändebudet](https://dashboard.envoy.com/login). Klicka på **Integrationer**.

    ![Integrering av sändebud](media/envoy-provisioning-tutorial/envoy01.png)

    Klicka på **Installera** för **Microsoft Azure SCIM-integrering**.

    ![Representant Installera](media/envoy-provisioning-tutorial/envoy02.png)

    Klicka på **Spara** för **synkronisera alla användare**. 

    ![Sändebud Spara](media/envoy-provisioning-tutorial/envoy03.png)

    Hämta den hemliga token ifylld.
    
    ![Sändebud OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Envoy. Om anslutningen misslyckas kontrollerar du att ditt Envoy-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till envoy**.
    
    ![Användarattribut för sändebud](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Granska användarattributen som synkroniseras från Azure AD till Sändebud i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Envoy för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för sändebud](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper mot envoy**.

    ![Användarmappningar för sändebud](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Sändebud i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Envoy för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarmappningar för sändebud](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Envoy ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till Sändebud genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Envoy.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

