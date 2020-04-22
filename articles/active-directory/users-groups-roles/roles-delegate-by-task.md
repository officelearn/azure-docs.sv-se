---
title: Delegera roller efter administratörsuppgift - Azure Active Directory | Microsoft-dokument
description: Roller att delegera för identitetsuppgifter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/03/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd24650c9bf0c4de155b5bfc8723cfa1fef01548
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755410"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Administratörsroller efter administratörsuppgift i Azure Active Directory

I den här artikeln kan du hitta den information som behövs för att begränsa en användares administratörsbehörigheter genom att tilldela minst privilegierade roller i Azure Active Directory (Azure AD). Du hittar administratörsuppgifter ordnade efter funktionsområde och den minst privilegierade roll som krävs för att utföra varje uppgift, tillsammans med ytterligare icke-globala administratörsroller som kan utföra uppgiften.

## <a name="application-proxy"></a>Programproxy

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera programproxyapp | Programadministratör | 
Konfigurera egenskaper för kopplingsgrupp | Programadministratör | 
Skapa programregistrering när möjligheten är inaktiverad för alla användare | Programutvecklare | Molnprogramadministratör, programadministratör
Skapa kopplingsgrupp | Programadministratör | 
Ta bort kopplingsgrupp | Programadministratör | 
Inaktivera programproxy | Programadministratör | 
Ladda ned anslutningstjänsten | Programadministratör | 
Läs alla konfigurationer | Programadministratör | 

## <a name="b2c"></a>B2C

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD B2C-kataloger | Alla icke-gästanvändare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa B2C-program | Global administratör | 
Skapa företagsprogram | Molnprogramadministratör | Programadministratör
Skapa, läsa, uppdatera och ta bort B2C-principer | B2C IEF-principadministratör | 
Skapa, läsa, uppdatera och ta bort identitetsleverantörer | Administratör för extern identitetsprovider | 
Skapa, läsa, uppdatera och ta bort användarflöden för återställning av lösenord | B2C-administratör för användarflöde | 
Skapa, läsa, uppdatera och ta bort profilredigering användarflöden | B2C-administratör för användarflöde | 
Skapa, läsa, uppdatera och ta bort inloggningsanvändarflöden | B2C-administratör för användarflöde | 
Skapa, läsa, uppdatera och ta bort användarflöde för registrering |B2C-administratör för användarflöde | 
Skapa, läsa, uppdatera och ta bort användarattribut | Administratör för användarflödesattribut för B2C | 
Skapa, läsa, uppdatera och ta bort användare | Användaradministratör
Läs alla konfigurationer | Global läsare | 
Läs B2C-granskningsloggar | Global läsare ([se dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C Global-läsare har inte samma behörigheter som azure AD-globala administratörer. Om du har globala administratörsbehörighet för Azure AD B2C kontrollerar du att du finns i en Azure AD B2C-katalog och inte en Azure AD-katalog.

## <a name="company-branding"></a>Företagsanpassning

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera varumärkesexponering | Global administratör | 
Läs alla konfigurationer | Katalogläsare | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Företagets fastigheter

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera företagsegenskaper | Global administratör | 

## <a name="connect"></a>Anslut

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Genomgångsautentisering | Administratör för hybrididentitet | 
Läs alla konfigurationer | Global läsare | Administratör för hybrididentitet |
Sömlös enkel inloggning | Administratör för hybrididentitet | 

## <a name="connect-health"></a>Connect Health

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägga till eller ta bort tjänster | Ägare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Använda korrigeringar för synkroniseringsfel | Bidragsgivare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera meddelanden | Bidragsgivare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera inställningar | Ägare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurera synkroniseringsmeddelanden | Bidragsgivare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Läs ADFS-säkerhetsrapporter | Säkerhetsläsare | Bidragsgivare, Ägare
Läs alla konfigurationer | Läsare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Bidragsgivare, Ägare
Läs synkroniseringsfel | Läsare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Bidragsgivare, Ägare
Läs synkroniseringstjänster | Läsare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Bidragsgivare, Ägare
Visa mått och aviseringar | Läsare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Bidragsgivare, Ägare
Visa mått och aviseringar | Läsare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Bidragsgivare, Ägare
Visa synkroniseringstjänstmått och aviseringar | Läsare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Bidragsgivare, Ägare

## <a name="custom-domain-names"></a>Egna domännamn

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera domäner | Global administratör | 
Läs alla konfigurationer | Katalogläsare | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD Domain Services-instans | Global administratör | 
Utföra alla Azure AD Domain Services-uppgifter | Gruppen Azure AD DC-administratörer ([se dokumentation](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Läs alla konfigurationer | Läsare på Azure-prenumeration som innehåller AD DS-tjänst | 

## <a name="devices"></a>Enheter

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Inaktivera enhet | Administratör för molnenheter | 
Aktivera enhet | Administratör för molnenheter | 
Läs grundläggande konfiguration | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Läs BitLocker-tangenter | Säkerhetsläsare | Lösenordsadministratör, Säkerhetsadministratör

## <a name="enterprise-applications"></a>Företagsprogram

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Samtycke till delegerade behörigheter | Administratör för molnprogram | Programadministratör
Samtycke till programbehörigheter som inte inkluderar Microsoft Graph | Administratör för molnprogram | Programadministratör
Samtycke till programbehörigheter till Microsoft Graph | Administratör för privilegierad roll | 
Samtycke till att program får tillgång till egna data | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa företagsprogram | Administratör för molnprogram | Programadministratör
Hantera programproxy | Programadministratör | 
Hantera användarinställningar | Global administratör | 
Läsåtkomstgranskning av en grupp eller en app | Säkerhetsläsare | Säkerhetsadministratör, användaradministratör
Läs alla konfigurationer | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Uppdatera tilldelningar av företagsprogram | Ägare av företagsprogram ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera ägare av företagsprogram | Ägare av företagsprogram ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera egenskaper för företagsprogram | Ägare av företagsprogram ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera etablering av företagsprogram | Ägare av företagsprogram ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera självbetjäning för företagsprogram | Ägare av företagsprogram ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera egenskaper för enkel inloggning | Ägare av företagsprogram ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör

## <a name="entitlement-management"></a>Berättigandehantering
Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägga till resurser i en katalog | Användaradministratör | Med rättighetshantering kan du delegera den här uppgiften till katalogägaren ([se dokumentation](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Lägga till SharePoint Online-webbplatser i katalogen | Global administratör


## <a name="groups"></a>Grupper

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Användaradministratör | 
Skapa grupp | Användaradministratör | 
Skapa, uppdatera eller ta bort åtkomstgranskning av en grupp eller en app | Användaradministratör | 
Hantera gruppens förfallodatum | Användaradministratör | 
Hantera gruppinställningar | Gruppadministratör | Användaradministratör | 
Läs alla konfigurationer (utom dolt medlemskap) | Katalogläsare | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Läs dolt medlemskap | Gruppmedlem | Gruppägare, Lösenordsadministratör, Exchange-administratör, SharePoint-administratör, Teams-administratör, Användaradministratör
Läs medlemskap i grupper med dolt medlemskap | Administratör för helpdesk | Användaradministratör, Teams-administratör
Återkalla licens | Licensadministratör | Användaradministratör
Uppdatera gruppmedlemskap | Gruppägare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör
Uppdatera gruppägare | Gruppägare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör
Uppdatera gruppegenskaper | Gruppägare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör

## <a name="identity-protection"></a>Identity Protection

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera varningsmeddelanden| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera MFA-princip| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera inloggningsriskprincip| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera användarriskprincip | Säkerhetsadministratör | 
Konfigurera veckovisa sammanfattningar | Säkerhetsadministratör| 
Ignorera alla riskidentifieringar | Säkerhetsadministratör | 
Åtgärda eller åtgärda säkerhetsproblem | Säkerhetsadministratör | 
Läs alla konfigurationer | Säkerhetsläsare | 
Läs alla riskidentifieringar | Säkerhetsläsare | 
Läs sårbarheter | Säkerhetsläsare | 

## <a name="licenses"></a>Licenser

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Licensadministratör | Användaradministratör
Läs alla konfigurationer | Katalogläsare | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Återkalla licens | Licensadministratör | Användaradministratör
Prova eller köpa prenumeration | Faktureringsadministratör | 


## <a name="monitoring---audit-logs"></a>Övervakning - Granskningsloggar

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs granskningsloggar | Rapporter läsare | Säkerhetsläsare, säkerhetsadministratör

## <a name="monitoring---sign-ins"></a>Övervakning - Inloggningar

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs inloggningsloggar | Rapporter läsare | Säkerhetsläsare, säkerhetsadministratör

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Ta bort alla befintliga applösenord som genereras av de valda användarna | Global administratör | 
Inaktivera MFA | Global administratör | 
Aktivera MFA | Global administratör | 
Hantera MFA-tjänstinställningar | Global administratör | 
Kräv att valda användare tillhandahåller kontaktmetoder igen | Administratör för autentisering | 
Återställa multifaktorautentisering på alla ihågkomna enheter  | Administratör för autentisering | 

## <a name="mfa-server"></a>MFA-server

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Blockera/avblockera användare | Global administratör | 
Konfigurera kontoutelåsning | Global administratör | 
Konfigurera cachelagringsregler | Global administratör | 
Konfigurera bedrägerivarning | Global administratör
Konfigurera meddelanden | Global administratör | 
Konfigurera en gång bypass | Global administratör | 
Konfigurera inställningar för telefonsamtal | Global administratör | 
Konfigurera leverantörer | Global administratör | 
Konfigurera serverinställningar | Global administratör | 
Läs aktivitetsrapport | Global läsare | 
Läs alla konfigurationer | Global läsare | 
Läsa serverstatus | Global läsare |  

## <a name="organizational-relationships"></a>Organisationsrelationer

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera identitetsleverantörer | Administratör för extern identitetsprovider | 
Hantera inställningar | Global administratör | 
Hantera användningsvillkor | Global administratör | 
Läs alla konfigurationer | Global läsare | 

## <a name="password-reset"></a>Lösenordsåterställning

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör |
Konfigurera anpassning | Global administratör |
Konfigurera meddelande | Global administratör |
Konfigurera lokal integrering | Global administratör |
Konfigurera egenskaper för återställning av lösenord | Användaradministratör | Global administratör
Konfigurera registrering | Global administratör |
Läs alla konfigurationer | Säkerhetsadministratör | Användaradministratör |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela användare till roller | Administratör för privilegierad roll | 
Konfigurera rollinställningar | Administratör för privilegierad roll | 
Visa granskningsaktivitet | Säkerhetsläsare | 
Visa rollmedlemskap | Säkerhetsläsare | 

## <a name="roles-and-administrators"></a>Roller och administratörer

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera rolltilldelningar | Administratör för privilegierad roll | 
Läs åtkomstgranskning av en Azure AD-roll  | Säkerhetsläsare | Säkerhetsadministratör, administratör för privilegierad roll
Läs alla konfigurationer | Standardanvändarroll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Säkerhet - Autentiseringsmetoder

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör | 
Läs alla konfigurationer | Global läsare | 

## <a name="security---conditional-access"></a>Säkerhet - Villkorlig åtkomst

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera MFA-betrodda IP-adresser | Administratör för villkorlig åtkomst | 
Skapa anpassade kontroller | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa namngivna platser | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa principer | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa användningsvillkor | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa VPN-anslutningscertifikat | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Ta bort klassisk princip | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Ta bort användningsvillkor | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Ta bort VPN-anslutningscertifikat | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Inaktivera klassisk princip | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Hantera anpassade kontroller | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Hantera namngivna platser | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Hantera användningsvillkor | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Läs alla konfigurationer | Säkerhetsläsare | Säkerhetsadministratör
Läs namngivna platser | Säkerhetsläsare | Administratör för villkorlig åtkomst, säkerhetsadministratör

## <a name="security---identity-security-score"></a>Säkerhet - Identitetssäkerhetspoäng

Aktivitet | Minst privilegierad roll | Ytterligare roller | 
---- | --------------------- | ----------------
Läs alla konfigurationer | Säkerhetsläsare | Säkerhetsadministratör
Läs säkerhetspoäng | Säkerhetsläsare | Säkerhetsadministratör
Uppdatera händelsestatus | Säkerhetsadministratör | 

## <a name="security---risky-sign-ins"></a>Säkerhet - Riskfyllda inloggningar

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs alla konfigurationer | Säkerhetsläsare | 
Läs riskfyllda inloggningar | Säkerhetsläsare | 

## <a name="security---users-flagged-for-risk"></a>Säkerhet - Användare flaggade för risk

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Ignorera alla händelser | Säkerhetsadministratör | 
Läs alla konfigurationer | Säkerhetsläsare | 
Läsa användare flaggade för risk | Säkerhetsläsare | 

## <a name="users"></a>Användare

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägga till användare i katalogrollen | Administratör för privilegierad roll | 
Lägga till användare i grupp | Användaradministratör | 
Tilldela licens | Licensadministratör | Användaradministratör
Skapa gästanvändare | Gäst inbjudna | Användaradministratör
Skapa användare | Användaradministratör | 
Ta bort användare | Användaradministratör | 
Ogiltigförklara uppdateringstoken för begränsade administratörer (se dokumentation) | Användaradministratör | 
Ogiltigförklara uppdateringstoken för icke-administratörer (se dokumentation) | Lösenordsadministratör | Användaradministratör
Ogiltigförklara uppdateringstoken för privilegierade administratörer (se dokumentation) | Privilegierad autentiseringsadministratör | 
Läs grundläggande konfiguration | Standardrollen Användare ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Återställa lösenord för begränsade administratörer (se dokumentation) | Användaradministratör | 
Återställa lösenord för icke-administratörer (se dokumentation) | Lösenordsadministratör | Användaradministratör
Återställa lösenord för privilegierade administratörer | Privilegierad autentiseringsadministratör | 
Återkalla licens | Licensadministratör | Användaradministratör
Uppdatera alla egenskaper utom användarens huvudnamn | Användaradministratör | 
Uppdatera användarnamn för begränsade administratörer (se dokumentation) | Användaradministratör | 
Egenskapen Uppdatera användarnamn för privilegierade administratörer (se dokumentation) | Global administratör | 
Uppdatera användarinställningar | Global administratör | 


## <a name="support"></a>Support

Aktivitet | Minst privilegierad roll | Ytterligare roller
---- | --------------------- | ----------------
Skicka supportbiljett | Tjänstadministratör | Programadministratör, Azure Information Protection Administrator, Faktureringsadministratör, Cloud Application Administrator, Compliance Administrator, Dynamics 365 Administrator, Desktop Analytics Administrator, Exchange Administrator, Password Administrator, Intune Administrator, Skype för företag Administratör, Power BI-administratör, Privilegierad autentiseringsadministratör, SharePoint-administratör, Teams Communications Administrator, Teams Administrator, User Administrator, Workplace Analytics Administrator

## <a name="next-steps"></a>Nästa steg

* [Så här tilldelar eller tar du bort azure AD-administratörsroller](directory-manage-roles-portal.md)
* [Referens för Azure AD-administratörsroller](directory-assign-admin-roles.md)
