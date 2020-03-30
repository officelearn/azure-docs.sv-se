---
title: 'Självstudiekurs: Konfigurera Zscaler Private Access (ZPA) för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Zscaler Private Access (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064163"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Zscaler Private Access (ZPA) för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Zscaler Private Access (ZPA) och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Zscaler Private Access (ZPA).

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Zscaler Private Access -klient (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Ett användarkonto i Zscaler Private Access (ZPA) med administratörsbehörighet.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Tilldela användare till Zscaler Private Access (ZPA)

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler Private Access (ZPA). När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Zscaler Private Access (ZPA) genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Viktiga tips för att tilldela användare till Zscaler Private Access (ZPA)

* Vi rekommenderar att en enda Azure AD-användare tilldelas Zscaler Private Access (ZPA) för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler Private Access (ZPA) måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Konfigurera Zscaler Private Access (ZPA) för etablering

1. Logga in på [Zscaler Private Access (ZPA) Admin Console](https://admin.private.zscaler.com/). Navigera till **Administration > IdP-konfiguration**.

    ![Zscaler Private Access (ZPA) Admin Console](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Kontrollera att en IdP för **enkel inloggning** är konfigurerad. Om ingen IdP är inställd, sedan lägga till en genom att klicka på plus ikonen längst upp till höger på skärmen.

    ![Zscaler Private Access (ZPA) Lägg till SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Följ igenom guiden **Lägg till IdP-konfiguration** om du vill lägga till ett IDP. Lämna fältet **Enkel inloggning** inställt på **Användare**. Ange ett **namn** och välj **domäner** i listrutan. Klicka på **Nästa** för att navigera till nästa fönster.

    ![Zscaler Private Access (ZPA) Lägg till IDP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Hämta **serviceprovidercertifikatet**. Klicka på **Nästa** för att navigera till nästa fönster.

    ![Zscaler Private Access (ZPA) SP-certifikat](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. I nästa fönster laddar du upp **serviceprovidercertifikatet** som hämtats tidigare.

    ![Zscaler Private Access (ZPA) ladda upp certifikat](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Bläddra nedåt för att ange **url för enkel inloggning** och **IdP-enhets-ID**.

    ![Zpa-ID (Private Access) (ZSCALER Private Access)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Bläddra nedåt för att **aktivera SCIM-synkronisering**. Klicka på **Knappen Generera ny token.** Kopiera **innehavartoken**. Det här värdet anges i fältet Hemlig token på fliken Etablering i Zscaler Private Access -programmet (ZPA) i Azure-portalen.

    ![Zscaler Private Access (ZPA) Skapa token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Om du vill hitta **klient-URL:en** navigerar du till **Administration > IdP-konfiguration**. Klicka på namnet på den nyligen tillagda IdP-konfigurationen som visas på sidan.

    ![Zscaler Private Access (ZPA) Idp-namn](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Bläddra nedåt för att visa slutpunkten för **SCIM-tjänstprovidern** i slutet av sidan. Kopiera **slutpunkten för SCIM-tjänstprovidern**. Det här värdet anges i fältet Klient-URL på fliken Etablering i Zscaler Private Access (ZPA) i Azure-portalen.

    ![Zscaler Private Access (ZPA) SCIM-URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Lägg till Zscaler Private Access (ZPA) från galleriet

Innan du konfigurerar Zscaler Private Access (ZPA) för automatisk användaretablering med Azure AD måste du lägga till Zscaler Private Access (ZPA) från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Zscaler Private Access (ZPA) från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Zscaler Private Access (ZPA),** väljer **Zscaler Private Access (ZPA)** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Zscaler Private Access (ZPA) i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Konfigurera automatisk användaretablering till Zscaler Private Access (ZPA) 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Zscaler Private Access (ZPA) baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zscaler Private Access (ZPA) genom att följa instruktionerna i [Zscaler Private Access (ZPA) Enkel inloggningskurs](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om Zscaler Private Accesss SCIM-slutpunkt finns [i detta](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Zscaler Private Access (ZPA) i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Zscaler Private Access (ZPA) i**programlistan .

    ![Zscaler Private Access (ZPA) i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **slutpunkten för SCIM-tjänstprovider** som hämtats tidigare i **klient-URL.** Mata in värdet **bärare token** som hämtats tidigare i **Hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Zscaler Private Access (ZPA). Om anslutningen misslyckas kontrollerar du att ditt Zscaler Private Access-konto (ZPA) har administratörsbehörighet och försök igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Zscaler Private Access (ZPA)**.

    ![Zscaler Private Access (ZPA) Användarmappningar](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Zscaler Private Access (ZPA) i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Zscaler Private Access (ZPA) för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Zscaler Private Access (ZPA) Användarattribut](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Zscaler Private Access (ZPA)**.

    ![Zscaler Private Access (ZPA) Gruppmappningar](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Zscaler Private Access (ZPA) i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Zscaler Private Access (ZPA) för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Zscaler Private Access (ZPA) Gruppattribut](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Zscaler Private Access (ZPA) ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Zscaler Private Access (ZPA) genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Zscaler Private Access (ZPA).

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

