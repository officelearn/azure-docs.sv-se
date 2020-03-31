---
title: 'Självstudiekurs: Konfigurera StarLeaf för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064314"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera StarLeaf för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i StarLeaf och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till StarLeaf.

> [!NOTE]
>  Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En StarLeaf hyresgäst](https://www.starleaf.com/solutions/).
* Ett användarkonto i StarLeaf med administratörsbehörighet.

## <a name="assign-users-to-starleaf"></a>Tilldela användare till StarLeaf
Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och grupper i Azure AD som behöver åtkomst till StarLeaf. Sedan kan du tilldela användare och grupper till StarLeaf genom att följa [dessa instruktioner](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Viktiga tips för att tilldela användare till StarLeaf

* Vi rekommenderar att en enda Azure AD-användare tilldelas StarLeaf för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och grupper kan tilldelas senare.

* När du tilldelar en användare till StarLeaf måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen Standardåtkomst är undantagna från etablering.

## <a name="set-up-starleaf-for-provisioning"></a>Ställ in StarLeaf för etablering

Innan du konfigurerar StarLeaf för automatisk användaretablering med Azure AD måste du konfigurera SCIM-etablering i StarLeaf:

1. Logga in på [din StarLeaf-administratörskonsol](https://portal.starleaf.com/#page=login). Navigera till **Integrationer** > **Lägg till integrering**.

    ![StarLeaf Lägg TILL SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Välj den **typ som** ska vara Microsoft Azure Active Directory. Ange ett lämpligt namn i **Namn**. Klicka på **Använd**.

    ![StarLeaf Lägg TILL SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  **SCIM-bas-URL:en** och **Åtkomsttokens** värden visas sedan. Dessa värden anges i fälten **Klient-URL** och **Hemlig token** på fliken Etablering i ditt StarLeaf-program i Azure-portalen. 

    ![Skapa token för StarLeaf](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Lägg till StarLeaf från galleriet

Om du vill konfigurera StarLeaf för automatisk användaretablering med Azure AD måste du lägga till StarLeaf från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till StarLeaf från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **StarLeaf**, välj **StarLeaf** på resultatpanelen.
    ![StarLeaf i resultatlistan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Konfigurera automatisk användaretablering till StarLeaf

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i StarLeaf baserat på användar- och/eller grupptilldelningar i Azure AD.

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **StarLeaf**i programlistan .

    ![StarLeaf-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet Administratörsautentiseringsuppgifter anger du **SCIM-bas-URL:en** och **åtkomsttokenvärden** som hämtats tidigare i **klient-URL** respektive **Hemlig token.** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till StarLeaf. Om anslutningen misslyckas kontrollerar du att ditt StarLeaf-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar rutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till StarLeaf**.

    ![Skapa token för StarLeaf](media/starleaf-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till StarLeaf i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i StarLeaf för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Skapa token för StarLeaf](media/starleaf-provisioning-tutorial/userattribute.png)


10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Om du vill aktivera Azure AD-etableringstjänsten för StarLeaf ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till StarLeaf genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på StarLeaf.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Begränsningar för anslutning

* StarLeaf stöder för närvarande inte gruppetablering. 
* StarLeaf kräver **e-post** och **userName-värden** för att ha samma källvärde.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md).
