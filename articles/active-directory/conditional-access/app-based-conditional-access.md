---
title: Godkända klientappar med villkorlig åtkomst - Azure Active Directory
description: Lär dig hur du kräver godkända klientappar för molnappåtkomst med villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a215e2bb7d9d1cf9013414037383590456296cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480903"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Så här: Kräv godkända klientappar för molnappåtkomst med villkorlig åtkomst

Människor använder regelbundet sina mobila enheter för både personliga uppgifter och arbetsuppgifter. Samtidigt som man ser till att personalen kan vara produktiv, vill organisationer också förhindra dataförlust från potentiellt osäkra program. Med villkorlig åtkomst kan organisationer begränsa åtkomsten till godkända (moderna autentiseringskompatibla) klientappar.

I den här artikeln finns två scenarier för att konfigurera principer för villkorlig åtkomst för resurser som Office 365, Exchange Online och SharePoint Online.

- [Scenario 1: Office 365-appar kräver ett godkänt klientprogram](#scenario-1-office-365-apps-require-an-approved-client-app)
- [Scenario 2: Exchange Online och SharePoint Online kräver en godkänd klientapp](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

I Villkorlig åtkomst kallas den här funktionen för att kräva en godkänd klientapp. En lista över godkända klientappar finns i [godkända klientappkrav](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> För att kräva godkända klientappar för iOS- och Android-enheter måste dessa enheter först registrera sig i Azure AD.

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>Scenario 1: Office 365-appar kräver ett godkänt klientprogram

I det här fallet har Contoso beslutat att användare som använder mobila enheter kan komma åt alla Office 365-tjänster så länge de använder godkända klientappar, till exempel Outlook mobile, OneDrive och Microsoft Teams. Alla deras användare loggar redan in med Azure AD-autentiseringsuppgifter och har licenser som tilldelats dem som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste slutföra följande tre steg för att kräva användning av en godkänd klientapp på mobila enheter.

**Steg 1: Policy för Android och iOS-baserade moderna autentiseringsklienter som kräver användning av ett godkänt klientprogram när du öppnar Exchange Online.**

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
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv godkänd klientapp**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** om du vill skapa och aktivera principen.

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync (EAS)**

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
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv godkänd klientapp**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** om du vill skapa och aktivera principen.

**Steg 3: Konfigurera Intune-appskyddsprincip för iOS- och Android-klientprogram.**

Läs artikeln [Så här skapar och tilldelar](/intune/apps/app-protection-policies)du principer för appskydd för steg för att skapa appskyddsprinciper för Android och iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scenario 2: Exchange Online och SharePoint Online kräver en godkänd klientapp

I det här fallet har Contoso beslutat att användare endast får komma åt e-post- och SharePoint-data på mobila enheter så länge de använder en godkänd klientapp som Outlook mobile. Alla deras användare loggar redan in med Azure AD-autentiseringsuppgifter och har licenser som tilldelats dem som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste slutföra följande tre steg för att kräva användning av en godkänd klientapp på mobila enheter och Exchange ActiveSync-klienter.

**Steg 1: Policy för Android- och iOS-baserade moderna autentiseringsklienter som kräver användning av ett godkänt klientprogram vid åtkomst till Exchange Online och SharePoint Online.**

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
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv godkänd klientapp**och välj **Välj**.
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
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv godkänd klientapp**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** om du vill skapa och aktivera principen.

**Steg 3: Konfigurera Intune-appskyddsprincip för iOS- och Android-klientprogram.**

Läs artikeln [Så här skapar och tilldelar](/intune/apps/app-protection-policies)du principer för appskydd för steg för att skapa appskyddsprinciper för Android och iOS. 

## <a name="next-steps"></a>Nästa steg

[Vad är villkorlig åtkomst?](overview.md)

[Komponenter för villkorlig åtkomst](concept-conditional-access-policies.md)

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
