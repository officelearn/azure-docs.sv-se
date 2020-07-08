---
title: Skydds principer för appar med villkorlig åtkomst – Azure Active Directory
description: Lär dig hur du kräver app Protection-princip för Cloud app-åtkomst med villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/08/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae584bdfa97b2c30cab5f15881323c26366592c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253366"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Gör så här: Kräv app Protection-princip och en godkänd klient app för åtkomst till Cloud App med villkorlig åtkomst

Användarna använder regelbundet sina mobila enheter för både personliga och arbetsrelaterade uppgifter. När du ser till att personalen kan vara produktiv, vill organisationer även förhindra data förlust från potentiellt osäkra program. Med villkorlig åtkomst kan organisationer begränsa åtkomsten till godkända (moderna autentiserings kapabel)-klient program med Intunes skydds principer för appar.

Den här artikeln innehåller tre scenarier för att konfigurera principer för villkorlig åtkomst för resurser som Office 365, Exchange Online och SharePoint Online.

- [Scenario 1: Office 365-appar kräver godkända appar med skydds principer för appar](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 2: webbläsarbaserade appar kräver godkända appar med skydds principer för appar](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 3: Exchange Online och SharePoint Online kräver en godkänd klient app och app Protection-princip](#scenario-3-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

I den villkorliga åtkomsten känner de här klient programmen till att skyddas med en skydds princip för appar. Mer information om skydds principer för appar finns i artikeln, [Översikt över app Protection-principer](/intune/apps/app-protection-policy)

En lista över tillgängliga klient program finns i [krav på skydds princip för appar](concept-conditional-access-grant.md).

> [!NOTE]
>    Or-satsen används i principen för att göra det möjligt för användare att använda appar som har stöd för antingen **Kräv skydds princip** för appar eller **Kräv godkända kontroller för klient programs** tilldelning. Mer information om vilka appar som stöder **Kräv skydds kontroll för program skydds principen** finns i [krav på skydds princip](concept-conditional-access-grant.md)för appar.

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 1: Office 365-appar kräver godkända appar med skydds principer för appar

I det här scenariot har contoso beslutat att all mobil åtkomst till Office 365-resurser måste använda godkända klient program, t. ex. Outlook Mobile och OneDrive, som skyddas av en skydds princip för appar innan åtkomsten tas emot. Alla användare har redan loggat in med autentiseringsuppgifter för Azure AD och har tilldelade licenser som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste utföra följande steg för att kräva att ett godkänt klient program används på mobila enheter.

**Steg 1: Konfigurera en princip för villkorlig åtkomst för Azure AD för Office 365**

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar**väljer du **Office 365 (för hands version)**.
1. Under **villkor**väljer du **enhets plattformar**.
   1. **Konfigurera** till **Ja**.
   1. Ta med **Android** och **iOS**.
1. Under **villkor**väljer du **klient program (för hands version)**.
   1. **Konfigurera** till **Ja**.
   1. Välj **Mobilappar och skrivbordsklienter** och **Moderna autentiseringsklienter**.
1. Under **åtkomst kontrolls**  >  **beviljande**väljer du följande alternativ:
   - **Kräv godkänd klientapp**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv alla markerade kontroller**
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync (EAS)**

Konfigurera följande komponenter för principen för villkorlig åtkomst i det här steget:

1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar**väljer du **Office 365 Exchange Online**.
1. Under **villkor**:
   1. **Klient program (för hands version)**:
      1. **Konfigurera** till **Ja**.
      1. Välj **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.
1. Under **åtkomst kontroller**  >  **tilldelar**väljer du **bevilja åtkomst**, **kräver app Protection-princip**och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 3: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

Läs artikeln [så här skapar och tilldelar du skydds principer för appar](/intune/apps/app-protection-policies)för att skapa skydds principer för Android och iOS. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 2: webbläsarbaserade appar kräver godkända appar med skydds principer för appar

I det här scenariot har contoso beslutat att all åtkomst till mobila webbplatser i Office 365-resurser måste använda en godkänd klient app, till exempel Edge för iOS och Android, som skyddas av en skydds princip för appar innan åtkomsten tas emot. Alla användare har redan loggat in med autentiseringsuppgifter för Azure AD och har tilldelade licenser som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste utföra följande steg för att kräva att ett godkänt klient program används på mobila enheter.

**Steg 1: Konfigurera en princip för villkorlig åtkomst för Azure AD för Office 365**

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar**väljer du **Office 365 (för hands version)**.
1. Under **villkor**väljer du **enhets plattformar**.
   1. **Konfigurera** till **Ja**.
   1. Ta med **Android** och **iOS**.
1. Under **villkor**väljer du **klient program (för hands version)**.
   1. **Konfigurera** till **Ja**.
   1. Välj **webbläsare**.
1. Under **åtkomst kontrolls**  >  **beviljande**väljer du följande alternativ:
   - **Kräv godkänd klientapp**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv alla markerade kontroller**
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 2: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram**

Läs artikeln [så här skapar och tilldelar du skydds principer för appar](/intune/apps/app-protection-policies)för att skapa skydds principer för Android och iOS. 

## <a name="scenario-3-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenario 3: Exchange Online och SharePoint Online kräver en godkänd klient app och app Protection-princip

I det här scenariot har contoso beslutat att användare bara får åtkomst till e-post och SharePoint-data på mobila enheter, så länge de använder en godkänd klient app som Outlook Mobile skyddas av en app Protection-princip innan åtkomsten tas emot. Alla användare har redan loggat in med autentiseringsuppgifter för Azure AD och har tilldelade licenser som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste utföra följande tre steg för att kräva att en godkänd klient app används på mobila enheter och Exchange ActiveSync-klienter.

**Steg 1: princip för Android-och iOS-baserade moderna autentisering klienter som kräver användning av en godkänd klient app och app Protection-princip vid åtkomst till Exchange Online och SharePoint Online.**

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar**väljer du **Office 365 Exchange Online** och **Office 365 SharePoint Online**.
1. Under **villkor**väljer du **enhets plattformar**.
   1. **Konfigurera** till **Ja**.
   1. Ta med **Android** och **iOS**.
1. Under **villkor**väljer du **klient program (för hands version)**.
   1. **Konfigurera** till **Ja**.
   1. Välj **Mobilappar och skrivbordsklienter** och **Moderna autentiseringsklienter**.
1. Under **åtkomst kontrolls**  >  **beviljande**väljer du följande alternativ:
   - **Kräv godkänd klientapp**
   - **Kräv app Protection-princip (förhands granskning)**
   - **Kräv en av de valda kontrollerna**
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 2: princip för Exchange ActiveSync-klienter som kräver användning av en godkänd klient app.**

1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar**väljer du **Office 365 Exchange Online**.
1. Under **villkor**:
   1. **Klient program (för hands version)**:
      1. **Konfigurera** till **Ja**.
      1. Välj **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.
1. Under **åtkomst kontroller**  >  **tilldelar**väljer du **bevilja åtkomst**, **kräver app Protection-princip**och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 3: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram.**

Läs artikeln [så här skapar och tilldelar du skydds principer för appar](/intune/apps/app-protection-policies)för att skapa skydds principer för Android och iOS. 

## <a name="next-steps"></a>Nästa steg

[Vad är villkorlig åtkomst?](overview.md)

[Komponenter för villkorlig åtkomst](concept-conditional-access-policies.md)

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

