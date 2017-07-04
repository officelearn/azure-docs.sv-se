---
title: "Översikt över enhetsregistrering i Azure Active Directory | Microsoft Docs"
description: "Enhetsregistrering i Azure Active Directory är grunden för scenarier med enhetsbaserad villkorlig åtkomst. När en enhet registreras tilldelar Azure Active Directory Device Registration en identitet till enheten som används för att autentisera enheten när användaren loggar in."
services: active-directory
keywords: enhetsregistrering, aktivera enhetsregistrering, enhetsregistrering och MDM
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 1e92c1a2-01b8-4225-950b-373cd601b035
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/23/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: d19956e4964f57251f51eb8ffe5041c6a49da1a7
ms.contentlocale: sv-se
ms.lasthandoff: 03/09/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Komma igång med enhetsregistrering i Azure Active Directory
Enhetsregistrering i Azure Active Directory är grunden för scenarier med enhetsbaserad villkorlig åtkomst. När en enhet registreras ger Azure Active Directory Device Registration den en identitet som används för att autentisera enheten när användaren loggar in. Den autentiserade enheten och attributen för enheten kan sedan användas för att genomdriva principer för villkorlig åtkomst för program som finns i molnet och lokalt.

När de kombineras med en MDM-lösning för hantering av mobila enheter, t.ex. Microsoft Intune, uppdateras enhetsattributen i Azure Active Directory med ytterligare information om enheten. På så sätt kan du skapa regler för villkorlig åtkomst som säkerställer att åtkomsten från enheter uppfyller dina säkerhets- och efterlevnadskrav. Mer information om hur du registrerar enheter i Microsoft Intune finns i [Registrera enheter för hantering i Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Scenarier som använder enhetsregistrering i Azure Active Directory
Enhetsregistrering i Azure Active Directory har stöd för iOS-, Android- och Windows-enheter. De enskilda scenarierna som använder enhetsregistrering i Azure AD kan ha mer specifika krav och plattformsstöd. Dessa scenarier är följande:

* **Villkorlig åtkomst till program som finns lokalt**: Du kan använda registrerade enheter med åtkomstprinciper för program som är konfigurerade att använda AD FS i Windows Server 2012 R2. Mer information om hur du konfigurerar lokal villkorlig åtkomst finns i [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](active-directory-device-registration-on-premises-setup.md).
* **Villkorlig åtkomst för Office 365-program med Microsoft Intune**: IT-administratörer kan etablera enhetsprinciper för villkorlig åtkomst för att skydda företagsresurser och samtidigt ge informationsarbetare på kompatibla enheter åtkomst till tjänsterna. Mer information finns i [Enhetsprinciper för villkorlig åtkomst för Office 365-tjänster](active-directory-conditional-access-device-policies.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Konfigurera enhetsregistrering i Azure Active Directory
Du måste aktivera enhetsregistrering i Azure AD på Azure Portal så att mobila enheter kan identifiera tjänsten genom att söka efter välkända DNS-poster. Du måste konfigurera företagets DNS så att Windows 10-, Windows 8.1-, Windows 7-, Android- och iOS-enheter kan identifiera och använda tjänsten.
Du kan visa och aktivera/inaktivera registrerade enheter med hjälp av administratörsportalen i Azure Active Directory.

> [!NOTE]
> De senaste instruktionerna för hur du konfigurerar automatisk enhetsregistrering finns i [How to setup automatic registration of Windows domain joined devices with Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md) (Så här konfigurerar du automatisk registrering av domänanslutna Windows-enheter med Azure Active Directory).
> 
> 

### <a name="enable-azure-active-directory-device-registration-service"></a>Aktivera enhetsregistreringstjänsten i Azure Active Directory
1. Logga in på Microsoft Azure-portalen som administratör.
2. Välj **Active Directory** i det vänstra fönstret.
3. Välj din katalog på fliken **Katalog**.
4. Välj fliken **Konfigurera**.
5. Bläddra till avsnittet **Enheter**.
6. Välj **Alla** för **Användare kan ansluta enheter till Azure AD**.
7. Välj det högsta antalet enheter som du vill tillåta per användare.

> [!NOTE]
> Registrering med Microsoft Intune eller Mobile Device Management för Office 365 kräver Workplace Join. Om du har konfigurerat någon av dessa tjänster är Alla valt och Inga är inaktiverat.
> 
> 

Tvåfaktorsautentisering är inte aktiverat för tjänsten som standard. Tvåfaktorsautentisering rekommenderas dock när du registrerar en enhet.

* Innan du kräver tvåfaktorsautentisering för den här tjänsten måste du konfigurera en provider för tvåfaktorsautentisering i Azure Active Directory och konfigurera dina användarkonton för Multi-Factor Authentication. Mer information finns i [Lägga till Multi-Factor Authentication i Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
* Om du använder AD FS med Windows Server 2012 R2 måste du konfigurera en modul för tvåfaktorsautentisering i AD FS. Mer information finns i [Använda Multi-Factor Authentication med Active Directory Federation Services](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Konfigurera identifiering av enhetsregistrering i Azure Active Directory
Windows 7- och Windows 8.1-enheter identifierar enhetsregistreringstjänsten genom att kombinera användarens kontonamn med ett välkänt servernamn för enhetsregistrering.

Du måste skapa en DNS CNAME-post som pekar på A-posten som associeras med enhetsregistreringstjänsten för Azure Active Directory. CNAME-posten måste använda det välkända prefixet enterpriseregistration följt av UPN-suffixet som används av användarkonton i din organisation. Om din organisation använder flera UPN-suffix måste flera CNAME-poster skapas i DNS.

Om du använder två UPN-suffix i din organisation som heter @contoso.com och @region.contoso.com skapar du följande DNS-poster.

| Post | Typ | Adress |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Visa och hantera enhetsobjekt i Azure Active Directory
1. Från Azure-administratörsportalen kan du visa, blockera och avblockera enheter. En enhet som är blockerad har inte längre åtkomst till program som är konfigurerade att endast tillåta registrerade enheter.
2. Logga in på Microsoft Azure Portal som administratör.
3. Välj **Active Directory** i det vänstra fönstret.
4. Välj din katalog.
5. Välj fliken **Användare**. Välj sedan en användare för att visa dennes enheter
6. Välj fliken **Enheter**.
7. Välj **Registrerade enheter** i listrutan.
8. Här kan du visa, blockera och avblockera användarregistrerade enheter.

## <a name="additional-topics"></a>Ytterligare information
Du kan registrera Windows 7- och Windows 8.1-domänanslutna enheter med enhetsregistrering i Azure AD. Följande avsnitt innehåller mer information om kraven och anvisningarna för att konfigurera enhetsregistrering för Windows 7- och Windows 8.1-enheter.

* [Automatisk enhetsregistrering med Azure Active Directory för domänanslutna Windows-enheter](active-directory-conditional-access-automatic-device-registration.md)
* [Automatisk enhetsregistrering med Azure Active Directory för domänanslutna Windows 10-enheter](active-directory-azureadjoin-devices-group-policy.md)


