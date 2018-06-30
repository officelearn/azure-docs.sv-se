---
title: Introduktion till hantering av enheter i Azure Active Directory | Microsoft Docs
description: Lär dig hur enhetshantering kan du få kontroll över de enheter som har åtkomst till resurser i din miljö.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 96de05eb8ebae85b73eaa012efdfb38ac074abf8
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110928"
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Introduktion till hantering av enheter i Azure Active Directory

I mobile första, molnet först världen aktiverar Azure Active Directory (AD Azure) enkel inloggning till enheter, appar och tjänster från var som helst. Med den ökande mängden av enheter – inklusive Bring Your Own Device (BYOD), inför IT-proffs två andra mål:

- Slutanvändare att vara produktiva var och när
- Skydda företagets tillgångar när som helst

Via enheter kan får användarna åtkomst till ditt företags tillgångar. För att skydda ditt företags tillgångar, som IT-administratör, som du vill ha kontroll över enheterna. På så sätt kan du se till att dina användare har åtkomst till dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. 

Enhetshantering också är grunden för [enhetsbaserad villkorlig åtkomst](active-directory-conditional-access-policy-connected-applications.md). Du kan kontrollera åtkomsten till resurser i din miljö är endast möjlig med hanterade enheter med enhetsbaserad villkorlig åtkomst.   

Den här artikeln förklarar hur enhetshantering i Azure Active Directory fungerar.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Hantera enheter under kontroll av Azure AD

För att få en enhet under kontroll av Azure AD, har du två alternativ:

- Registrera 
- Koppla

**Registrera** en enhet till Azure AD kan du hantera en enhet identitet. När en enhet är registrerad innehåller enhetsregistrering för Azure AD enheten med en identitet som används för att autentisera enheten när en användare loggar in till Azure AD. Du kan använda identiteten för att aktivera eller inaktivera en enhet.

När den kombineras med en enhetsattributen lösning för mobila enheter, till exempel Microsoft Intune, uppdateras attributen i Azure AD med ytterligare information om enheten. På så sätt kan du skapa regler för villkorlig åtkomst som säkerställer att åtkomsten från enheter uppfyller dina säkerhets- och efterlevnadskrav. Mer information om registrerar enheter i Microsoft Intune finns i registrera enheter för hantering i Intune.

**Koppla** en enhet är ett tillägg till registrerar en enhet. Det innebär den ger dig alla fördelar med att registrera en enhet och dessutom också lokala tillståndet ändras för en enhet. Ändra tillståndet lokala gör det möjligt för användarna att logga in på en enhet med ett organisations arbets- eller skolkonto i stället för ett personligt konto.

## <a name="azure-ad-registered-devices"></a>Azure AD som registrerade enheter   

Målet med Azure AD som registrerade enheter är att ge stöd för den **Bring Your Own Device (BYOD)** scenario. I det här scenariot kan en användare åtkomst till organisationens Azure Active Directory styrs resurser med hjälp av en personlig enhet.  

![Azure AD som registrerade enheter](./media/device-management-introduction/03.png)

Åtkomst baserat på ett arbets- eller skolkonto konto som har angetts på enheten.  
Till exempel kan Windows 10 du lägga till ett arbets- eller skolkonto konto till en dator, surfplatta eller telefon.  
När en användare har lagt till ett arbets- eller skolkonto, enheten är registrerad med Azure AD och du kan också registreras i mobila enheter (MDM) hanteringssystemet som din organisation har konfigurerats. Användare i organisationen kan lägga till en arbets- eller skolkonto till en personlig enhet lätt:

- Vid åtkomst till ett arbetsplats-program för första gången
- Manuellt via den **inställningar** menyn för Windows 10 

Du kan konfigurera Azure AD som registrerade enheter för Windows 10, iOS, Android och macOS.

## <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

Målet med Azure AD anslutna enheter är att förenkla:

- Windows-distributioner av enheter som ägs av arbetet 
- Åtkomst till organisationens appar och resurser från valfri windowsenhet
- Molnbaserad hantering av enheter som ägs av arbetet

![Azure AD som registrerade enheter](./media/device-management-introduction/02.png)

Azure AD-anslutning kan distribueras med hjälp av följande metoder: 
 - [Windows Autopilot](https://docs.microsoft.com/en-us/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Bulk-distribution](https://docs.microsoft.com/en-us/intune/windows-bulk-enroll)
 - [Självbetjäningsupplevelse](device-management-azuread-joined-devices-frx.md) 

**Azure AD-anslutning** är avsedd för organisationer som vill ha molnet först (som är i första hand använder molntjänster, med målet att minska användningen av en lokal infrastruktur) eller endast molnbaserad (ingen lokal infrastruktur). Det finns inga begränsningar för storlek eller typ av organisationer som kan distribuera Azure AD Join. Azure AD-anslutning fungerar bra även i en hybridmiljö aktiverar åtkomst till både till molnet och lokala appar och resurser.

Implementera Azure AD anslutna enheter ger dig följande fördelar:

- **Enkel inloggning (SSO)** till din Azure hanteras SaaS-appar och tjänster. Användarna ser anvisningarna för ytterligare autentisering vid åtkomst till arbetsresurser. SSO-funktioner är även när de inte är anslutna till domännätverk som är tillgängliga.

- **Enterprise kompatibla centrala** av användarinställningar för domänanslutna enheter. Användare behöver inte ansluta en Microsoft-konto (till exempel Hotmail) om du vill se inställningar över enheter.

- **Åtkomst till Windows Store för företag** med hjälp av Azure AD-kontot. Användarna kan välja från en inventering av program som är markerat i organisationen.

- **Windows Hello** stöd för säker och smidig tillgång till arbetsresurser.

- **Begränsning av åtkomst** för appar från enheter som uppfyller policy för efterlevnad.

- **Sömlös åtkomst till lokala resurser** när enheten har fri till den lokala domänkontrollanten. 


Azure AD-anslutning är främst avsedd för organisationer som inte har en lokal Windows Server Active Directory-infrastruktur, du kan visserligen använda den i scenarier där:

- Du vill att övergå till en molnbaserad infrastruktur med hjälp av Azure AD och MDM som Intune.

- Du kan inte använda en lokal domänanslutning till exempel om du behöver hämta mobila enheter som surfplattor och telefoner under kontroll.

- Användarna behöver främst åtkomst till Office 365 eller andra SaaS-appar som är integrerade med Azure AD.

- Du vill hantera en grupp användare i Azure AD i stället för i Active Directory. Detta kan till exempel gälla när anställda, leverantörer eller studenter.

- Vill du ange funktioner för anslutning till arbetare i fjärranslutna avdelningskontor med begränsad lokal infrastruktur.

Du kan konfigurera Azure AD anslutna enheter för Windows 10-enheter.


## <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

För mer än en tio åren har många organisationer används domänanslutning till deras lokala Active Directory för att aktivera:

- IT-avdelningar att hantera enheter som ägs av arbetet från en central plats.

- Användare att logga in på sina enheter med deras Active Directory arbets- eller skolkonton. 

Normalt organisationer med ett lokalt storleken är beroende av imaging metoder för att etablera enheter och de använder ofta **System Center Configuration Manager (SCCM)** eller **Grupprincip (GP)** att hantera dem.

Om din miljö har en lokal AD storleken och du även vill utnyttja funktionerna i Azure Active Directory, du kan implementera hybrid Azure AD anslutna enheter. Dessa är enheter som är både, ansluten till din lokala Active Directory och Azure Active Directory.

![Azure AD som registrerade enheter](./media/device-management-introduction/01.png)


Du bör använda Azure AD hybrid anslutna enheter om:

- Du har Win32-appar som distribueras till enheterna som förlitar sig på Active Directory-autentisering för datorn.

- Du behöver GP att hantera enheter.

- Vill du fortsätta att använda avbildningsverktyg lösningar för att konfigurera enheter för dina anställda.

Du kan konfigurera Hybrid Azure AD-anslutna enheter för Windows 10 och äldre enheter, till exempel Windows 8 och Windows 7.

## <a name="summary"></a>Sammanfattning

Med hantering av enheter i Azure AD kan du: 

- Förenkla processen med att enheter under kontrollen av Azure AD

- Ge användarna en lättanvänd åtkomst till organisationens molnbaserade resurser

Som regel i en USB bör du använda:

- Azure AD som registrerade enheter:

    - För personliga enheter 

    - Registrera enheter manuellt med Azure AD

- Azure AD-anslutna enheter: 

    - För enheter som ägs av din organisation

    - För enheter som är **inte** ansluten till en lokal AD

    - Registrera enheter manuellt med Azure AD

    - Att ändra lokala tillståndet för en enhet

- Hybrid Azure AD-anslutna enheter för enheter som är anslutna till en lokal AD     

    - För enheter som ägs av din organisation

    - För enheter som är anslutna till en lokal AD

    - Att automatiskt registrera enheter med Azure AD

    - Att ändra lokala tillståndet för en enhet



## <a name="next-steps"></a>Nästa steg

- Om du vill få en översikt över hur du hanterar enheten i Azure portal finns [hantera enheter med hjälp av Azure portal](device-management-azure-portal.md)

- Läs mer om enhetsbaserad villkorlig åtkomst i [konfigurera Azure Active Directory enhetsbaserad villkorliga åtkomstprinciper](active-directory-conditional-access-policy-connected-applications.md).

- Installationsprogrammet:
    - Azure Active Directory registrerade Windows 10-enheter, se [hur du konfigurerar Azure Active Directory registrerade Windows 10-enheter](device-management-azuread-registered-devices-windows10-setup.md)
    - Azure Active Directory-anslutna enheter, se [hur du konfigurerar Azure Active Directory-anslutna enheter](device-management-azuread-joined-devices-setup.md)
    - Hybrid Azure AD anslutna enheter, se [hur du konfigurerar hybrid Azure Active Directory-anslutna enheter](device-management-hybrid-azuread-joined-devices-setup.md).


