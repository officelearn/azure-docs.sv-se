---
title: 'Självstudie: Konfigurera Salesforce Sandbox för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: d6ec5e0c2c3a83335dfcb7e3bcc048dd66494e94
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447809"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Salesforce Sandbox för automatisk användaretablering

Målet med den här självstudien är att visa dig vad du behöver för att utföra i Salesforce sandbox-miljön och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Salesforce Sandbox.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En giltig klient för Salesforce-Sandbox för arbete eller Salesforce Sandbox för utbildning. Du kan använda ett kostnadsfritt utvärderingskonto för någon av tjänsterna.
*   Ett användarkonto i Salesforce Sandbox-teamet administratörsbehörigheter.

## <a name="assigning-users-to-salesforce-sandbox"></a>Tilldela användare till Salesforce Sandbox

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till din Salesforce-Sandbox-app. När du har gjort detta beslut, du kan tilldela dessa användare till din Salesforce-Sandbox-app genom att följa instruktionerna i [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Viktiga tips för att tilldela användare till Salesforce Sandbox

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Salesforce Sandbox att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Salesforce sandbox-miljön, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

> [!NOTE]
> Den här appen importerar anpassade roller från Salesforce Sandbox som en del av etableringen, som kunden kanske du väljer när du tilldelar användare.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Salesforce Sandbox användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelad användare som har konton i Salesforce Sandbox baserat på användare och grupper tilldelning i Azure AD.

>[!Tip]
>Du kan också välja att aktiverat SAML-baserad enkel inloggning för Salesforce begränsat läge, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens kontoetablering

Målet med det här avsnittet som beskriver hur du aktiverar etableringen av användare i Active Directory-användarkonton till Salesforce Sandbox.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

1. Om du redan har konfigurerat Salesforce begränsat läge för enkel inloggning, söka efter din instans av Salesforce sandbox-miljön med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Salesforce Sandbox** i programgalleriet. Välj Salesforce Sandbox i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av Salesforce-Sandbox och välj sedan den **etablering** fliken.

1. Ange den **Etableringsläge** till **automatisk**.

    ![etablering](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Under den **administratörsautentiseringsuppgifter** avsnittet tillhandahåller följande konfigurationsinställningar:
   
    a. I den **Admin Username** textrutan typ som en Salesforce-Sandbox kontonamn som har den **systemadministratören** profil i Salesforce.com som tilldelats.
   
    b. I den **adminlösenord** textrutan skriver du lösenordet för det här kontot.

1. Om du vill hämta dina Salesforce-Sandbox säkerhetstoken, öppna en ny flik och logga till samma Salesforce Sandbox-administratörskonto. I det övre högra hörnet på sidan klickar du på ditt namn och sedan på **inställningar**.

     ![Aktivera automatisk användaretablering](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "aktivera automatisk användaretablering")

1. I det vänstra navigeringsfönstret klickar du på **mina personuppgifter** Expandera avsnittet relaterade och klicka sedan på **återställa Mina Security Token**.
  
    ![Aktivera automatisk användaretablering](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "aktivera automatisk användaretablering")

1. På den **återställa Security Token** klickar du på den **återställa Security Token** knappen.

    ![Aktivera automatisk användaretablering](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "aktivera automatisk användaretablering")

1. Kontrollera Inkorgen för e-post som är associerade med den här administratörskonto. Leta efter ett e-postmeddelande från Salesforce-Sandbox.com som innehåller nya säkerhetstoken.

1. Kopiera token, gå till din Azure AD-fönstret och klistra in den i den **hemlighet Token** fält.

1. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din Salesforce-Sandbox-app.

1. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden om etablering fel och markera kryssrutan.

1. Klicka på **spara.**  
    
1.  Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare i Salesforce sandlådan.**

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till Salesforce Sandbox. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Salesforce sandbox-miljön för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för Salesforce begränsat läge, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

1. Klicka på **spara.**

Den startar den första synkroniseringen av användare och/eller grupper som tilldelats till Salesforce Sandbox i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten i Salesforce Sandbox-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
