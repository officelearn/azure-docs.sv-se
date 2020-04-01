---
title: 'Självstudiekurs: Konfigurera Cisco Webex för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Cisco Webex.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77058526"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Cisco Webex för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Cisco Webex och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare till Cisco Webex.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En Cisco Webex-klient .](https://www.webex.com/pricing/index.html)
* Ett användarkonto i Cisco Webex med administratörsbehörighet.

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

Innan du konfigurerar Cisco Webex för automatisk användaretablering med Azure AD måste du lägga till Cisco Webex från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Cisco Webex från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cisco Webex**, väljer **Cisco Webex** på resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

    ![Cisco Webex i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Tilldela användare till Cisco Webex

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare i Azure AD som behöver åtkomst till Cisco Webex. När du har bestämt dig kan du tilldela dessa användare till Cisco Webex genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Viktiga tips för att tilldela användare till Cisco Webex

* Vi rekommenderar att en enda Azure AD-användare tilldelas Cisco Webex för att testa konfigurationen för automatisk användaretablering. Ytterligare användare kan tilldelas senare.

* När du tilldelar en användare till Cisco Webex måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurera automatisk användaretablering till Cisco Webex

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare i Cisco Webex baserat på användartilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Cisco Webex i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Enterprise Applications**, välj **Alla program**och välj sedan **Cisco Webex**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Cisco Webex** i listan över program.

    ![Cisco Webex-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Cisco Webex-etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Cisco Webex-etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **klient-URL:en**och **hemlig token för** ditt Cisco Webex-konto.

    ![Cisco Webex-etablering](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  Ange **Tenant URL** ett värde i form av `https://api.ciscoweb.com/v1/scim/[OrgId]`. `[OrgId]`Logga in på [Cisco Webex Control Hub](https://admin.webex.com/login). Klicka på organisationens namn längst ned till vänster och kopiera värdet från **organisations-ID**. 

    * Om du vill hämta värdet för **hemlig token**navigerar du till den här [URL:en](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Logga in med hela Cisco Webex-administratörskontot för din organisation på inloggningssidan webex. En felsida visas som säger att webbplatsen inte kan nås, men det är normalt.

        ![Cisco Webex-etablering](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Kopiera värdet för den genererade innehavartoken från webbadressen enligt beskrivningen nedan. Denna token är giltig i 365 dagar.
        
        ![Cisco Webex-etablering](./media/cisco-webex-provisioning-tutorial/test1.png)

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Cisco Webex. Om anslutningen misslyckas kontrollerar du att ditt Cisco Webex-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Cisco Webex**.

    ![Cisco Webex-etablering](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Granska användarattributen som synkroniseras från Azure AD till Cisco Webex i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Cisco Webex för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Cisco Webex-etablering](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Cisco Webex ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Cisco Webex genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Cisco Webex.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Cisco Webex är för närvarande i Ciscos fas för tidig fälttestning (EFT). Kontakta [Ciscos supportteam](https://www.webex.co.in/support/support-overview.html)om du vill ha mer information. 
* Mer information om Cisco Webex-konfiguration finns i Cisco-dokumentationen [här](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)