---
title: 'Självstudiekurs: Konfigurera Comeet Recruiting Software för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Comeet Recruiting Software.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: f427fb75cfaeda79b037c327992e4ad482a7e689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058347"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Comeet Recruiting Software för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Comeet Recruiting Software och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Comeet Recruiting Software.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Comeet Rekrytera Software hyresgäst](https://www.comeet.co/)
* Ett användarkonto i Comeet Recruiting Software med administratörsbehörighet.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Lägg till Comeet Recruiting Software från galleriet

Innan du konfigurerar Comeet Recruiting Software för automatisk användaretablering med Azure AD måste du lägga till Comeet Recruiting Software från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Comeet Recruiting Software från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Comeet Recruiting Software**, väljer **Comeet Recruiting Software** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Comeet Recruiting Software i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Tilldela användare till Comeet Recruiting Software

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Comeet Recruiting Software. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Comeet Recruiting Software genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Viktiga tips för att tilldela användare till Comeet Recruiting Software

* Vi rekommenderar att en enda Azure AD-användare tilldelas Comeet Recruiting Software för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Comeet Recruiting Software måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Konfigurera automatisk användaretablering till Comeet Recruiting Software 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Comeet Recruiting Software baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Comeet Rekrytering Software, enligt instruktionerna i [Comeet Rekrytera Software enda inloggning handledning](comeetrecruitingsoftware-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Comeet Recruiting Software i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Comeet Recruiting Software**.

    ![Länken Comeet Recruiting Software i listan med program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **klientadressen** och **den hemliga token för** ditt Comeet Recruiting Softwares konto enligt beskrivningen i steg 6.

6. I [administratörskonsolen Comeet Recruiting Software](https://app.comeet.co/)navigerar du till **Comeet >-inställningar > autentisering > Microsoft Azure**och **kopierar den hemliga token för ditt företags** värde till fältet Hemlig **token** i Azure AD.

    ![Comeet Rekrytering Software Provisionering](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Comeet Recruiting Software. Om anslutningen misslyckas kontrollerar du att ditt Comeet Recruiting Software-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-token.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Comeet**.

    ![Comeet Rekrytera programvara användarkartor](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Granska användarattributen som synkroniseras från Azure AD till Comeet Recruiting Software i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Comeet Recruiting Software för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Comeet Rekrytera Software Group Attribut](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Comeet Recruiting Software ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Comeet Recruiting Software genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Comeet Recruiting Software.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Comeet Recruiting Software stöder för närvarande inte grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

