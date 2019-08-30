---
title: Delegera minst privilegierade roller efter administratörs uppgift – Azure Active Directory | Microsoft Docs
description: Roller som ska delegeras för identitets uppgifter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62814bb1e2934c406dc5fb1eb3b3bc3f3d2ee3b7
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135558"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Administratörs roller efter administratörs uppgift i Azure Active Directory

I den här artikeln hittar du den information som behövs för att begränsa en användares administratörs behörighet genom att tilldela minst privilegierade roller i Azure Active Directory (Azure AD). Du hittar administratörs uppgifter ordnade efter funktions områden och den minst privilegierade rollen som krävs för att utföra varje aktivitet, tillsammans med ytterligare icke-globala administratörs roller som kan utföra uppgiften.

## <a name="application-proxy"></a>Programproxy

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera Application Proxy-appen | Programadministratör | 
Konfigurera egenskaper för kopplings grupp | Programadministratör | 
Skapa program registrering när möjligheten är inaktive rad för alla användare | Programutvecklare | Moln program administratör, program administratör
Skapa anslutnings grupp | Programadministratör | 
Ta bort anslutnings grupp | Programadministratör | 
Inaktivera programproxy | Programadministratör | 
Hämta anslutningsapptjänsten | Programadministratör | 
Läs all konfiguration | Programadministratör | 

## <a name="b2c"></a>B2C

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD B2C kataloger | Alla användare som inte är gäst ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa B2C-program | Global administratör | 
Skapa företags program | Molnprogramadministratör | Programadministratör
Skapa, läsa, uppdatera och ta bort B2C-principer | Global administratör | 
Skapa, läsa, uppdatera och ta bort identitets leverantörer | Global administratör | 
Skapa, läsa, uppdatera och ta bort användar flöden för lösen ords återställning | Global administratör | 
Skapa, läsa, uppdatera och ta bort profil redigera användar flöden | Global administratör | 
Skapa, läsa, uppdatera och ta bort användar flöden för inloggning | Global administratör | 
Skapa, läsa, uppdatera och ta bort användar flöde för registrering |Global administratör | 
Skapa, läsa, uppdatera och ta bort användarattribut | Global administratör | 
Skapa, läsa, uppdatera och ta bort användare | Global administratör ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Läs all konfiguration | Global administratör | 
Läs gransknings loggar för B2C | Global administratör ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C globala administratörer har inte samma behörigheter som globala Azure AD-administratörer. Om du har Azure AD B2C global administratörs behörighet kontrollerar du att du är i en Azure AD B2C katalog och inte i Azure AD-katalogen.

## <a name="company-branding"></a>Företagsanpassning

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera företagsanpassning | Global administratör | 
Läs all konfiguration | Katalogläsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Företags egenskaper

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera företags egenskaper | Global administratör | 

## <a name="connect"></a>Anslut

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Genom strömnings autentisering | Global administratör | 
Läs all konfiguration | Global administratör | 
Smidig enkel inloggning | Global administratör | 

## <a name="connect-health"></a>Connect Health

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägg till eller ta bort tjänster | Ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Tillämpa fel korrigeringar på synkroniseringsfel | Bidrags givare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera meddelanden | Bidrags givare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera inställningar | Ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurera sync-meddelanden | Bidrags givare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Läsa ADFS säkerhets rapporter | Säkerhetsläsare | Deltagare, ägare
Läs all konfiguration | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Fel vid läsning av synkroniseringsfel | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Läs Sync Services | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar för synkroniseringstjänst | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare


## <a name="custom-domain-names"></a>Anpassade domännamn

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera domäner | Global administratör | 
Läs all konfiguration | Katalogläsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD Domain Services instans | Global administratör | 
Utföra alla Azure AD Domain Services uppgifter | Gruppen Azure AD DC-administratörer ([Se dokumentationen](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Läs all konfiguration | Läsare på Azure-prenumeration som innehåller AD DS-tjänsten | 

## <a name="devices"></a>Enheter

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Inaktivera enheten | Molnenhetsadministratör | 
Aktivera enheten | Molnenhetsadministratör | 
Läsa grundläggande konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Läs BitLocker-nycklar | Säkerhetsläsare | Lösen ords administratör, säkerhets administratör

## <a name="enterprise-applications"></a>Företagsprogram

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Medgivande till alla delegerade behörigheter | Molnprogramadministratör | Programadministratör
Medgivande till program behörigheter som inte inkluderar Microsoft Graph eller Azure AD Graph | Molnprogramadministratör | Programadministratör
Medgivande till program behörigheter till Microsoft Graph eller Azure AD Graph | Global administratör | 
Medgivande till program som har åtkomst till egna data | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa företags program | Molnprogramadministratör | Programadministratör
Hantera programproxy | Programadministratör | 
Hantera användarinställningar | Global administratör | 
Läs åtkomst granskning av en grupp eller en app | Säkerhetsläsare | Säkerhets administratör, användar administratör
Läs all konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Uppdatera företags program tilldelningar | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera företags program ägare | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera egenskaper för företags program | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera företags applikations etablering | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera Self-service för företags program | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera egenskaper för enkel inloggning | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör



## <a name="groups"></a>Grupper

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Användaradministratör | 
Skapa grupp | Användaradministratör | 
Skapa, uppdatera eller ta bort åtkomst granskning för en grupp eller en app | Användaradministratör | 
Hantera grupp förfallo datum | Användaradministratör | 
Hantera gruppinställningar | Global administratör | 
Läs all konfiguration (förutom dolt medlemskap) | Katalogläsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Läs dolt medlemskap | Grupp medlem | Grupp ägare, lösen ords administratör, Exchange-administratör, SharePoint-administratör, team administratör, användar administratör
Läsa medlemskap i grupper med dolt medlemskap | Supportavdelningsadministratör | Användar administratör, team administratör
Återkalla licens | Licensadministratör | Användaradministratör
Uppdatera grupp medlemskap | Grupp ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör
Uppdatera grupp ägare | Grupp ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör
Uppdatera grupp egenskaper | Grupp ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör

## <a name="identity-protection"></a>Identity Protection

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera aviserings meddelanden| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera MFA-principen| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera inloggnings risk princip| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera användar risk princip | Säkerhetsadministratör | 
Konfigurera vecko sammandrag | Säkerhetsadministratör| 
Ignorera alla risk identifieringar | Säkerhetsadministratör | 
Åtgärda eller ignorera sårbarhet | Säkerhetsadministratör | 
Läs all konfiguration | Säkerhetsläsare | 
Läs alla risk identifieringar | Säkerhetsläsare | 
Läs sårbarheter | Säkerhetsläsare | 

## <a name="licenses"></a>Licenser

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Licensadministratör | Användaradministratör
Läs all konfiguration | Katalogläsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Återkalla licens | Licensadministratör | Användaradministratör
Prova eller köp prenumerationen | Faktureringsadministratör | 


## <a name="monitoring---audit-logs"></a>Övervakning – gransknings loggar

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs gransknings loggar | Rapportläsare | Säkerhets läsare, säkerhets administratör

## <a name="monitoring---sign-ins"></a>Övervakning-inloggnings program

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs inloggnings loggar | Rapportläsare | Säkerhets läsare, säkerhets administratör

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Ta bort alla befintliga applösenord som har genererats av de valda användarna | Global administratör | 
Inaktivera MFA | Global administratör | 
Aktivera MFA | Global administratör | 
Hantera inställningar för MFA-tjänsten | Global administratör | 
Kräv att valda användare ska tillhandahålla kontakt metoder igen | Autentiseringsadministratör | 
Återställa Multi-Factor Authentication på alla sparade enheter  | Autentiseringsadministratör | 

## <a name="mfa-server"></a>MFA Server

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Blockera/avblockera användare | Global administratör | 
Konfigurera konto utelåsning | Global administratör | 
Konfigurera regler för cachelagring | Global administratör | 
Konfigurera bedrägeri avisering | Global administratör
Konfigurera meddelanden | Global administratör | 
Konfigurera en kringgåd körning | Global administratör | 
Konfigurera inställningar för telefonsamtal | Global administratör | 
Konfigurera providers | Global administratör | 
Konfigurera Server inställningar | Global administratör | 
Läs aktivitets rapport | Global administratör | 
Läs all konfiguration | Global administratör | 
Läs Server status | Global administratör |  

## <a name="organizational-relationships"></a>Organisationsrelationer

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera identitetsproviders | Global administratör | 
Hantera inställningar | Global administratör | 
Hantera användnings villkor | Global administratör | 
Läs all konfiguration | Global administratör | 

## <a name="password-reset"></a>Återställ lösenord

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör |
Konfigurera anpassning | Global administratör |
Konfigurera meddelande | Global administratör |
Konfigurera lokal integrering | Global administratör |
Konfigurera egenskaper för lösen ords återställning | Användaradministratör | Global administratör
Konfigurera registrering | Global administratör |
Läs all konfiguration | Säkerhetsadministratör | Användaradministratör |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela användare till roller | Administratör för privilegierad roll | 
Konfigurera rollinställningar | Administratör för privilegierad roll | 
Visa gransknings aktivitet | Säkerhetsläsare | 
Visa roll medlemskap | Säkerhetsläsare | 

## <a name="roles-and-administrators"></a>Roller och administratörer

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera rolltilldelningar | Administratör för privilegierad roll | 
Läs åtkomst granskning av en Azure AD-roll  | Säkerhetsläsare | Säkerhets administratör, privilegie rad roll administratör
Läs all konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Metoder för säkerhet – autentisering

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör | 
Läs all konfiguration | Global administratör | 

## <a name="security---conditional-access"></a>Säkerhet-villkorlig åtkomst

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera MFA-betrodda IP-adresser | Administratör av villkorsstyrd åtkomst | 
Skapa anpassade kontroller | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa namngivna platser | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa principer | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa användnings villkor | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa VPN-anslutnings certifikat | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Ta bort klassisk princip | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Ta bort användnings villkor | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Ta bort VPN-anslutningens certifikat | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Inaktivera klassisk princip | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Hantera anpassade kontroller | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Hantera namngivna platser | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Hantera användnings villkor | Administratör av villkorsstyrd åtkomst | Säkerhetsadministratör
Läs all konfiguration | Säkerhetsläsare | Säkerhetsadministratör
Läs namngivna platser | Säkerhetsläsare | Administratör för villkorlig åtkomst, säkerhets administratör

## <a name="security---identity-security-score"></a>Säkerhets resultat för säkerhet och identitet

Uppgift | Minst privilegie rad roll | Ytterligare roller | 
---- | --------------------- | ----------------
Läs all konfiguration | Säkerhetsläsare | Säkerhetsadministratör
Läs säkerhets Poäng | Säkerhetsläsare | Säkerhetsadministratör
Uppdatera händelse status | Säkerhetsadministratör | 

## <a name="security---risky-sign-ins"></a>Säkerhet – riskfyllda inloggningar

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs all konfiguration | Säkerhetsläsare | 
Läs riskfyllda inloggningar | Säkerhetsläsare | 

## <a name="security---users-flagged-for-risk"></a>Säkerhet – användare som har flaggats för risk

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Stäng alla händelser | Säkerhetsadministratör | 
Läs all konfiguration | Säkerhetsläsare | 
Läs användare som har flaggats för risk | Säkerhetsläsare | 

## <a name="users"></a>Användare

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägg till rollen användare i katalog | Administratör för privilegierad roll | 
Lägg till användaren i gruppen | Användaradministratör | 
Tilldela licens | Licensadministratör | Användaradministratör
Skapa gäst användare | Gästinbjudare | Användaradministratör
Skapa användare | Användaradministratör | 
Ta bort användare | Användaradministratör | 
Invalidera uppdateringstoken för begränsade administratörer (se dokumentationen) | Användaradministratör | 
Ogiltig verifiering av uppdateringstoken för icke-administratörer (se dokumentationen) | Lösenordsadminitratör | Användaradministratör
Ogiltig verifiering av uppdateringstoken för privilegierade administratörer (se dokumentationen) | Global administratör | 
Läsa grundläggande konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Återställ lösen ord för begränsade administratörer (se dokumentationen) | Användaradministratör | 
Återställ lösen ord för icke-administratörer (se dokumentationen) | Lösenordsadminitratör | Användaradministratör
Återställ lösen ord för privilegierade administratörer | Global administratör | 
Återkalla licens | Licensadministratör | Användaradministratör
Uppdatera alla egenskaper utom användarens huvud namn | Användaradministratör | 
Uppdatera användarens huvud namn för begränsade administratörer (se dokumentationen) | Användaradministratör | 
Uppdatera egenskapen för användarens huvud namn i privilegierade administratörer (se dokumentationen) | Global administratör | 
Uppdatera användar inställningar | Global administratör | 


## <a name="support"></a>Support

Uppgift | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Skicka support ärende | Tjänstadministratör | Program administratör, Azure Information Protection administratör, fakturerings administratör, moln program administratör, kompatibilitets administratör, Dynamics 365-administratör, Desktop Analytics-administratör, Exchange-administratör, lösen ord Administratör, Intune-administratör, Skype för företag-administratör, Power BI administratör, Privileged Authentication Administrator, SharePoint-administratör, teams kommunikations administratör, team administratör, användar administratör, Administratör för arbets plats analys

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort administratörs roller för Azure AD](directory-manage-roles-portal.md)
* [Roll referens för Azure AD-administratörer](directory-assign-admin-roles.md)
