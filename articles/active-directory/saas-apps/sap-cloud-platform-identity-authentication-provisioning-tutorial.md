---
title: 'Självstudiekurs: Konfigurera SAP Cloud Platform Identity Authentication för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till SAP Cloud Platform Identity Authentication.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060488"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera SAP Cloud Platform Identity Authentication för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i SAP Cloud Platform Identity Authentication och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till SAP Cloud Platform Identity Authentication.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En klient för identitetsautentisering för SAP-molnplattform](https://cloudplatform.sap.com/pricing.html)
* Ett användarkonto i SAP Cloud Platform Identity Authentication med administratörsbehörigheter.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Tilldela användare till SAP Cloud Platform Identity Authentication

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till SAP Cloud Platform Identity Authentication. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till SAP Cloud Platform Identity Authentication genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Viktiga tips för att tilldela användare till SAP Cloud Platform Identity Authentication

* Vi rekommenderar att en enda Azure AD-användare tilldelas SAP Cloud Platform Identity Authentication för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till SAP Cloud Platform Identity Authentication måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Konfigurera SAP Cloud Platform Identity Authentication för etablering

1. Logga in på [administratörskonsolen för ADMINISTRATION](https://sapmsftintegration.accounts.ondemand.com/admin)AV SAP Cloud Platform Identity. Navigera till **användare & auktoriseringar > administratörer**.

    ![Adminkonsol för administration av identitetsautentisering för SAP-molnplattform](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Skapa en administratörsanvändare och välj användaren.  

3.  Under Konfigurera auktoriseringar aktiverar du växlingsknappen mot **Hantera användare** och **hantera grupper**.

    ![SAP Cloud Platform Identity Authentication Lägg till SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Du kommer att få ett e-postmeddelande för att aktivera ditt konto och ange ett lösenord för **SAP Cloud Platform Identity Authentication Service**.

4.  Kopiera **användar-ID** och **lösenord**. Dessa värden anges i fälten Administratörsanvändarnamn respektive adminlösenord på fliken Etablering i ditt SAP Cloud Platform Identity Authentication-program i Azure-portalen.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Lägg till SAP Cloud Platform Identity Authentication från galleriet

Innan du konfigurerar SAP Cloud Platform Identity Authentication för automatisk användaretablering med Azure AD måste du lägga till SAP Cloud Platform Identity Authentication från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till SAP Cloud Platform Identity Authentication från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **SAP Cloud Platform Identity Authentication**, väljer SAP Cloud Platform Identity **Authentication** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![SAP Cloud Platform Identity Authentication i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Konfigurera automatisk användaretablering till SAP Cloud Platform Identity Authentication 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i SAP Cloud Platform Identity Authentication baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för SAP Cloud Platform Identity Authentication , enligt instruktionerna i [SAP Cloud Platform Identity Authentication Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för SAP Cloud Platform Identity Authentication i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SAP Cloud Platform Identity Authentication**.

    ![Länken för SAP Cloud Platform Identity Authentication i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://<tenantID>.accounts.ondemand.com/service/scim ` **klient-URL.** Ange **värdena användar-ID** och **lösenord** som hämtats tidigare i **administratörsanvändar-** respektive **administratörslösenordet.** Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till SAP Cloud Platform Identity Authentication. Om anslutningen misslyckas kontrollerar du att ditt SAP Cloud Platform Identity Authentication-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till SAP Cloud Platform Identity Authentication**.

    ![Användare av ANVÄNDARE AV SAP Cloud Platform Identity-autentisering](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till SAP Cloud Platform Identity Authentication i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i SAP Cloud Platform Identity Authentication för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användare av användare av SAP Cloud Platform Identity-identitet](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för SAP Cloud Platform Identity Authentication ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till SAP Cloud Platform Identity Authentication genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på SAP Cloud Platform Identity Authentication.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* SAP Cloud Platform Identity Authentications SCIM-slutpunkt kräver att vissa attribut är av specifikt format. Du kan veta mer om dessa attribut och deras specifika format [här](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

