---
title: 'Självstudiekurs: Konfigurera Federerad katalog för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Federerad katalog.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057953"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Federerad katalog för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Federerad katalog och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Federerad katalog.

> [!NOTE]
>  Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En federerad katalog](https://www.federated.directory/pricing).
* Ett användarkonto i Federerad katalog med administratörsbehörighet.

## <a name="assign-users-to-federated-directory"></a>Tilldela användare till federerad katalog
Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Federerad katalog. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Federerad katalog genom att följa instruktionerna här:

 * [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Viktiga tips för att tilldela användare till Federerad katalog
 * Vi rekommenderar att en enda Azure AD-användare tilldelas Federerad katalog för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Federerad katalog måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen Standardåtkomst är undantagna från etablering.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Ställ in Federerad katalog för etablering

Innan du konfigurerar Federerad katalog för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Federerad katalog.

1. Logga in på [administratörskonsolen för federerad katalog](https://federated.directory/of)

    ![Självstudiekurs för Federerad katalog](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navigera till **kataloger > användarkataloger** och välj din klient. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Om du vill generera en permanent innehavartoken navigerar du till **katalognycklar > skapa ny nyckel.** 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated01.png)

4. Skapa en katalognyckel. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Kopiera värdet **access token.** Det här värdet anges i fältet **Hemlig token** på fliken Etablering i ditt Federerade katalogprogram i Azure-portalen. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Lägga till federerad katalog från galleriet

Om du vill konfigurera Federerad katalog för automatisk användaretablering med Azure AD måste du lägga till Federerad katalog från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Federerad katalog från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Federerad katalog**, välj **Federerad katalog** på resultatpanelen.

    ![Federerad katalog i resultatlistan](common/search-new-app.png)

5. Navigera till **webbadressen** som markeras nedan i en separat webbläsare. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klicka på **LOGGA IN**.

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Eftersom Federerad katalog är en OpenIDConnect-app väljer du att logga in på Federerad katalog med ditt Microsoft-arbetskonto.
    
    ![federerad katalog](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Efter en lyckad autentisering godkänner du samtyckesprompten för samtyckessidan. Programmet läggs sedan automatiskt till din klient och du kommer att omdirigeras till ditt Federerade katalogkonto.

    ![federerad katalog Lägg till SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurera automatisk användaretablering till Federerad katalog 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Federerad katalog baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Federerad katalog i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Federerad katalog**i programlistan .

    ![Länken Federerad katalog i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://api.federated.directory/v2/` klient-URL:en. Ange värdet som du hämtade och sparade tidigare från Federerad katalog i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Federerad katalog. Om anslutningen misslyckas kontrollerar du att ditt Federerade katalogkonto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Federerad katalog**.

    ![Självstudiekurs för Federerad katalog](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Granska användarattributen som synkroniseras från Azure AD till Federerad katalog i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Federerad katalog för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Självstudiekurs för Federerad katalog](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Federerad katalog ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Federerad katalog genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Federerad katalog.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
