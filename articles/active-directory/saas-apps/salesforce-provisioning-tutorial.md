---
title: 'Självstudie: Konfigurera Salesforce för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f095c962f08ab0207ffc51d1c898570d9be7ea9a
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047243"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Salesforce för automatisk användar etablering

Syftet med den här självstudien är att visa de steg som krävs för att utföra i Salesforce och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till Salesforce.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En Salesforce.com-klient

> [!IMPORTANT]
> Om du använder ett Salesforce.com utvärderings konto kan du inte konfigurera automatisk användar etablering. Utvärderings kontona har inte den nödvändiga API-åtkomsten aktiverat förrän de har köpts. Du kan komma runt den här begränsningen genom att använda ett kostnads fritt [utvecklare](https://developer.salesforce.com/signup) för att slutföra den här kursen.

Om du använder en sandbox-miljö med en Salesforce-miljö kan du läsa [självstudien för Salesforce-integrering](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Tilldela användare till Salesforce

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till din Salesforce-app. När du har gjort det här beslutet kan du tilldela dessa användare till din Salesforce-app genom att följa instruktionerna i [tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Viktiga tips för att tilldela användare till Salesforce

* Vi rekommenderar att en enda Azure AD-användare tilldelas Salesforce för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Salesforce måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering

    > [!NOTE]
    > Den här appen importerar profiler från Salesforce som en del av etablerings processen, som kunden kanske vill välja när de tilldelar användare i Azure AD. Observera att profilerna som importeras från Salesforce visas som roller i Azure AD.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användar etablering

Det här avsnittet vägleder dig genom att ansluta Azure AD till Salesforces etablerings-API för användar konton och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i Salesforce baserat på användare och grupp tilldelning i Azure AD.

> [!Tip]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Salesforce genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användar konto

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar användar etablering av Active Directory användar konton till Salesforce.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

2. Om du redan har konfigurerat Salesforce för enkel inloggning söker du efter din instans av Salesforce med Sök fältet. Annars väljer du **Lägg till** och Sök efter **Salesforce** i program galleriet. Välj Salesforce från Sök resultaten och Lägg till det i listan över program.

3. Välj din instans av Salesforce och välj sedan fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**.

    ![etablerings](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Ange följande konfigurations inställningar under avsnittet **admin credentials** :

    a. I text rutan **admin-användar** namn anger du ett Salesforce-kontonamn som har **system administratörs** profilen i Salesforce.com tilldelad.

    b. I text rutan **Administratörs lösen ord** skriver du lösen ordet för det här kontot.

6. Om du vill hämta din Salesforce-säkerhetstoken öppnar du en ny flik och loggar in på samma Salesforce-administratörskonto. Klicka på ditt namn i det övre högra hörnet på sidan och klicka sedan på **Inställningar**.

    ![Aktivera automatisk användar etablering](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Aktivera automatisk användar etablering")

7. I det vänstra navigerings fönstret klickar du på **Mina personliga uppgifter** för att expandera det relaterade avsnittet och klicka sedan på **Återställ min säkerhetstoken**.
  
    ![Aktivera automatisk användar etablering](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Aktivera automatisk användar etablering")

8. På sidan **Återställ** säkerhetstoken klickar du på knappen **Återställ säkerhetstoken** .

    ![Aktivera automatisk användar etablering](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Aktivera automatisk användar etablering")

9. Kontrol lera e-postinkorgen som är associerad med det här administratörs kontot. Sök efter ett e-postmeddelande från Salesforce.com som innehåller den nya säkerhetstoken.

10. Kopiera token, gå till Azure AD-fönstret och klistra in den i fältet **hemlig token** .

11. **Klient-URL: en** måste anges om instansen av Salesforce finns på molnet för Salesforce-myndigheter. Annars är det valfritt. Ange klient webb adressen med formatet "https://\<Your-instance\>. my.salesforce.com" och ersätt \<din instance\> med namnet på din Salesforce-instans.

12. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din Salesforce-app.

13. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan nedan.

14. Klicka på **Spara.**  

15. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till Salesforce.**

16. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till Salesforce. Observera att attributen som har valts som **matchande** egenskaper används för att matcha användar kontona i Salesforce för uppdaterings åtgärder. Välj knappen Spara för att genomföra ändringarna.

17. Om du vill aktivera Azure AD Provisioning-tjänsten för Salesforce ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

18. Klicka på **Spara.**

> [!NOTE]
> När användarna har tillhandahållits i Salesforce-programmet måste administratören konfigurera språkspecifika inställningar för dem. I [den här](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artikeln finns mer information om språk konfiguration.

Detta startar den inledande synkroniseringen av alla användare och/eller grupper som tilldelats Salesforce i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i Salesforce-appen.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Vanliga problem
* Mappningen av standardattributet för etablering till Salesforce innehåller SingleAppRoleAssignments-uttrycket för att etablera användar roller i Salesforce. Se till att användarna inte har flera roller tilldelade till sig i programmet eftersom mappningen av attribut endast stöder etablering av en roll. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
