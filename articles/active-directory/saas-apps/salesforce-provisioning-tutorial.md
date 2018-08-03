---
title: 'Självstudie: Konfigurera Salesforce för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 9ece2e0f56522582e53e827ac6db7f33b1c8cb7e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432556"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Salesforce för automatisk användaretablering

Målet med den här självstudien är att visa steg som krävs för att utföra i Salesforce och Azure AD för att automatiskt etablera och användares användarkonton från Azure AD för till Salesforce.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient
*   En Salesforce.com-klientorganisation

>[!IMPORTANT] 
>Om du använder ett utvärderingskonto för Salesforce.com, sedan går du inte att konfigurera automatisk användaretablering. Utvärderingskonton har inte de nödvändiga API-åtkomst aktiverat tills de köps. Du kan komma runt denna begränsning med hjälp av en kostnadsfri [utvecklarkonto](https://developer.salesforce.com/signup) att slutföra den här självstudien.

Om du använder en testmiljö för Salesforce, finns det [Salesforce Sandbox-integrationsvägledning](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Tilldela användare till Salesforce

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till ditt Salesforce-app. När du har gjort detta beslut, du kan tilldela dessa användare till din Salesforce-app genom att följa instruktionerna i [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Viktiga tips för att tilldela användare till Salesforce

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Salesforce för att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*  Du måste välja en giltig användarroll när du tilldelar en användare till Salesforce. Rollen ”standard åtkomst” fungerar inte för etablering

    > [!NOTE]
    > Den här appen importerar anpassade roller från Salesforce som en del av etableringen, som kunden kanske du väljer när du tilldelar användare

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Salesforces användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i Salesforce baserat på användar- och grupptilldelningar i Azure AD.

>[!Tip]
>Du kan också välja att aktiveras SAML-baserad enkel inloggning för Salesforce, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens kontoetablering

Målet med det här avsnittet som beskriver hur du aktiverar etableringen av användare i Active Directory-användarkonton till Salesforce.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

1. Om du redan har konfigurerat Salesforce för enkel inloggning, söka efter din instans av Salesforce med sökfältet. Annars väljer **Lägg till** och Sök efter **Salesforce** i programgalleriet. Välj Salesforce i sökresultatet och lägga till den i din lista över program.

1. Välj din instans av Salesforce och välj sedan den **etablering** fliken.

1. Ange den **Etableringsläge** till **automatisk**.

    ![etablering](./media/salesforce-provisioning-tutorial/provisioning.png)

1. Under den **administratörsautentiseringsuppgifter** avsnittet tillhandahåller följande konfigurationsinställningar:
   
    a. I den **Admin Username** textrutan skriver ett Salesforce-kontonamn som har den **systemadministratören** profil i Salesforce.com som tilldelats.
   
    b. I den **adminlösenord** textrutan skriver du lösenordet för det här kontot.

1. Om du vill hämta din Salesforce säkerhetstoken, öppna en ny flik och logga till samma Salesforce-administratörskonto. I det övre högra hörnet på sidan klickar du på ditt namn och sedan på **inställningar**.

     ![Aktivera automatisk användaretablering](./media/salesforce-provisioning-tutorial/sf-my-settings.png "aktivera automatisk användaretablering")

1. I det vänstra navigeringsfönstret klickar du på **mina personuppgifter** Expandera avsnittet relaterade och klicka sedan på **återställa Mina Security Token**.
  
    ![Aktivera automatisk användaretablering](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "aktivera automatisk användaretablering")

1. På den **återställa Security Token** klickar du på **återställa Security Token** knappen.

    ![Aktivera automatisk användaretablering](./media/salesforce-provisioning-tutorial/sf-reset-token.png "aktivera automatisk användaretablering")

1. Kontrollera Inkorgen för e-post som är associerade med den här administratörskonto. Leta efter ett e-postmeddelande från Salesforce.com som innehåller nya säkerhetstoken.

1. Kopiera token, gå till din Azure AD-fönstret och klistra in den i den **hemlighet Token** fält.

1. Den **klient-URL** ska anges om Salesforce-instansen finns på Salesforce Government-molnet. I annat fall kan det är valfritt. Ange klient-URL i formatet för ”https://\<din instans\>. my.salesforce.com”, ersätter \<din instans\> med namnet på din Salesforce-instans.

1. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till ditt Salesforce-app.

1. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden om etablering fel och markera kryssrutan nedan.

1. Klicka på **spara.**  
    
1.  Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Salesforce.**

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till Salesforce. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkonton i Salesforce för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för Salesforce, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

1. Klicka på **spara.**

Detta startar den första synkroniseringen av användare och/eller grupper som har tilldelats till Salesforce i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på din Salesforce-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
