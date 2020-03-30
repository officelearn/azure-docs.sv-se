---
title: 'Självstudiekurs: Konfigurera Infor CloudSuite för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057557"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Infor CloudSuite för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Infor CloudSuite och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Infor CloudSuite.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Infor CloudSuite-klient](https://www.infor.com/products/infor-os)
* Ett användarkonto i Infor CloudSuite med administratörsbehörighet.

## <a name="assigning-users-to-infor-cloudsuite"></a>Tilldela användare till Infor CloudSuite

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Infor CloudSuite. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Infor CloudSuite genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Viktiga tips för att tilldela användare till Infor CloudSuite

* Vi rekommenderar att en enda Azure AD-användare tilldelas Infor CloudSuite för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Infor CloudSuite måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Konfigurera Infor CloudSuite för etablering

1. Logga in på din [Infor CloudSuite-administratörskonsol](https://www.infor.com/customer-center). Klicka på användarikonen och navigera sedan till **användarhantering**.

    ![Administratörskonsolen Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Klicka på menyikonen längst upp i det vänstra övre hörnet av skärmen. Klicka på **Hantera**.

    ![Infor CloudSuite Lägg till SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigera till **SCIM-konton**.

    ![Infor CloudSuite SCIM-konto](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Lägg till en administratörsanvändare genom att klicka på plusikonen. Ange ett **SCIM-lösenord** och skriv samma lösenord under **Bekräfta lösenord**. Klicka på mappikonen för att spara lösenordet. Du kommer då att se en **användaridentifierare** som genereras för administratörsanvändaren.

    ![Infor CloudSuite Admin-användare](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite-lösenord](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite-identifierare](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Om du vill generera innehavartoken **kopierar du användaridentifieraren** och **SCIM-lösenordet**. Klistra in dem i anteckningar ++ åtskilda av ett kolon. Koda strängvärdet genom att navigera till **plugins > MIME Tools > Basic64-kod**. 

    ![Infor CloudSuite-identifierare](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Kopiera innehavartoken. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt Infor CloudSuite-program i Azure-portalen.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Lägg till Infor CloudSuite från galleriet

Innan du konfigurerar Infor CloudSuite för automatisk användaretablering med Azure AD måste du lägga till Infor CloudSuite från Azure AD-programgalleriet i din lista över hanterade SaaS-program.

**Så här lägger du till Infor CloudSuite från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Infor CloudSuite**, väljer **Infor CloudSuite** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Infor CloudSuite i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Konfigurera automatisk användaretablering till Infor CloudSuite 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Infor CloudSuite baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Infor CloudSuite, i enlighet med instruktionerna i [infor CloudSuite Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om Infor CloudSuites SCIM-slutpunkt finns [i detta](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Infor CloudSuite i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Infor CloudSuite**i programlistan .

    ![Länken Infor CloudSuite i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` **klient-URL.** Mata in innehavartokenvärdet som hämtats tidigare i **Hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Infor CloudSuite. Om anslutningen misslyckas kontrollerar du att ditt Infor CloudSuite-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Infor CloudSuite**.

    ![Infor CloudSuite användarmappningar](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Infor CloudSuite i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Infor CloudSuite för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Infor CloudSuite-användarattribut](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Infor CloudSuite**.

    ![Infor CloudSuite Gruppmappningar](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Infor CloudSuite i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Infor CloudSuite för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Infor CloudSuite-gruppattribut](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Infor CloudSuite ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Infor CloudSuite genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Infor CloudSuite.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)