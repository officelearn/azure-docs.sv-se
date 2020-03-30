---
title: 'Självstudiekurs: Konfigurera WSS (Symantec Web Security Service) för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till WSS (Symantec Web Security Service).
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
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063129"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera WSS (Symantec Web Security Service) för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i WSS (Symantec Web Security Service) och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till WSS (Symantec Web Security Service).

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En WSS-klient (Symantec Web Security Service)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Ett användarkonto i WSS (Symantec Web Security Service) med administratörsbehörighet.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Tilldela användare till WSS (Symantec Web Security Service)

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till WSS (Symantec Web Security Service). När du har bestämt dig kan du tilldela dessa användare och/eller grupper till WSS (Symantec Web Security Service) genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Viktiga tips för att tilldela användare till Symantec Web Security Service (WSS)

* Vi rekommenderar att en enda Azure AD-användare tilldelas WSS (Symantec Web Security Service) för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till WSS (Symantec Web Security Service) måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Konfigurera WSS (Setup Symantec Web Security Service) för etablering

Innan du konfigurerar WSS (Symantec Web Security Service) för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Symantec Web Security Service (WSS).

1. Logga in på [administratörskonsolen Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Navigera till **lösningstjänsten** > **Service**.

    ![Symantec Webbsäkerhetstjänst (WSS)](media/symantec-web-security-service/service.png)

2. Navigera till > **Kontounderhållsintegreringar** > **ny integrering**. **Account Maintenance**

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Välj **tredjepartsanvändare & grupper synkroniseras**. 

    ![Symantec webbsäkerhetstjänst](media/symantec-web-security-service/third-party-users.png)

4.  Kopiera **SCIM-URL:en** och **Token**. Dessa värden anges i fältet **Klient-URL** och **Hemlig token** på fliken Etablering i WSS-programmet (Symantec Web Security Service) i Azure-portalen.

    ![Symantec webbsäkerhetstjänst](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Lägg till WSS (Symantec Web Security Service) från galleriet

Om du vill konfigurera WSS (Symantec Web Security Service) för automatisk användaretablering med Azure AD måste du lägga till WSS (Symantec Web Security Service) från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till WSS (Symantec Web Security Service) från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Symantec Web Security Service**, väljer **Symantec Web Security Service** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Symantec Web Security Service (WSS) i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Konfigurera automatisk användaretablering till WSS (Symantec Web Security Service)

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i WSS (Symantec Web Security Service) baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för WSS (Symantec Web Security Service), i enlighet med instruktionerna i [WSS-självstudiekursen för enkel inloggning (Symantec Web Security Service).](symantec-tutorial.md) Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för WSS (Symantec Web Security Service) i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Symantec Web Security Service**i programlistan .

    ![Symantec Web Security Service-länken (WSS) i listan med program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet Administratörsautentiseringsuppgifter anger du **SCIM-URL:en** och **Tokenvärden** som hämtats tidigare i **klient-URL** respektive **Hemlig token.** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Symantec Web Security Service. Om anslutningen misslyckas kontrollerar du att ditt WSS-konto (Symantec Web Security Service) har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till WSS (Symantec Web Security Service).**

    ![WSS-användarmappningar (Symantec Web Security Service)](media/symantec-web-security-service/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till Symantec Web Security Service (WSS) i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i WSS (Symantec Web Security Service) för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![WSS-användarmappningar (Symantec Web Security Service)](media/symantec-web-security-service/userattribute.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Symantec Web Security Service**.

    ![WSS-användarmappningar (Symantec Web Security Service)](media/symantec-web-security-service/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Symantec Web Security Service (WSS) i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i WSS (Symantec Web Security Service) för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![WSS-användarmappningar (Symantec Web Security Service)](media/symantec-web-security-service/groupattribute.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Symantec Web Security Service ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till WSS (Symantec Web Security Service) genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [Hur lång tid tar det att etablera användare](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **Aktuell status** för att övervaka förloppet och följa länkar till din etableringsaktivitetsrapport, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Symantec Web Security Service (WSS). Mer information finns [i Kontrollera status för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Information om hur du läser Azure AD-etableringsloggarna finns i Rapportera om automatisk etablering av [användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
