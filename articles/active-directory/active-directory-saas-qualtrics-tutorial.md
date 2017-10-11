---
title: "Självstudier: Azure Active Directory-integrering med Qualtrics | Microsoft Docs"
description: "Lär dig hur du använder Qualtrics med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2fcde595a40dafda7549f5bccb582b57585b314e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Självstudier: Azure Active Directory-integrering med Qualtrics
Syftet med den här kursen är att visa integreringen av Azure och Qualtrics.  

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En Qualtrics enkel inloggning (SSO) aktiverat prenumeration

Den här kursen Azure AD-användare som du har tilldelat Qualtrics kommer att kunna enkel inloggning till programmet på din Qualtrics företagets plats (service provider initierade inloggning) eller med hjälp av den [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

Det scenario som beskrivs i den här kursen består av följande byggblock:

1. Aktivera programintegrationstyp för Qualtrics
2. Konfigurera enkel inloggning (SSO)
3. Konfigurera användaretablering
4. Tilldela användare

![Scenariot](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scenario")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Aktivera programintegrationstyp för Qualtrics
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för Qualtrics.

**Utför följande steg om du vill aktivera programmet-integrering för Qualtrics:**

1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
   ![Program](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "program")
4. Klicka på **Lägg till** längst ned på sidan.
   
   ![Lägg till program](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "lägga till program")
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
   ![Lägga till ett program från gallerry](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "lägga till ett program från gallerry")
6. I den **sökrutan**, typen **Qualtrics**.
   
   ![Programgalleriet](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Programgalleriet")
7. I resultatfönstret, Välj **Qualtrics**, och klicka sedan på **Slutför** lägga till programmet.
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Syftet med det här avsnittet är att beskriva hur användarna att autentisera till Qualtrics med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.

**Utför följande steg för att konfigurera enkel inloggning:**

1. I den klassiska Azure-portalen på den **Qualtrics** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Konfigurera enkel inloggning")
2. På den **hur vill du att användarna kan logga in på Qualtrics** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Konfigurera enkel inloggning")
3. På den **konfigurera App-URL** sidan den **Qualtrics logga URL** textruta Skriv URL: en (t.ex. ”:*https://ssotest2ut1.qualtrics.com*”), och klicka sedan på **nästa**.
   
   ![Konfigurera App-URL](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "konfigurera App-URL")
4. På den **Konfigurera enkel inloggning på Qualtrics** klickar du på **hämtar metadata**, och spara sedan metadatafilen på datorn.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Konfigurera enkel inloggning")
5. Skicka metadatafilen till Qualtrics supportteam.
   
   >[!NOTE]
   >SSO-konfigurationen har kan utföras av supportteamet Qualtrics. Du får ett meddelande så snart som konfigurationen har slutförts.
   > 
   > 
6. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Konfigurera enkel inloggning")
   
## <a name="configure-user-provisioning"></a>Konfigurera användaretablering

Det finns inga uppgiften som du kan konfigurera användaretablering till Qualtrics. När en tilldelad användare försöker logga in med hjälp av åtkomstpanelen Qualtrics kontrollerar Qualtrics om användaren finns.  

Om det finns inget användarkonto ännu, skapas den automatiskt av Qualtrics.

## <a name="assign-users"></a>Tilldela användare
Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

**Utför följande steg om du vill tilldela Qualtrics användare:**

1. Skapa ett testkonto i den klassiska Azure-portalen.
2. På den **Qualtrics** integreringssidan för programmet, klickar du på **tilldela användare**.
   
   ![Tilldela användare](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "tilldela användare")
3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
   ![Ja](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Ja")

Om du vill testa inställningarna för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

