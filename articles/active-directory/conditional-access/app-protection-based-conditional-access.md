---
title: Kräv princip för cloud app-åtkomst med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Lär dig mer om att kräva appens skyddsprincip för åtkomst till molnet appen med villkorlig åtkomst i Azure Active Directory.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496938"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Kräv appskyddsprincip för cloud app-åtkomst med villkorlig åtkomst (förhandsversion)

Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. Att se till att dina anställda kan vara produktiva, vill du också förhindra förlust av data. Du kan använda villkorlig åtkomst för Azure Active Directory (AD Azure), för att skydda företagets data genom att begränsa åtkomsten till dina molnappar. Använd först klientappar med en appskyddsprincip.

Den här artikeln förklarar hur du konfigurerar principer för villkorlig åtkomst som kan kräva en appskyddsprincip innan åtkomst beviljas till data.

## <a name="overview"></a>Översikt

Med [Azure AD villkorsstyrd åtkomst](overview.md), du kan finjustera hur behöriga användare kan komma åt dina resurser. Du kan exempelvis begränsa åtkomsten till dina molnappar till betrodda enheter.

Du kan använda [Intunes appskyddsprinciper](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data. Intunes appskyddsprinciper kräver inte en lösning för hantering av mobila enheter. Du kan skydda företagets data med eller utan registrering av enheter i en enhetshanteringslösning.

Villkorlig åtkomst i Azure Active Directory begränsar åtkomsten till dina appar i molnet för klientprogram som Intune har rapporterats till Azure AD som tar emot en appskyddsprincip. Du kan exempelvis begränsa åtkomsten till Exchange Online till Outlook-appen som har en Intune-appskyddsprincip.

Med villkorlig åtkomst-terminologin dessa klientappar har visat sig vara princip som skyddas med ett *appskyddsprincip*.  

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)

En lista över princip-skyddad klient apps finns i [kravet för App protection](technical-reference.md#approved-client-app-requirement).

Du kan kombinera app-protection-principer för villkorlig åtkomst med andra principer, till exempel [principer för enhetsbaserad villkorlig åtkomst](require-managed-devices.md). På så sätt kan du ger flexibilitet i hur du skyddar data för både personliga och företagsägda enheter.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Fördelarna med app protection-baserad villkorlig åtkomst krav

Liknar efterlevnad som rapporteras av Intune för iOS och Android för en hanterad enhet Intune nu rapporter till Azure AD om en appskyddsprincip tillämpas. Villkorlig åtkomst kan använda den här principen som en åtkomstkontroll. Den här nya princip för villkorlig åtkomst, appskyddsprincipen, ökar säkerheten. Det skyddar mot admin fel, till exempel:

- Användare som inte har en Intune-licens.
- Användare som inte kan ta emot en Intune-appskyddsprincip.
- Intune app protection policy appar som inte är konfigurerade för att ta emot en princip.


## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du är bekant med:

- Den [kravet för app protection](technical-reference.md#app-protection-policy-requirement) Teknisk referens.
- Den [godkända kravet på klienten app](technical-reference.md#approved-client-app-requirement) Teknisk referens.
- Grundläggande begrepp för [villkorlig åtkomst i Azure Active Directory](overview.md).
- Så här [konfigurerar principer för villkorlig åtkomst](app-based-mfa.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill skapa en princip för skydd-baserad villkorlig åtkomst måste du:

- Har en Enterprise Mobility + Security eller Azure Active Directory premium-prenumeration + Intune.
- Se till att användarna är licensierade för Enterprise Mobility + Security eller Azure AD och Intune.
- Kontrollera att klientappen är konfigurerat i Intune för att ta emot en appskyddsprincip.
- Kontrollera att användarna som har konfigurerats i Intune för att ta emot en Intune-appskyddsprincip.

## <a name="app-protection-based-policy-for-exchange-online"></a>App protection-baserad princip för Exchange Online

Det här scenariot består av en app protection-baserad villkorlig åtkomstprincip för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att en användare:

- Konfigurerar e-post med hjälp av en intern e-postprogrammet på iOS eller Android för att ansluta till Exchange.
- Tar emot ett e-postmeddelande som indikerar att access är tillgängliga med Outlook-appen.
- Hämtar programmet med länken.
- Öppnas Outlook-programmet och loggar in med autentiseringsuppgifter för Azure AD.
- Uppmanas att installera antingen Microsoft Authenticator för iOS eller Intunes företagsportal för Android använder att fortsätta.
- Installerar programmet och returnerar Outlook-appen för att fortsätta.
- Uppmanas att registrera en enhet.
- Kan ta emot en Intune-appskyddsprincip.
- Kan komma åt e-post.

Alla Intunes appskyddsprinciper måste finnas i programmet för att komma åt företagets data. Principerna kan uppmana användaren att starta om programmet eller använda en ytterligare PIN-kod. Detta är fallet om principerna som har konfigurerats för programmet och plattform.

### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För den villkorliga åtkomstprincipen i det här steget konfigurerar du följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/01.png)

1. Ange namnet på din princip för villkorlig åtkomst.

2. Under **tilldelningar**i **användare och grupper**väljer minst en användare eller grupp för varje princip för villkorlig åtkomst.

3. I **Molnappar**väljer **Office 365 Exchange Online**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. I **villkor**, konfigurera **enhetsplattformar** och **klientappar (förhandsgranskning)**:

    a. I **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. I **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Under **åtkomstkontroller**väljer **kräver appskyddsprincip (förhandsversion)**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)
 

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync (EAS)**

För den villkorliga åtkomstprincipen i det här steget konfigurerar du följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Ange namnet på din princip för villkorlig åtkomst.

2. Under **tilldelningar**i **användare och grupper**väljer minst en användare eller grupp för varje princip för villkorlig åtkomst.


3. I **Molnappar**väljer **Office 365 Exchange Online**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. I **villkor**, konfigurera **klientappar (förhandsgranskning)**. 

    a. I **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

    b. Under **åtkomstkontroller**väljer **kräver appskyddsprincip (förhandsversion)**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)


**Steg 3: Konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>App protection-baserade eller kompatibel enhetsprincip för Exchange Online

Det här scenariot består av en app protection-baserade eller kompatibel enhetsprincip för villkorlig åtkomst för åtkomst till Exchange Online.


### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att:
 
- En användare har redan registrerats med eller utan företagets enheter.
- Användare som inte har registrerats och registrerats med Azure AD med hjälp av en app skyddade program behöver registrera en enhet för att få åtkomst till resurser.
- Registrerade användare som använder programmet app som skyddas inte registrera enheten igen.
- Användaren kan ta emot en Intune-appskyddsprincip om inte registrerats.
- Användaren kan komma åt e-postmeddelande med Outlook och en Intune-appskyddsprincip om inte registrerats.
- Användaren har åtkomst till e-postmeddelande med Outlook om enheten har registrerats.


### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För den villkorliga åtkomstprincipen i det här steget konfigurerar du följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/62.png)

1. Ange namnet på din princip för villkorlig åtkomst.

2. Under **tilldelningar**i **användare och grupper**väljer minst en användare eller grupp för varje princip för villkorlig åtkomst.

3. I **Molnappar**väljer **Office 365 Exchange Online**. 

     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. I **villkor**, konfigurera **enhetsplattformar** och **klientappar (förhandsgranskning)**. 
 
    a. I **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. I **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Under **åtkomstkontroller**, väljer du följande alternativ:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Begär en av de valda kontrollerna**   
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/11.png)



**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync**

För den villkorliga åtkomstprincipen i det här steget konfigurerar du följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Ange namnet på din princip för villkorlig åtkomst.

2. Under **tilldelningar**i **användare och grupper**väljer minst en användare eller grupp för varje princip för villkorlig åtkomst.

3. I **Molnappar**väljer **Office 365 Exchange Online**. 

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. I **villkor**, konfigurera **klientappar (förhandsgranskning)**. 

    I **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

5. Under **åtkomstkontroller**, väljer du följande alternativ:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Begär en av de valda kontrollerna**

     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/11.png)



**Steg 3: Konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>App protection-baserade och kompatibel enhetsprincip för Exchange Online

Det här scenariot består av en princip för villkorlig åtkomst för app-protection-baserade och kompatibla enheter för åtkomst till Exchange Online.


### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att en användare:
 
-   Konfigurerar e-post med hjälp av en intern e-postprogrammet på iOS eller Android för att ansluta till Exchange.
-   Tar emot ett e-postmeddelande som anger att åtkomst kräver att enheten registreras.
-   Laddar ned Intune-Företagsportalen och loggar in på portalen.
-   Kontrollerar e-post och uppmanas att använda Outlook-appen.
-   Laddar ned Outlook-appen.
-   Öppnar Outlook-appen och anger de autentiseringsuppgifter som används i registreringen.
-   Kan ta emot en Intune-appskyddsprincip.
-   Kan komma åt e-postmeddelande med Outlook och en Intune-appskyddsprincip.

Alla Intunes appskyddsprinciper aktiveras innan åtkomst beviljas till företagets data. Principerna kan uppmana användaren att starta om programmet eller använda en ytterligare PIN-kod. Detta är fallet om principerna som har konfigurerats för programmet och plattform.


### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För den villkorliga åtkomstprincipen i det här steget konfigurerar du följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/01.png)

1. Ange namnet på din princip för villkorlig åtkomst.

2. Under **tilldelningar**i **användare och grupper**väljer minst en användare eller grupp för varje princip för villkorlig åtkomst.

3. I **Molnappar**väljer **Office 365 Exchange Online**. 

     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. I **villkor**, konfigurera **enhetsplattformar** och **klientappar (förhandsgranskning)**. 
 
    a. I **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. I **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Under **åtkomstkontroller**, väljer du följande alternativ:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Kräv de valda kontrollerna**   
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/13.png)



**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync**

För den villkorliga åtkomstprincipen i det här steget konfigurerar du följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Ange namnet på din princip för villkorlig åtkomst.

2. Under **tilldelningar**i **användare och grupper**väljer minst en användare eller grupp för varje princip för villkorlig åtkomst.

3. I **Molnappar**väljer **Office 365 Exchange Online**. 

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. I **villkor**, konfigurera **klientappar (förhandsgranskning)**. 

    I **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

5. Under **åtkomstkontroller**, väljer du följande alternativ:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Kräv de valda kontrollerna**   
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/13.png)




**Steg 3: Konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>App-protection-baserade eller appbaserad princip för Exchange Online och SharePoint Online

Det här scenariot består av en princip för åtkomst till Exchange Online och SharePoint Online protection-baserade eller godkända appar.


### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att en användare:

- Konfigurerar klientprogram som är på listan över appar som har stöd för kravet för app protection eller kravet på godkända appar.  
- Använder klientprogram som uppfyller kravet för app-skydd och kan ta emot en Intune-appskyddsprincip.
- Använder klientprogram som uppfyller kravet för godkända appar som har stöd för Intunes appskyddsprincip.
- Öppnar programmet åtkomst till e-postmeddelande eller dokument.
- Öppnas Outlook-programmet och loggar in med autentiseringsuppgifter för Azure AD.
- Uppmanas att installera antingen Microsoft Authenticator för iOS eller Intunes företagsportal för Android använder om de inte redan är installerade.
- Installerar program och kan gå tillbaka till Outlook-appen för att fortsätta.
- Uppmanas att registrera en enhet.
- Kan ta emot en Intune-appskyddsprincip.
- Kan komma åt e-postmeddelande med Outlook och en Intune-appskyddsprincip.
- Kan komma åt platser och -dokument med en app inte på kravet för app protection men visas i kravet på godkänd app.

Alla Intunes appskyddsprinciper krävs innan åtkomst beviljas till företagets data. Principerna kan uppmana användaren att starta om programmet eller använda en ytterligare PIN-kod. Detta är fallet om principerna som har konfigurerats för programmet och plattform.

**Kommentarer**

- Du kan använda det här scenariot, om du vill stödja båda principerna för app-protection-baserade och appbaserad villkorlig åtkomst.
- I det här *eller* principer, appar med en princip för app skyddskraven utvärderas för åtkomst innan kravet på godkända appar.

### <a name="configuration"></a>Konfiguration

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För den villkorliga åtkomstprincipen i det här steget konfigurerar du följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/62.png)

1. Ange namnet på din princip för villkorlig åtkomst.

2. Under **tilldelningar**i **användare och grupper**väljer minst en användare eller grupp för varje princip för villkorlig åtkomst.

3. I **Molnappar**väljer **Office 365 Exchange Online**. 

     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/02.png)

4. I **villkor**, konfigurera **enhetsplattformar** och **klientappar (förhandsgranskning)**. 
 
    a. I **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. I **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Under **åtkomstkontroller**, väljer du följande alternativ:

   - **Kräv godkänd klientapp**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Begär en av de valda kontrollerna**
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/12.png)


**Steg 2: Konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Mer information finns i [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md). 