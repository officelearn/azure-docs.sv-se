---
title: Så här kräver du godkända klient program för Cloud app-åtkomst med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Lär dig hur du kräver godkända klient program för Cloud app-åtkomst med villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45eeef7d96f194e224e5b44421e73eb5ee5d9c0d
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515139"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Instruktioner: Kräv godkända klient program för Cloud app-åtkomst med villkorlig åtkomst 

Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. När du ser till att dina anställda kan vara produktiva vill du även förhindra data förlust. Med Azure Active Directory (Azure AD) villkorlig åtkomst kan du begränsa åtkomsten till dina molnappar till godkända klient program som kan skydda företagets data.  

I det här avsnittet beskrivs hur du konfigurerar villkor för åtkomst principer som kräver godkända klient program.

## <a name="overview"></a>Översikt

Med [villkorlig åtkomst i Azure AD](overview.md)kan du finjustera hur behöriga användare kan komma åt dina resurser. Du kan till exempel begränsa åtkomsten till dina molnappar till betrodda enheter.

Du kan använda [Intune App Protection-principer](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data. Intune App Protection-principer kräver inte hanterings lösning för mobila enheter (MDM), vilket gör att du kan skydda företagets data med eller utan att registrera enheter i en enhets hanterings lösning.

Med Azure Active Directory villkorlig åtkomst kan du begränsa åtkomsten till dina molnappar till klient program som stöder Intune App Protection-principer. Du kan till exempel begränsa åtkomsten till Exchange Online till Outlook-appen.

I terminologin för villkorlig åtkomst kallas dessa klient appar **godkända klient program**.  

![Villkorad åtkomst](./media/app-based-conditional-access/05.png)

En lista över godkända klient program finns i [godkända klient program krav](technical-reference.md#approved-client-app-requirement).

Du kan kombinera app-baserade villkorliga åtkomst principer med andra principer, till exempel enhets [principer för villkorlig åtkomst](require-managed-devices.md) för att ge flexibilitet i hur du skyddar data för både personliga och företags enheter.

## <a name="before-you-begin"></a>Innan du börjar

Det här avsnittet förutsätter att du är bekant med:

- Den tekniska referensen för [godkända klient program krav](technical-reference.md#approved-client-app-requirement) .
- Grundläggande begrepp för [villkorlig åtkomst i Azure Active Directory](overview.md).
- Så här [konfigurerar du en princip för villkorlig åtkomst](app-based-mfa.md).
- [Migreringen av principer för villkorlig åtkomst](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en app-baserad villkorlig åtkomst princip måste du ha en Enterprise Mobility + Security eller en Azure Active Directory Premium-prenumeration och användarna måste ha licens för EMS eller Azure AD. 

## <a name="exchange-online-policy"></a>Exchange Online-princip 

Det här scenariot består av en app-baserad villkorlig åtkomst princip för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att en användare:

- Konfigurerar e-post med hjälp av ett internt e-postprogram på iOS eller Android för att ansluta till Exchange
- Tar emot ett e-postmeddelande som anger att åtkomst endast är tillgänglig med Outlook-appen
- Laddar ned programmet med länken
- Öppnar Outlook-programmet och loggar in med autentiseringsuppgifter för Azure AD
- Uppmanas att installera antingen Authenticator (iOS) eller Företagsportal (Android) för att fortsätta
- Installerar programmet och kan gå tillbaka till Outlook-appen för att fortsätta
- Uppmanas att registrera en enhet
- Kan komma åt e-post

Alla Intunes skydds principer aktive ras vid tiden för åtkomst till företags data och användaren uppmanas att starta om programmet, använda ytterligare en PIN-kod osv. (om det har kon figurer ATS för programmet och plattformen).

### <a name="configuration"></a>Konfiguration 

**Steg 1 – Konfigurera en princip för villkorlig åtkomst för Azure AD för Exchange Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online**.
1. **Kraven** Som **villkor**måste du konfigurera enhets **plattformar** och **klient program**:
   1. Som **enhets plattformar**väljer du **Android** och **iOS**.
   1. Som **klient program (för hands version)** väljer du **mobilappar och skrivbordsappar** och **moderna autentiserade klienter**.
1. Som **åtkomst kontroller**måste du ha valt **Kräv godkänd klient app (för hands version)** .

   ![Villkorad åtkomst](./media/app-based-conditional-access/05.png)

**Steg 2 – Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online**.
1. **Kraven** Som **villkor**måste du konfigurera **klient program (för hands version)** . 
   1. Som **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.
   1. Som **åtkomst kontroller**måste du ha valt **Kräv godkänd klient app (för hands version)** .

      ![Villkorad åtkomst](./media/app-based-conditional-access/05.png)

**Steg 3 – Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online-och SharePoint Online-princip

Det här scenariot består av en villkorlig åtkomst med hanterings principer för mobilappar för åtkomst till Exchange Online och SharePoint Online med godkända appar.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att en användare:

- Försöker använda SharePoint-appen för att ansluta och även visa sina företags webbplatser
- Försöker logga in med samma autentiseringsuppgifter som Outlook-appens autentiseringsuppgifter
- Behöver inte registrera igen och få till gång till resurserna

### <a name="configuration"></a>Konfiguration

**Steg 1 – Konfigurera en princip för villkorlig åtkomst för Azure AD för Exchange Online och SharePoint Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online** och **Office 365 SharePoint Online**. 
1. **Kraven** Som **villkor**måste du konfigurera enhets **plattformar** och **klient program**:
   1. Som **enhets plattformar**väljer du **Android** och **iOS**.
   1. Som **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **moderna autentiserade klienter**.
1. Som **åtkomst kontroller**måste du ha valt **Kräv godkänd klient app (för hands version)** .

   ![Villkorad åtkomst](./media/app-based-conditional-access/05.png)

**Steg 2 – Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online**. Online 
1. **Kraven** Som **villkor**måste du konfigurera- **klient program**:
   1. Som **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.
   1. Som **åtkomst kontroller**måste du ha valt **Kräv godkänd klient app (för hands version)** .

      ![Villkorad åtkomst](./media/app-based-conditional-access/05.png)

**Steg 3 – Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

![Villkorad åtkomst](./media/app-based-conditional-access/09.png)

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>App-baserad eller kompatibel enhets princip för Exchange Online och SharePoint Online

Det här scenariot består av en app-baserad eller kompatibel enhets princip för villkorlig åtkomst för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att:
 
- Vissa användare har redan registrerats (med eller utan företags enheter)
- Användare som inte har registrerats och registrerats med Azure AD med hjälp av ett app-skyddat program behöver registrera en enhet för att få åtkomst till resurser
- Registrerade användare som använder appens skyddade program behöver inte registrera enheten på nytt

### <a name="configuration"></a>Konfiguration

**Steg 1 – Konfigurera en princip för villkorlig åtkomst för Azure AD för Exchange Online och SharePoint Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online** och **Office 365 SharePoint Online**. 
1. **Kraven** Som **villkor**måste du konfigurera enhets **plattformar** och **klient program**. 
   1. Som **enhets plattformar**väljer du **Android** och **iOS**.
   1. Som **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **moderna autentiserade klienter**.
1. Som **åtkomst kontroller**måste du ha följande valt:
   - **Kräv att enheten ska markeras som kompatibel**
   - **Kräv godkänd klient app (för hands version)**
   - **Kräv en av de valda kontrollerna**   
 
      ![Villkorad åtkomst](./media/app-based-conditional-access/11.png)

**Steg 2 – Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online**. 
1. **Kraven** Som **villkor**måste du konfigurera- **klient program**. 
   1. Som **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.
1. Som **åtkomst kontroller**måste du ha valt **Kräv godkänd klient app (för hands version)** .
 
   ![Villkorad åtkomst](./media/app-based-conditional-access/11.png)

**Steg 3 – Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

![Villkorad åtkomst](./media/app-based-conditional-access/09.png)

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>App-baserad och kompatibel enhets princip för Exchange Online och SharePoint Online

Det här scenariot består av en app-baserad och kompatibel enhets princip för villkorlig åtkomst för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att en användare:
 
- Konfigurerar e-post med hjälp av ett internt e-postprogram på iOS eller Android för att ansluta till Exchange
- Tar emot ett e-postmeddelande som anger att enheten måste registreras
- Laddar ned företags portalen och loggar in på företags portalen
- Söker efter e-post och uppmanas att använda Outlook-appen
- Hämtar Outlook-appen
- Öppnar Outlook-appen och anger de autentiseringsuppgifter som används i registreringen
- Användaren kan komma åt e-post

Alla principer för Intune App Protection aktive ras vid tidpunkten för åtkomst till företags data och användaren uppmanas att starta om programmet, använda ytterligare en PIN-kod osv. (om det har kon figurer ATS för programmet och plattformen)

### <a name="configuration"></a>Konfiguration

**Steg 1 – Konfigurera en princip för villkorlig åtkomst för Azure AD för Exchange Online och SharePoint Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online** och **Office 365 SharePoint Online**. 
1. **Kraven** Som **villkor**måste du konfigurera enhets **plattformar** och **klient program**. 
   1. Som **enhets plattformar**väljer du **Android** och **iOS**.
   1. Som **klient program (för hands version)** väljer du **mobilappar och skrivbordsappar** och **moderna autentiserade klienter**.
1. Som **åtkomst kontroller**måste du ha följande valt:
   - **Kräv att enheten ska markeras som kompatibel**
   - **Kräv godkänd klient app (för hands version)**
   - **Kräv alla markerade kontroller**   
 
      ![Villkorad åtkomst](./media/app-based-conditional-access/13.png)

**Steg 2 – Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

1. **Namnet** på den villkorliga åtkomst principen.
1. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en vald användare eller grupp.
1. **Molnappar:** Som molnappar måste du välja **Office 365 Exchange Online**. 
1. **Kraven** Som **villkor**måste du konfigurera **klient program (för hands version)** . 
   1. Som **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.

   ![Villkorad åtkomst](./media/app-based-conditional-access/92.png)

1. Som **åtkomst kontroller**måste du ha följande valt:
   - **Kräv att enheten ska markeras som kompatibel**
   - **Kräv godkänd klient app (för hands version)**
   - **Kräv alla markerade kontroller**   

**Steg 3 – Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

![Villkorad åtkomst](./media/app-based-conditional-access/09.png)

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).

Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md). 
