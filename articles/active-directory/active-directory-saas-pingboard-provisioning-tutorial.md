---
title: "Självstudier: Konfigurera Pingboard för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Pingboard."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Pingboard för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du måste utföra om du vill aktivera automatisk etablering och avetablering av användarkonton från Azure Active Directory till Pingboard.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient
*   En klient Pingboard [Pro-konto](https://pingboard.com/pricing) 
*   Ett användarkonto i Pingboard med administratörsbehörigheter 

> [!NOTE] 
> Azure Active Directory-etablering integration förlitar sig på Pingboard-API (`https://your_domain.pingboard.com/scim/v2`) som är tillgänglig för ditt konto.

## <a name="assigning-users-to-pingboard"></a>Tilldela användare till Pingboard

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda program. I samband med automatisk konto användaretablering, synkroniseras de användare som har ”tilldelats” till ett program i Azure Active Directory. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare i Azure Active Directory representerar de användare som behöver åtkomst till appen Pingboard. När bestämt, kan du tilldela dessa användare i appen Pingboard genom att följa anvisningarna här:

[Tilldela en användare till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Viktiga tips för att tilldela användare till Pingboard

*   Det rekommenderas att du tilldelar en enskild användare i Azure Active Directory Pingboard att testa allokering konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configuring-user-provisioning-to-pingboard"></a>Konfigurering av användarförsörjning till Pingboard 

Det här avsnittet hjälper dig att ansluta din Azure Active Directory till Pingboard användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelade användarkonton i Pingboard baserat på Användartilldelning i Azure Active Directory.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Pingboard, följer du instruktionerna som anges i [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Konfigurera automatisk konto användaretablering till Pingboard i Azure Active Directory:

1)  I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

2) Om du redan har konfigurerat Pingboard för enkel inloggning, söka efter din instans av Pingboard med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Pingboard** i programgalleriet. Välj **Pingboard** i sökresultatet och lägga till den i listan över program.

3)  Välj din instans av Pingboard och sedan den **etablering** fliken.

4)  Ange den **Etableringsläge** till **automatisk**.

![Pingboard etablering](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) Under avsnittet autentiseringsuppgifter som administratör utför du följande steg:

* I den **klient URL** textruta ange `https://your_domain.pingboard.com/scim/v2` och Ersätt domän med verkliga domänen
* Logga in på [Pingboard](https://pingboard.com/) med administratörskonto.
* Klicka på tillägg > integreringar > Azure Active Directory.
* Klicka på Konfigurera fliken och markera **aktivera användarförsörjning från Azure**.
* Kopiera den **OAuth ägar-Token** och Skriv in i **hemlighet Token** textruta.

6) I Azure-portalen klickar du på **Testanslutningen** så Azure Active Directory kan ansluta till din Pingboard app. Om anslutningen misslyckas, kan du kontrollera att ditt Pingboard-konto som har administratörsbehörigheter och försök den **”Testa anslutning”** steg igen.

7) Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera följande kryssruta.

8) Klicka på **Spara**. 

9) Välj under avsnittet mappningar **synkronisera Azure Active Directory-användare Pingboard**.

10) I den **attributmappning** avsnittet kan du granska användarattribut ska synkroniseras från Azure Active Directory med Pingboard. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton i Pingboard för uppdateringsåtgärder. Välj den **spara** för att genomföra ändringarna. Mer information finns i [anpassa användaren etablering attributmappning](active-directory-saas-customizing-attribute-mappings.md).

11) Om du vill aktivera Azure Active Directory-tjänsten för Pingboard-etablering, ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

12) Klicka på **spara** att starta den första synkroniseringen av användare som tilldelats Pingboard.

Första tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering i appen Pingboard.

Mer information om hur du läser den Azure Active Directory-etablering loggar finns [rapportering om automatisk konto användaretablering](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera enkel inloggning](active-directory-saas-pingboard-tutorial.md)
