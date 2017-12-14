---
title: "Azure Active Directory app-baserad villkorlig åtkomst | Microsoft Docs"
description: "Lär dig hur Azure Active Directory app-baserad villkorlig åtkomst fungerar."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: aaf2da57d8653371ab0b46e47474442aa4be1d65
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Azure Active Directory app-baserad villkorlig åtkomst  

Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. Medan att dina anställda kan vara produktiva vill du även förhindra förlust av data. Med Azure Active Directory (AD Azure) app-baserad villkorlig åtkomst, kan du begränsa åtkomsten till dina molnappar för klientappar som kan skydda företagets data.  

Det här avsnittet beskriver hur du konfigurerar Azure AD app-baserad villkorlig åtkomst.

## <a name="overview"></a>Översikt

Med [villkorlig åtkomst i Azure AD](active-directory-conditional-access-azure-portal.md), du kan finjustera hur behöriga användare kan komma åt dina resurser. Du kan exempelvis begränsa åtkomsten till dina molnappar till betrodda enheter.

Du kan använda [skyddsprinciper för Intune app](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data. Intune app protection-principer behöver lösning för hantering av mobila enheter (MDM), där du kan skydda företagets data med eller utan att registrera enheter i en enhetshanteringslösning.

Azure Active Directory app-baserad villkorlig åtkomst kan du begränsa åtkomsten till dina molnappar för klientappar som stöder skyddsprinciper för Intune app. Du kan exempelvis begränsa åtkomsten till Exchange Online för Outlook-appen.

I terminologin villkorlig åtkomst apparna klienten kallas **godkända klientprogram**.  


![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/05.png)


En lista över godkända klientprogram, se [godkända kravet på klienten app](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


Du kan kombinera principer för app-baserad villkorlig åtkomst med andra principer som [enhetsbaserad villkorliga åtkomstprinciper](active-directory-conditional-access-policy-connected-applications.md) till ger flexibilitet i hur du skyddar data för både personliga och företagets enheter.

 


##<a name="before-you-begin"></a>Innan du börjar

Det här avsnittet förutsätter att du är bekant med:

- Den [godkända kravet på klienten app](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) Teknisk referens.


- Grundläggande begrepp för [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- Så här [konfigurera en princip för villkorlig åtkomst](active-directory-conditional-access-azure-portal-get-started.md).

- Den [migrering av principer för villkorlig åtkomst](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Krav

Om du vill skapa en princip för app-baserad villkorlig åtkomst, du måste ha ett Enterprise Mobility + Security eller ett Azure Active Directory premium-prenumeration och användarna måste ha licens för EMS eller Azure AD. 


## <a name="exchange-online-policy"></a>Exchange Online-princip 

Det här scenariot består av en princip för app-baserad villkorlig åtkomst för åtkomst till Exchange Online.


### <a name="scenario-playbook"></a>Scenariot playbook

Det här scenariot förutsätter att en användare:

- Konfigurerar e-post med en intern e-postprogrammet på iOS- eller Android för att ansluta till Exchange

- Tar emot ett e-postmeddelande som anger att åtkomst endast är tillgängliga med hjälp av Outlook-appen

- Hämtar programmet med länken

- Outlook-programmet öppnas och loggar in med autentiseringsuppgifter för Azure AD

- Uppmanas att installera autentiseraren (iOS) eller företagsportal (Android) för att fortsätta

- Installerar programmet och kan gå tillbaka till Outlook-appen för att fortsätta

- Uppmanas att registrera en enhet

- Komma åt e-post

Alla principer för Intune app skydd är aktiverat samtidigt åtkomst till företagets data och kan uppmana användaren att starta om programmet använder ett ytterligare PIN-kod osv (om konfigurerad för programmet och plattform).

### <a name="configuration"></a>Konfiguration 

**Steg 1 – konfigurera en Azure AD-principen för villkorlig åtkomst för Exchange Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/01.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.

3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/07.png)

4. **Villkor:** som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**:

    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/03.png)

    b. Som **klientappar**väljer **mobilappar och skrivbordsappar**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/04.png)

5. Som **åtkomstkontroller**, måste du ha **kräver godkända klientappen (förhandsgranskning)** valda.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/05.png)
 

**Steg 2 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/06.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.


3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/07.png)

4. **Villkor:** som **villkor**, måste du konfigurera **klientappar**. 

    a. Som **klientappar**väljer **Exchange Active Sync**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/08.png)

    b. Som **åtkomstkontroller**, måste du ha **kräver godkända klientappen (förhandsgranskning)** valda.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/05.png)


**Steg 3 – Konfigurera Intune app protection-principen för iOS och Android klientprogram**


![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online och SharePoint Online-princip

Det här scenariot består av en villkorlig åtkomst med mam-princip för åtkomst till Exchange Online och SharePoint Online med godkända appar.

### <a name="scenario-playbook"></a>Scenariot playbook

Det här scenariot förutsätter att en användare:

- Försöker använda SharePoint-appen att ansluta och visa företagets webbplatser

- Försök att logga in med samma autentiseringsuppgifter som autentiseringsuppgifter för Outlook-appen

- Behöver inte registrera och kan få åtkomst till resurser


### <a name="configuration"></a>Konfiguration

**Steg 1 – konfigurera en Azure AD-principen för villkorlig åtkomst för Exchange Online och SharePoint Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/71.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.


3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online** och **Office 365 SharePoint Online**. 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/02.png)

4. **Villkor:** som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**:

    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/03.png)

    b. Som **klientappar**väljer **mobilappar och skrivbordsappar**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/04.png)

5. Som **åtkomstkontroller**, måste du ha **kräver godkända klientappen (förhandsgranskning)** valda.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/05.png)




**Steg 2 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/06.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.

3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online**. Online 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/07.png)

4. **Villkor:** som **villkor**, måste du konfigurera **klientappar**:

    a. Som **klientappar**väljer **Exchange Active Sync**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/08.png)

    b. Som **åtkomstkontroller**, måste du ha **kräver godkända klientappen (förhandsgranskning)** valda.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/05.png)




**Steg 3 – Konfigurera Intune app protection-principen för iOS och Android klientprogram**


![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>App- eller kompatibel enhetsprincip för Exchange Online och SharePoint Online

Det här scenariot består av en princip för villkorlig åtkomst för app- eller kompatibla enheter för åtkomst till Exchange Online.


### <a name="scenario-playbook"></a>Scenariot playbook

Det här scenariot förutsätter att:
 
- Vissa användare har redan registrerats (med eller utan företagets enheter)

- Användare som inte är registrerad och registrerad med Azure AD med hjälp av en app skyddade program behöver registrera en enhet för att komma åt resurser

- Registrerade användare med app-skyddade program behöver inte registrera enheten


### <a name="configuration"></a>Konfiguration

**Steg 1 – konfigurera en Azure AD-principen för villkorlig åtkomst för Exchange Online och SharePoint Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/62.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.

3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online** och **Office 365 SharePoint Online**. 

     ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/02.png)

4. **Villkor:** som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**. 
 
    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/03.png)

    b. Som **klientappar**väljer **mobilappar och skrivbordsappar**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/04.png)

5. Som **åtkomstkontroller**, måste du ha följande valt:

    - **Kräv att enheten har markerats som kompatibel**

    - **Kräv godkända klientappen (förhandsgranskning)**

    - **Kräv en av de markerade kontrollerna**   
 
    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/11.png)



**Steg 2 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/61.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.

3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online**. 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/07.png)

4. **Villkor:** som **villkor**, måste du konfigurera **klientappar**. 

    Som **klientappar*väljer **Exchange Active Sync**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/08.png)

5. Som **åtkomstkontroller**, måste du ha **kräver godkända klientappen (förhandsgranskning)** valda.
 
    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/11.png)




**Steg 3 – Konfigurera Intune app protection-principen för iOS och Android klientprogram**


![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>App- och kompatibla enhetsprincip för Exchange Online och SharePoint Online

Det här scenariot består av en princip för villkorlig åtkomst för app- och kompatibla enheter för åtkomst till Exchange Online.


### <a name="scenario-playbook"></a>Scenariot playbook

Det här scenariot förutsätter att en användare:
 
-   Konfigurerar e-post med en intern e-postprogrammet på iOS- eller Android för att ansluta till Exchange
-   Tar emot ett e-postmeddelande som anger att åtkomst kräver att enheten registreras
-   Hämtar Företagsportalen och loggar in på Företagsportalen
-   Kontrollerar e-post och uppmanas att använda Outlook-appen
-   Hämtar Outlook-appen
-   Öppnar Outlook-appen och anger autentiseringsuppgifter som används i registreringen
-   Användare har tillgång till e-post

Policyer för Intune app skydd aktiveras vid tidpunkten för åtkomst till företagets data och kan uppmana användaren att starta om programmet använder ett ytterligare PIN-kod osv (om konfigurerad för programmet och plattform)


### <a name="configuration"></a>Konfiguration

**Steg 1 – konfigurera en Azure AD-principen för villkorlig åtkomst för Exchange Online och SharePoint Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/62.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.

3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online** och **Office 365 SharePoint Online**. 

     ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/02.png)

4. **Villkor:** som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**. 
 
    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/03.png)

    b. Som **klientappar**väljer **mobilappar och skrivbordsappar**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/04.png)

5. Som **åtkomstkontroller**, måste du ha följande valt:

    - **Kräv att enheten har markerats som kompatibel**

    - **Kräv godkända klientappen (förhandsgranskning)**

    - **Kräv en av de markerade kontrollerna**   
 
    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/11.png)



**Steg 2 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/61.png)

1. Den **namn** av princip för villkorlig åtkomst.

2. **Användare och grupper**: varje princip för villkorlig åtkomst måste ha minst en användare eller grupp som har valts.

3. **Molnappar:** som molnappar, måste du markera **Office 365 Exchange Online**. 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/07.png)

4. **Villkor:** som **villkor**, måste du konfigurera **klientappar**. 

    Som **klientappar**väljer **Exchange Active Sync**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/08.png)

5. Som **åtkomstkontroller**, måste du ha följande valt:

    - **Kräv att enheten har markerats som kompatibel**

    - **Kräv godkända klientappen (förhandsgranskning)**

    - **Kräv de markerade kontrollerna**   
 
    ![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/64.png)




**Steg 3 – Konfigurera Intune app protection-principen för iOS och Android klientprogram**


![Villkorlig åtkomst](./media/active-directory-conditional-access-mam/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.






## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
