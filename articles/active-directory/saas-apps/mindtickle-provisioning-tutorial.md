---
title: 'Självstudiekurs: Konfigurera MindTickle för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till MindTickle.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 6ae7b6ac9c6feb539e4da3627ebbabd3c5b628fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061247"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera MindTickle för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i MindTickle och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till MindTickle.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En MindTickle-klient](https://www.mindtickle.com/)
* Ett användarkonto i MindTickle med administratörsbehörighet.

## <a name="assigning-users-to-mindtickle"></a>Tilldela användare till MindTickle

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till MindTickle. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till MindTickle genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Viktiga tips för att tilldela användare till MindTickle

* Vi rekommenderar att en enda Azure AD-användare tilldelas MindTickle för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till MindTickle måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-mindtickle-for-provisioning"></a>Konfigurera MindTickle för etablering

Innan du konfigurerar MindTickle för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på MindTickle.


1.  Kontakta [MindTickles supportteam](mailto:help@mindtickle.com) för att hämta den JWT-token som behövs för att konfigurera SCIM-etablering.


## <a name="add-mindtickle-from-the-gallery"></a>Lägg till MindTickle från galleriet

Om du vill konfigurera MindTickle för automatisk användaretablering med Azure AD måste du lägga till MindTickle från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till MindTickle från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **MindTickle**, väljer **MindTickle** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![MindTickle i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Konfigurera automatisk användaretablering till MindTickle 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i MindTickle baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för MindTickle, följ instruktionerna i [MindTickle Single sign-on tutorial](mindtickle-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för MindTickle i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **MindTickle**.

    ![MindTickle-länken i listan med program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://admin.mindtickle.com/scim` **klient-URL.** Mata in **JWT-tokenvärdet** som hämtats tidigare i secret token textbox, ange **JWT-tokenvärdet** som gavs av MindTickle supportteam. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till myPolicies. Om anslutningen misslyckas kontrollerar du att ditt MindTickle-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till MindTickle**.

    ![MindTickle-användarmappningar](media/mindtickle-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till MindTickle i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i MindTickle för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![MindTickle-användarmappningar](media/mindtickle-provisioning-tutorial/userattribute.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för MindTickle ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till MindTickle genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [Hur lång tid tar det att etablera användare](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Du kan använda avsnittet **Aktuell status** för att övervaka förloppet och följa länkar till din etableringsaktivitetsrapport, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på MindTickle. Mer information finns [i Kontrollera status för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
