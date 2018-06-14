---
title: Användningsscenarier och överväganden vid distribution för Azure AD Join | Microsoft Docs
description: Förklarar hur administratörer kan konfigurera Azure AD Join för slutanvändarna (anställda, studenter, andra användare). Här beskrivs också olika verkliga scenarier för att använda Azure AD Join.
services: active-directory
documentationcenter: ''
author: femila
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 173ad6f07699ca6bfa534dedc053663bba571382
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26602438"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Användningsscenarier och överväganden vid distribution för Azure AD Join
## <a name="usage-scenarios-for-azure-ad-join"></a>Användningsscenarier för Azure AD Join
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scenario 1: Företag i stort sett i molnet
Azure Active Directory Join (Azure AD Join) du kan ha nytta om du för närvarande fungerar och hantera identiteter för ditt företag i molnet eller flytt till molnet snart. Du kan använda ett konto som du har skapat i Azure AD för att logga in på Windows 10. Via [första gången du kör uppstå (FRX) processen](active-directory-azureadjoin-user-frx.md), eller genom att ansluta till Azure AD från [inställningsmenyn](active-directory-azureadjoin-user-upgrade.md), kan användarna ansluta sina datorer till Azure AD.  Användarna kan också få enkel inloggning (SSO) åtkomst till molnresurser som Office 365 i webbläsaren eller i Office-program.

### <a name="scenario-2-educational-institutions"></a>Scenario 2: Utbildningssyfte
Utbildningssyfte har vanligtvis två användartyper: lärare och övrig personal och studenter. Fakultetsmedlemmar anses långsiktig medlemmar i organisationen. Skapa lokala konton för dem. är önskvärt. Men studenter shorter-term som ingår i organisationen och sina konton kan hanteras i Azure AD. Det innebär att directory skala kan skickas till molnet i stället för att lagras lokalt. Det innebär också att studenter som kommer att kunna logga in till Windows med sina Azure AD-konton och få åtkomst till Office 365-resurser i Office-program.

### <a name="scenario-3-retail-businesses"></a>Scenario 3: Retail företag
Retail företag har säsongsbaserade arbetare och långsiktig anställda. Du vanligtvis skapa lokala konton och använda domänanslutna datorer för långsiktig heltidsanställda. Men när anställda är shorter-term medlemmar i organisationen och det är klokt att hantera sina konton där användarlicenser kan enkelt flytta runt. När du skapar sina konton i molnet med Office 365 licenser kan få dessa användare fördelarna med att logga in på Windows och Office-program med Azure AD-kontot, samtidigt som du behåller mer flexibelt med licenser när de lämnar.

### <a name="scenario-4-additional-scenarios"></a>Scenario 4: Fler scenarier
Tillsammans med de fördelar som tidigare diskuterats, dra nytta av att användarna ansluta sina enheter till Azure AD på grund av en förenklad anslutande upplevelse, effektiv enhetshantering, automatisk hantering av mobilenhetsregistrering och enkel inloggning till Azure AD och lokala resurser.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Överväganden vid distribution för Azure AD Join
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Ge användarna att ansluta till en företagsägd enhet direkt till Azure AD
Företag kan tillhandahålla molnbaserad konton till partnerföretag och organisationer. Dessa partners kan sedan enkelt komma åt företagets appar och resurser med enkel inloggning. Det här scenariot gäller för användare som har åtkomst till resurser i första hand i molnet, till exempel Office 365 eller SaaS-appar som förlitar sig på Azure AD för autentisering.

### <a name="prerequisites"></a>Krav
**På företagsnivå (administratör)**

* Azure-prenumeration med Azure Active Directory  

**På användarnivå**

* Windows 10 (Professional och Enterprise Edition)

### <a name="administrator-tasks"></a>Administratörsåtgärder
* [Konfigurera enhetsregistrering](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Användaruppgifter
* [Skapa en ny Windows 10-enhet med Azure AD under installationen](active-directory-azureadjoin-user-frx.md)
* [Konfigurera en Windows 10-enhet med Azure AD från inställningsmenyn](active-directory-azureadjoin-user-upgrade.md)
* [Ansluta en egen Windows 10-enhet till din organisation](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Aktivera BYOD i din organisation för Windows 10
Du kan konfigurera användare och anställda använda sina personliga Windows-enheter (BYOD) för att få åtkomst till företagets appar och resurser. Användarna kan lägga till sina Azure AD-konton (arbets- eller skolkonto konton) till en personlig windowsenhet för att komma åt resurser på ett sätt som skyddade och kompatibla.

### <a name="prerequisites"></a>Krav
**På företagsnivå (administratör)**

* Azure AD-prenumeration

**På användarnivå**

* Windows 10 (Professional och Enterprise Edition)

### <a name="administrator-tasks"></a>Administratörsåtgärder
* [Konfigurera enhetsregistrering](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Användaruppgifter
* [Ansluta en egen Windows 10-enhet till din organisation](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Ytterligare information
* [Windows 10 för företaget: Sätt att använda enheter för arbete](active-directory-azureadjoin-windows10-devices-overview.md)
* [Utöka molnkapaciteten till Windows 10-enheter via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autentisera identiteter utan lösenord via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Läs mer om användningsscenarier för Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ansluta domänanslutna enheter till miljöer med Azure AD och Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurera Azure AD Join](active-directory-azureadjoin-setup.md)

