---
title: Principer för appskydd med villkorlig åtkomst - Azure Active Directory
description: Lär dig hur du kräver appskyddsprincip för åtkomst till molnapp med villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b282962cc713487b8ee5113b02b8533a1538fff
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631899"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Så här: Kräv appskyddsprincip och en godkänd klientapp för molnappåtkomst med villkorlig åtkomst

Människor använder regelbundet sina mobila enheter för både personliga uppgifter och arbetsuppgifter. Samtidigt som man ser till att personalen kan vara produktiv, vill organisationer också förhindra dataförlust från potentiellt osäkra program. Med villkorlig åtkomst kan organisationer begränsa åtkomsten till godkända (moderna autentiseringskompatibla) klientappar med Intune-appskyddsprinciper som tillämpas på dem.

I den här artikeln finns två scenarier för att konfigurera principer för villkorlig åtkomst för resurser som Office 365, Exchange Online och SharePoint Online.

- [Scenario 1: Office 365-appar kräver godkända appar med principer för appskydd](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 2: Exchange Online och SharePoint Online kräver en godkänd klientapp- och appskyddsprincip](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

I villkorlig åtkomst är dessa klientappar kända för att skyddas med en appskyddsprincip. Mer information om appskyddsprinciper finns i artikeln, [Översikt över appskyddsprinciper](/intune/apps/app-protection-policy)

En lista över kvalificerade klientappar finns i [Krav på appskyddsprincip](concept-conditional-access-grant.md).

> [!NOTE]
>    Klausulen eller används i principen för att göra det möjligt för användare att använda appar som stöder antingen **principen Kräv appskydd** eller **Kräv godkända kontroller för klientappbidrag.** Mer information om vilka appar som stöder kontrollen **Kräv appskyddsprincip** ger finns i [Krav på appskyddsprincip](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 1: Office 365-appar kräver godkända appar med principer för appskydd

I det här fallet har Contoso beslutat att all mobil åtkomst till Office 365-resurser måste använda godkända klientappar, till exempel Outlook mobile, OneDrive och Microsoft Teams som skyddas av en appskyddsprincip innan de får åtkomst. Alla deras användare loggar redan in med Azure AD-autentiseringsuppgifter och har licenser som tilldelats dem som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste utföra följande steg för att kräva användning av en godkänd klientapp på mobila enheter.

**Steg 1: Konfigurera en Azure AD-princip för villkorlig åtkomst för Office 365**

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare** eller de specifika användare och **grupper** som du vill tillämpa den här principen på. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Office 365 (förhandsversion)**.
1. Under **Villkor**väljer du **Enhetsplattformar**.
   1. Ställ in **Konfigurera** till **Ja**.
   1. Inkludera **Android** och **iOS**.
1. Under **Villkor**väljer du **Klientappar (förhandsgranskning)**.
   1. Ställ in **Konfigurera** till **Ja**.
   1. Välj **Mobilappar och skrivbordsklienter** och **Moderna autentiseringsklienter**.
1. Under **Bevilja Tilldelning av Åtkomstkontroller** > **Grant**väljer du följande alternativ:
   - **Kräv godkänd klientapp**
   - **Kräv appskyddsprincip (förhandsgranskning)**
   - **Kräv alla markerade kontroller**
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** om du vill skapa och aktivera principen.

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync (EAS)**

Konfigurera följande komponenter för principen Villkorlig åtkomst i det här steget:

1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare** eller de specifika användare och **grupper** som du vill tillämpa den här principen på. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Office 365 Exchange Online**.
1. Under **förhållanden:**
   1. **Klientappar (förhandsgranskning):**
      1. Ställ in **Konfigurera** till **Ja**.
      1. Välj **Mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv appskyddsprincip**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** om du vill skapa och aktivera principen.

**Steg 3: Konfigurera Intune-appskyddsprincip för iOS- och Android-klientprogram**

Läs artikeln [Så här skapar och tilldelar](/intune/apps/app-protection-policies)du principer för appskydd för steg för att skapa appskyddsprinciper för Android och iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenario 2: Exchange Online och SharePoint Online kräver en godkänd klientapp- och appskyddsprincip

I det här fallet har Contoso beslutat att användare endast får komma åt e-post- och SharePoint-data på mobila enheter så länge de använder en godkänd klientapp som Outlook mobile som skyddas av en appskyddsprincip innan de får åtkomst. Alla deras användare loggar redan in med Azure AD-autentiseringsuppgifter och har licenser som tilldelats dem som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste slutföra följande tre steg för att kräva användning av en godkänd klientapp på mobila enheter och Exchange ActiveSync-klienter.

**Steg 1: Policy för Android- och iOS-baserade moderna autentiseringsklienter som kräver användning av en godkänd klientapp och appskyddsprincip när du öppnar Exchange Online och SharePoint Online.**

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare** eller de specifika användare och **grupper** som du vill tillämpa den här principen på. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Office 365 Exchange Online** och Office **365 SharePoint Online**.
1. Under **Villkor**väljer du **Enhetsplattformar**.
   1. Ställ in **Konfigurera** till **Ja**.
   1. Inkludera **Android** och **iOS**.
1. Under **Villkor**väljer du **Klientappar (förhandsgranskning)**.
   1. Ställ in **Konfigurera** till **Ja**.
   1. Välj **Mobilappar och skrivbordsklienter** och **Moderna autentiseringsklienter**.
1. Under **Bevilja Tilldelning av Åtkomstkontroller** > **Grant**väljer du följande alternativ:
   - **Kräv godkänd klientapp**
   - **Kräv appskyddsprincip (förhandsgranskning)**
   - **Kräv en av de markerade kontrollerna**
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** om du vill skapa och aktivera principen.

**Steg 2: Princip för Exchange ActiveSync-klienter som kräver användning av en godkänd klientapp.**

1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare** eller de specifika användare och **grupper** som du vill tillämpa den här principen på. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Office 365 Exchange Online**.
1. Under **förhållanden:**
   1. **Klientappar (förhandsgranskning):**
      1. Ställ in **Konfigurera** till **Ja**.
      1. Välj **Mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv appskyddsprincip**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** om du vill skapa och aktivera principen.

**Steg 3: Konfigurera Intune-appskyddsprincip för iOS- och Android-klientprogram.**

Läs artikeln [Så här skapar och tilldelar](/intune/apps/app-protection-policies)du principer för appskydd för steg för att skapa appskyddsprinciper för Android och iOS. 

## <a name="next-steps"></a>Nästa steg

[Vad är villkorsstyrd åtkomst?](overview.md)

[Komponenter för villkorlig åtkomst](concept-conditional-access-policies.md)

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

