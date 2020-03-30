---
title: 'Självstudiekurs: Konfigurera 15Five för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till 15Five.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059326"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera 15Five för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i 15Five och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till 15Five.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En 15Five hyresgäst](https://www.15five.com/pricing/).
* Ett användarkonto i 15Five med administratörsbehörighet.

## <a name="assigning-users-to-15five"></a>Tilldela användare till 15Five

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till 15Five. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till 15Five genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Viktiga tips för att tilldela användare till 15Five

* Vi rekommenderar att en enda Azure AD-användare tilldelas 15Five för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till 15Five måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-15five-for-provisioning"></a>Setup 15Five för etablering

Innan du konfigurerar 15Five för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på 15Five.

1. Logga in på [administratörskonsolen 15Five](https://my.15five.com/). Navigera till **Funktioner > integrationer**.

    ![15Five Admin Console 15Five Admin Console 15Five Admin Console 1](media/15five-provisioning-tutorial/integration.png)

2.  Klicka på **SCIM 2.0**.

    ![15Five Admin Console 15Five Admin Console 15Five Admin Console 1](media/15five-provisioning-tutorial/image00.png)

3.  Navigera till **SCIM-integrering > generera OAuth-token**.

    ![15Five Lägg till SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Kopiera värdena för **SCIM 2.0-bas-URL** och **Åtkomsttoken**. Det här värdet anges i fältet **Klient-URL** och **Hemlig token** på fliken Etablering i ditt 15Five-program i Azure-portalen.
    
    ![15Five Lägg till SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Lägg till 15Five från galleriet

Om du vill konfigurera 15Five för automatisk användaretablering med Azure AD måste du lägga till 15Five från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till 15Five från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **15Five**, väljer **15Five** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![15Five i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Konfigurera automatisk användaretablering till 15Five 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i 15Five baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för 15Five , enligt instruktionerna i [15Five Single sign-on tutorial](15five-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för 15Five i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **15Five**.

    ![Länken för 15Five i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5.  Under avsnittet Administratörsautentiseringsuppgifter anger du **de SCIM 2.0-bas-URL- och Åtkomsttokenvärden** som hämtats tidigare i **klient-URL** respektive **Hemlig token.** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till 15Five. Om anslutningen misslyckas kontrollerar du att ditt 15Five-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till 15Five**.

    ![15Fiva användarmappningar](media/15five-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till 15Five i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i 15Five för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![15Fiva användarattribut](media/15five-provisioning-tutorial/userattribute.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till 15Five**.

    ![15Fiva gruppkartningar](media/15five-provisioning-tutorial/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till 15Five i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i 15Five för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![15Fiva gruppattribut](media/15five-provisioning-tutorial/groupattribute.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för 15Five ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till 15Five genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på 15Five.

    Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Begränsningar för anslutning

* 15Five stöder inte hårda borttagningar för användare.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md).
