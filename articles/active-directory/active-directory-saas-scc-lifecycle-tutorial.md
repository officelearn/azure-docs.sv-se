---
title: "Självstudier: Azure Active Directory-integrering med SCC livscykel | Microsoft Docs"
description: "Lär dig hur du använder SCC livscykel med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Självstudier: Azure Active Directory-integrering med SCC livscykel
Syftet med den här kursen är att visa integreringen av Azure och SCC livscykel.  

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En SCC livscykel enkel inloggning (SSO) aktiverat prenumeration

Den här kursen Azure AD-användare som du har tilldelat SCC livscykel kommer att kunna enkel inloggning till programmet på din SCC livscykel företagets plats (service provider initierade inloggning) eller med hjälp av den [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

Det scenario som beskrivs i den här kursen består av följande byggblock:

1. Aktivera programintegrationstyp för SCC livscykel
2. Konfigurera enkel inloggning (SSO)
3. Konfigurera användaretablering
4. Tilldela användare

![Scenario](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenario")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Aktivera application-integration för SCC livscykel
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för SCC livscykel.

**Om du vill aktivera programmet-integrering för SCC livscykel, utför du följande steg:**

1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "program")
4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Lägg till program](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "lägga till program")
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Lägga till ett program från gallerry](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "lägga till ett program från gallerry")
6. I den **sökrutan**, typen **SCC livscykel**.
   
    ![Programgalleriet](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Programgalleriet")
7. I resultatfönstret, Välj **SCC livscykel**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![SCC livscykel](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC livscykel")
   
## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Syftet med det här avsnittet är att beskriva hur användarna att autentisera till SCC livscykel med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.

**Utför följande steg för att konfigurera enkel inloggning:**

1. I den klassiska Azure-portalen på den **SCC livscykel** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den ** Konfigurera enkel inloggning ** dialogrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Konfigurera enkel inloggning")
2. På den **hur vill du att användarna kan logga in på SCC livscykel** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Konfigurera enkel inloggning")
3. På den **konfigurera App-URL** sidan den **logga URL** textruta, Skriv URL-Adressen används av dina användare logga in i tillämpningsprogrammet SCC livscykel med hjälp av följande mönster ” *https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx* ”, och klicka sedan på **nästa**.
   
    ![Konfigurera App-URL](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "konfigurera App-URL")
4. På den **Konfigurera enkel inloggning på SCC livscykel** klickar du på **hämtar metadata**, och sedan spara metadatafilen lokalt på datorn.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Konfigurera enkel inloggning")
5. Vidarebefordra den metadatafilen till SCC livscykel supportteam.
   
   >[!NOTE]
   >Enkel inloggning måste vara aktiverat av supportteamet SCC livscykel.
   > 
   > 

6. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Konfigurera enkel inloggning")
   
## <a name="configure-user-provisioning"></a>Konfigurera användaretablering

För att aktivera Azure AD-användare att logga in på SCC livscykel, måste de etableras i SCC livscykel. Det finns inga uppgiften som du kan konfigurera användaretablering till SCC livscykel.

När en tilldelad användare försöker logga in på SCC livscykel, skapas automatiskt ett SCC livscykel-konto om det behövs.

>[!NOTE]
>Du kan använda något annat SCC livscykel användarens konto skapas verktyg eller API: er som tillhandahålls av SCC livscykel etablera AAD-användarkonton.
> 
> 

## <a name="assign-users"></a>Tilldela användare
Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

**Om du vill tilldela användare till SCC livscykel, utför du följande steg:**

1. Skapa ett testkonto i den klassiska Azure-portalen.
2. På den ** SCC livscykel ** integreringssidan för programmet, klickar du på **tilldela användare**.
   
    ![Tilldela användare](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "tilldela användare")
3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
    ![Ja](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Ja")

Om du vill testa inställningarna för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

