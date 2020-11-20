---
title: 'Självstudie: Konfigurera innehåll för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till innehåll.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: Zhchia
ms.openlocfilehash: dfec022759cdc51b7ce52eda7b53ec3cb4b7660b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974870"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Självstudie: Konfigurera innehåll för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både innehålls lös och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper till [innehåll](https://www.contentful.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare med innehåll
> * Ta bort användare i innehåll utan att ha åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och innehåll
> * Etablera grupper och grupp medlemskap i innehåll
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial) till innehåll (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* Ett innehålls administrerat organisations konto med en prenumeration som stöder SCIM-etablering. Kontakta innehåll via [support@contentful.com](mailto:support@contentful.com) om du har frågor om din organisations prenumeration.
 
## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som finns i [etableringsomfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och contentable](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-contentful-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera innehåll som stöder etablering med Azure AD

1. Skapa ett **användar** konto för tjänsten med innehåll. Alla etablerings behörigheter för Azure kommer att tillhandahållas via det här kontot. Vi rekommenderar att du väljer **ägare** som organisations roll för det här kontot.

2. Logga in till innehåll som en **tjänst användare** som du skapade i föregående steg.

3. Navigera till **vänster Slider**  ->  **organisations inställningar**  ->  **åtkomst verktyg**  ->  **användar etablering**.

    ![Meny](media/contentful-provisioning-tutorial/access.png)

4. Kopiera och spara **scim-URL: en**. Det här värdet anges på fliken etablering i det innehålls bara programmet i Azure Portal.

5. Klicka på **generera personlig** åtkomsttoken.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Ge din personliga åtkomsttoken ett meningsfullt namn och klicka på generera i fönstret modal.
    
7. **Scim-URL:** en och den **hemliga token** kommer att genereras. Kopiera och spara dessa värden. Dessa värden kommer att anges på fliken etablering i ditt innehålls begränsande program i Azure Portal.

    ![access](media/contentful-provisioning-tutorial/token.png)


Kontakta [support@contentful.com](mailto:support@contentful.com) om du har frågor när du konfigurerar etableringen i den innehållsbaserade administratörs konsolen.


## <a name="step-3-add-contentful-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till innehåll med hjälp av Azure AD-programgalleriet

Lägg till innehåll från Azure AD-programgalleriet för att börja hantera etablering till innehåll. Om du tidigare har konfigurerat innehåll för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till innehåll måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-contentful"></a>Steg 5. Konfigurera automatisk användar etablering till innehåll 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Konfigurera automatisk användar etablering för innehåll i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **innehållet** i listan program.

    ![Innehålls länkningen i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in din innehålls beskrivna URL och hemlig token. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till innehåll. Om anslutningen Miss lyckas kontrollerar du att ditt innehålls exklusivt konto har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till innehåll**.

9. Granska de användarattribut som synkroniseras från Azure AD till innehåll i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i innehåll som kan åtgärdas. Om du väljer att ändra det [matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att INNEHÅLLS utan API stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |name.givenName|Sträng|
   |name.familyName|Sträng|

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till innehåll**.

11. Granska gruppattributen som synkroniseras från Azure AD till innehåll i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupper med innehåll som kan användas för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för innehåll kan du ändra **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till innehåll genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
