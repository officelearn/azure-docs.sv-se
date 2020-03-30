---
title: 'Självstudiekurs: Konfigurera 4me för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till 4me.
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
ms.openlocfilehash: 423ba8c7aea9659a4c91f68a01392954c2ba6db2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059193"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera 4me för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i 4me och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till 4me.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En 4me hyresgäst](https://www.4me.com/trial/)
* Ett användarkonto i 4me med administratörsbehörighet.

## <a name="add-4me-from-the-gallery"></a>Lägg till 4me från galleriet

Innan du konfigurerar 4me för automatisk användaretablering med Azure AD måste du lägga till 4me från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till 4me från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **4me,** väljer **4me** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![4me i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Tilldela användare till 4me

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till 4me. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till 4me genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Viktiga tips för att tilldela användare till 4me

* Vi rekommenderar att en enda Azure AD-användare tilldelas 4me för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till 4me måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurera automatisk användaretablering till 4me 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i 4me baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för 4me, enligt instruktionerna i [4me enda sign-on tutorial](4me-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för 4me i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **4me** i listan med program.

    ![4me-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Om du vill hämta **klientadressen** och **den hemliga token för** ditt 4me-konto följer du genomgången enligt beskrivningen i steg 6.

6. Logga in på administratörskonsolen på 4me. Navigera till **Inställningar**.

    ![4me Inställningar](media/4me-provisioning-tutorial/4me01.png)

    Skriv in **appar** i sökfältet.

    ![4me-appar](media/4me-provisioning-tutorial/4me02.png)

    Öppna **SCIM-listrutan** för att hämta den hemliga token och SCIM-slutpunkten.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till 4me. Om anslutningen misslyckas kontrollerar du att ditt 4me-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Users till 4me**.

    ![4me Användarmappningar](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Granska användarattributen som synkroniseras från Azure AD till 4me i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i 4me för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![4me Användarmappningar](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till 4me**.

    ![4me Användarmappningar](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Granska gruppattributen som synkroniseras från Azure AD till 4me i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i 4me för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![4me Gruppmappningar](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för 4me ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till 4me genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten den 4me.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* 4me har olika SCIM-slutpunktsadresser för test- och produktionsmiljöer. Den förstnämnda slutar med **.qa** medan den senare slutar med **.com**
* 4me genererade hemliga tokens har ett utgångsdatum på en månad från generering.
* 4me stöder inte **DELETE-åtgärder**

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)