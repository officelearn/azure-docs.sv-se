---
title: Skydds principer för appar med villkorlig åtkomst – Azure Active Directory
description: Lär dig hur du kräver app Protection-princip för Cloud app-åtkomst med villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c853ef3a5a40381aba4e1c13eaf9ad7d8653170
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186623"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Kräv app Protection-princip för Cloud app-åtkomst med villkorlig åtkomst (för hands version)

Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. När du ser till att dina anställda kan vara produktiva vill du även förhindra data förlust. Med Azure Active Directory (Azure AD) villkorlig åtkomst kan du skydda dina företags data genom att begränsa åtkomsten till dina molnappar. Använd klient program med en app Protection-princip först.

Den här artikeln beskriver hur du konfigurerar principer för villkorlig åtkomst som kan kräva en skydds princip för appar innan åtkomst beviljas till data.

## <a name="overview"></a>Översikt

Med [villkorlig åtkomst i Azure AD](overview.md)kan du finjustera hur behöriga användare kan komma åt dina resurser. Du kan till exempel begränsa åtkomsten till dina molnappar till betrodda enheter.

Du kan använda [Intune App Protection-principer](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data. Intune App Protection-principer kräver inte någon lösning för hantering av mobila enheter (MDM). Du kan skydda företagets data med eller utan att registrera enheter i en enhets hanterings lösning.

Azure Active Directory villkorlig åtkomst begränsar åtkomsten till dina molnappar till klient program som Intune har rapporterat till Azure AD som att ta emot en skydds princip för appar. Du kan till exempel begränsa åtkomsten till Exchange Online till Outlook-appen som har en princip för Intune App Protection.

I terminologin för villkorlig åtkomst är dessa klient program kända för principer som skyddas med en *skydds princip för appar*.  

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)

En lista över principbaserad klient program finns i [krav på skydds princip för appar](concept-conditional-access-grant.md).

Du kan kombinera program skydds baserade principer för villkorlig åtkomst med andra principer, till exempel [enhets principer för villkorlig åtkomst](require-managed-devices.md). På så sätt kan du ge flexibilitet i hur du skyddar data för både personliga och företags enheter.

> [!NOTE]
> Det går inte att använda program skydds principer för villkorlig åtkomst för B2B-användare eftersom den bjudande organisationen inte har någon insyn i B2B-användarens hem organisation.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Förmåner för app Protection-baserat krav för villkorlig åtkomst

I likhet med efterlevnad som rapporteras av Intune för iOS och Android för en hanterad enhet, rapporterar Intune nu till Azure AD om en app Protection-princip tillämpas. Villkorlig åtkomst kan använda den här principen som en åtkomst kontroll. Den här nya principen för villkorlig åtkomst, skydds principen för appar, ökar säkerheten. Det skyddar mot administrativa fel, till exempel:

- Användare som inte har en Intune-licens.
- Användare som inte kan ta emot en princip för Intune-App-skydd.
- Appar för Intune App Protection-principer som inte är konfigurerade att ta emot en princip.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du är bekant med:

- [Krav för skydds principen för appen](concept-conditional-access-grant.md).
- [Godkänt klient program krav](concept-conditional-access-grant.md).
- Grundläggande begrepp för [villkorlig åtkomst i Azure Active Directory](overview.md).
- Så här [konfigurerar du en princip för villkorlig åtkomst](app-based-mfa.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en app Protection-baserad princip för villkorlig åtkomst måste du:

- Ha en Enterprise Mobility + Security eller en Azure Active Directory Premium-prenumeration + Intune.
- Se till att användarna är licensierade för Enterprise Mobility + Security eller Azure AD + Intune.
- Kontrol lera att klient programmet har kon figurer ATS i Intune för att ta emot en skydds princip för appar.
- Se till att användarna har kon figurer ATS i Intune för att ta emot en Intune App Protection-princip.

## <a name="app-protection-based-policy-for-exchange-online"></a>App Protection-baserad princip för Exchange Online

Det här scenariot består av en app Protection-baserad princip för villkorlig åtkomst för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att en användare:

- Konfigurerar e-post med hjälp av ett internt e-postprogram på iOS eller Android för att ansluta till Exchange.
- Tar emot ett e-postmeddelande som anger att åtkomst endast är tillgänglig med hjälp av Outlook-appen.
- Laddar ned programmet med länken.
- Öppnar Outlook-programmet och loggar in med autentiseringsuppgifter för Azure AD.
- Uppmanas att installera antingen **Microsoft Authenticator-appen** eller **Intune-företagsportal** för att fortsätta.
- Installerar programmet och återgår till Outlook-appen för att fortsätta.
- Uppmanas att registrera en enhet.
- Kan ta emot en princip för Intune App Protection.
- Har åtkomst till e-post.

Alla Intune App Protection-principer måste finnas i programmet för att få åtkomst till företags data. Principerna kan uppmana användaren att starta om programmet eller använda en ytterligare PIN-kod. Detta är fallet om principerna har kon figurer ATS för programmet och plattformen.

### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/01.png)

1. Ange namnet på den villkorliga åtkomst principen.
1. Under **tilldelningar**i **användare och grupper väljer du**minst en användare eller grupp för varje princip för villkorlig åtkomst.
1. I **molnappar**väljer du **Office 365 Exchange Online**.

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

1. I **villkor**konfigurerar du **enhets plattformar** och **klient program (för hands version)** :
   1. I **enhets plattformar**väljer du **Android** och **iOS**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

   1. I **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **moderna autentiserade klienter**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

1. Under **åtkomst kontroller**väljer du **Kräv app Protection-princip (för hands version)** .

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync (EAS)**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Ange namnet på den villkorliga åtkomst principen.
1. Under **tilldelningar**i **användare och grupper väljer du**minst en användare eller grupp för varje princip för villkorlig åtkomst.
1. I **molnappar**väljer du **Office 365 Exchange Online**.

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

1. I **villkor**konfigurerar du **klient program (för hands version)** . 

   1. I **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

   1. Under **åtkomst kontroller**väljer du **Kräv app Protection-princip (för hands version)** .

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)

**Steg 3: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>App Protection-baserad eller kompatibel enhets princip för Exchange Online

Det här scenariot består av en app Protection-baserad eller kompatibel enhets princip för villkorlig åtkomst för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att:
 
- En användare har redan registrerats, med eller utan företags enheter.
- Användare som inte har registrerats och registrerats med Azure AD med hjälp av ett program som skyddas av appen behöver registrera en enhet för att få åtkomst till resurser.
- Registrerade användare som använder appens skyddade program behöver inte registrera enheten på nytt.
- Användaren kan ta emot en Intune App Protection-princip om den inte har registrerats.
- Användaren kan komma åt e-post med Outlook och en princip för Intune-appen om den inte har registrerats.
- Användaren har åtkomst till e-post med Outlook om enheten har registrerats.

### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/62.png)

1. Ange namnet på den villkorliga åtkomst principen.
1. Under **tilldelningar**i **användare och grupper väljer du**minst en användare eller grupp för varje princip för villkorlig åtkomst.
1. I **molnappar**väljer du **Office 365 Exchange Online**. 

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

1. I **villkor**konfigurerar du **enhets plattformar** och **klient program (för hands version)** . 
 
   1. I **enhets plattformar**väljer du **Android** och **iOS**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

   1. I **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **moderna autentiserade klienter**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Under **åtkomst kontroller**väljer du följande alternativ:
   - **Kräv att enheten ska markeras som kompatibel**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv en av de valda kontrollerna**   
 
      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/11.png)

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Ange namnet på den villkorliga åtkomst principen.
1. Under **tilldelningar**i **användare och grupper väljer du**minst en användare eller grupp för varje princip för villkorlig åtkomst.
1. I **molnappar**väljer du **Office 365 Exchange Online**. 

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

1. I **villkor**konfigurerar du **klient program (för hands version)** . 

   I **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

1. Under **åtkomst kontroller**väljer du följande alternativ:
   - **Kräv att enheten ska markeras som kompatibel**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv en av de valda kontrollerna**

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/11.png)

**Steg 3: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>App Protection-baserad och kompatibel enhets princip för Exchange Online

Det här scenariot består av en app-Protection-baserad och kompatibel enhets princip för villkorlig åtkomst för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att en användare:
 
- Konfigurerar e-post med hjälp av ett internt e-postprogram på iOS eller Android för att ansluta till Exchange.
- Tar emot ett e-postmeddelande som anger att enheten måste vara registrerad för åtkomst.
- Hämtar Intune-företagsportal och loggar in på portalen.
- Söker efter e-post och uppmanas att använda Outlook-appen.
- Hämtar Outlook-appen.
- Öppnar Outlook-appen och anger de autentiseringsuppgifter som används i registreringen.
- Kan ta emot en princip för Intune App Protection.
- Har åtkomst till e-post med Outlook och en princip för Intune App Protection.

Alla principer för Intune App Protection aktive ras innan åtkomst beviljas till företags data. Principerna kan uppmana användaren att starta om programmet eller använda en ytterligare PIN-kod. Detta är fallet om principerna har kon figurer ATS för programmet och plattformen.

### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/01.png)

1. Ange namnet på den villkorliga åtkomst principen.
1. Under **tilldelningar**i **användare och grupper väljer du**minst en användare eller grupp för varje princip för villkorlig åtkomst.
1. I **molnappar**väljer du **Office 365 Exchange Online**. 

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

1. I **villkor**konfigurerar du **enhets plattformar** och **klient program (för hands version)** . 
   1. I **enhets plattformar**väljer du **Android** och **iOS**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

   1. I **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **moderna autentiserade klienter**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

1. Under **åtkomst kontroller**väljer du följande alternativ:
   - **Kräv att enheten ska markeras som kompatibel**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv alla markerade kontroller**   
 
      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/13.png)

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Ange namnet på den villkorliga åtkomst principen.
1. Under **tilldelningar**i **användare och grupper väljer du**minst en användare eller grupp för varje princip för villkorlig åtkomst.
1. I **molnappar**väljer du **Office 365 Exchange Online**. 

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

1. I **villkor**konfigurerar du **klient program (för hands version)** . 

   I **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

1. Under **åtkomst kontroller**väljer du följande alternativ:
   - **Kräv att enheten ska markeras som kompatibel**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv alla markerade kontroller**   
 
      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/13.png)

**Steg 3: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>App Protection-baserad eller app-baserad princip för Exchange Online och SharePoint Online

Det här scenariot består av en app Protection-baserad eller godkänd Apps-princip för åtkomst till Exchange Online och SharePoint Online.

### <a name="scenario-playbook"></a>Scenario Spelbok

Det här scenariot förutsätter att en användare:

- Konfigurerar klient program som antingen finns i listan över appar som har stöd för skydds princip krav för appar eller krav för godkända appar.  
- Använder klient program som uppfyller kraven för skydds principen för appar och kan ta emot en princip för Intune App Protection.
- Använder klient program som uppfyller de godkända apparnas princip krav som stöder Intune App Protection-princip.
- Öppnar programmet för att få åtkomst till e-post eller dokument.
- Öppnar Outlook-programmet och loggar in med autentiseringsuppgifter för Azure AD.
- Uppmanas att installera antingen Microsoft Authenticator för användning av iOS eller Intune-företagsportal för Android om de inte redan är installerade.
- Installerar programmet och kan gå tillbaka till Outlook-appen för att fortsätta.
- Uppmanas att registrera en enhet.
- Kan ta emot en princip för Intune App Protection.
- Har åtkomst till e-post med Outlook och en princip för Intune App Protection.
- Kan komma åt webbplatser och dokument med en app som inte är i princip kravet för program skydd men som anges i kravet för godkända appar.

Alla Intune App Protection-principer krävs innan åtkomst beviljas till företags data. Principerna kan uppmana användaren att starta om programmet eller använda en ytterligare PIN-kod. Detta är fallet om principerna har kon figurer ATS för programmet och plattformen.

**Kommentarer**

- Du kan använda det här scenariot om du vill ha stöd för både app Protection-baserade och app-baserade principer för villkorlig åtkomst.
- I den här *eller* -principen utvärderas appar med ett krav för en skydds princip för åtkomst först innan kraven för godkända klient appar.

### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en princip för villkorlig åtkomst för Azure AD för Exchange Online och SharePoint Online**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/62.png)

1. Ange namnet på den villkorliga åtkomst principen.
1. Under **tilldelningar**i **användare och grupper väljer du**minst en användare eller grupp för varje princip för villkorlig åtkomst.
1. Välj **Office 365 Exchange Online** och **Office 365 SharePoint Online**i **molnappar**. 

   ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/02.png)

1. I **villkor**konfigurerar du **enhets plattformar** och **klient program (för hands version)** . 
   1. I **enhets plattformar**väljer du **Android** och **iOS**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

   1. I **klient program (för hands version)** väljer du **mobilappar och skriv bords klienter** och **moderna autentiserade klienter**.

      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

1. Under **åtkomst kontroller**väljer du följande alternativ:
   - **Kräv godkänd klient app**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv en av de valda kontrollerna**
 
      ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/12.png)

**Steg 2: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [Skydda appar och data med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md).
