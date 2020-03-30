---
title: 'Självstudiekurs: Konfigurera Fuze för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Fuze.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: a58402297380116f83214e52ae7f2796412755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057920"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Fuze för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Fuze och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till Fuze.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En Fuze hyresgäst](https://www.fuze.com/).
* Ett användarkonto i Fuze med administratörsbehörighet.

## <a name="assigning-users-to-fuze"></a>Tilldela användare till Fuze

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Fuze. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Fuze genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-fuze"></a>Viktiga tips för att tilldela användare till Fuze

* Vi rekommenderar att en enda Azure AD-användare tilldelas Fuze för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Fuze måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-fuze-for-provisioning"></a>Setup Fuze för etablering

Innan du konfigurerar Fuze för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Fuze. 

1. Börja med att kontakta din Fuze-representant för följande information:

    * Lista över Fuze Product SKU:er som för närvarande används på ditt företag.
    * Lista över platskoder för företagets platser.
    * Lista över avdelningskoder för ditt företag.

2. Du hittar dessa SKU:er och koder i dina Fuze-kontrakts- och konfigurationsdokument, eller genom att kontakta din Fuze-representant.

3. När kraven har tagits emot kommer din Fuze-representant att förse dig med Fuze-autentiseringstoken som krävs för att aktivera integrationen. Det här värdet anges i fältet Hemlig token på fliken Etablering i fuze-programmet i Azure-portalen.

## <a name="add-fuze-from-the-gallery"></a>Lägg till Fuze från galleriet

Innan du konfigurerar Fuze för automatisk användaretablering med Azure AD måste du lägga till Fuze från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Fuze från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Fuze,** väljer **Fuze** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Fuze i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-fuze"></a>Konfigurera automatisk användaretablering till Fuze 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Fuze baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Fuze , enligt instruktionerna i [Fuze Single sign-on tutorial](fuze-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Fuze i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Fuze**.

    ![Länken för Fuze i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://api.fuze.com/scim/v2` **klient-URL.** Mata in **scim-autentiseringstokenvärdet** som hämtats tidigare från Fuze-representanten i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Fuze. Om anslutningen misslyckas kontrollerar du att ditt Fuze-konto har administratörsbehörighet och försöker igen.

    ![Url-token för klient](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Fuze**.

    ![Fuze användarkartor](media/fuze-provisioning-tutorial/image01.png)

9. Granska användarattributen som synkroniseras från Azure AD till Fuze i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Fuze för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Fuze användarkartor](media/fuze-provisioning-tutorial/image00.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Fuze ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Fuze genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Fuze.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Fuze stöder anpassade SCIM-attribut som kallas **rättigheter**. Dessa attribut kan bara skapas och inte uppdateras. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md).