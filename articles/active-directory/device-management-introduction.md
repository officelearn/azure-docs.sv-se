---
title: Introduktion till hantering av enheter i Azure Active Directory | Microsoft Docs
description: Lär dig hur enhetshantering kan hjälpa dig att få kontroll över de enheter som har åtkomst till resurser i din miljö.
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
ms.openlocfilehash: 5d5ddd83a2335e7a08bac587aa627f1dcae9738c
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970908"
---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Introduktion till hantering av enheter i Azure Active Directory

I en mobil- och molnorienterade värld, Azure Active Directory (Azure AD) som möjliggör enkel inloggning till enheter, appar och tjänster från var som helst. Genom spridningen av enheter – inklusive Bring Your Own Device (BYOD), inför IT-proffs två helt motsatt mål:

- Ge slutanvändarna att vara produktiva oavsett tid och plats
- Skydda företagets resurser när som helst

Via enheter kan får användarna åtkomst till ditt företags tillgångar. För att skydda dina företagstillgångar som IT-administratör, som du vill ha kontroll över enheterna. På så sätt kan du se till att dina användare kommer åt dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. 

Hantering av enheter är också grunden för [enhetsbaserad villkorlig åtkomst](active-directory-conditional-access-policy-connected-applications.md). Med enhetsbaserad villkorlig åtkomst kan du se till att åtkomsten till resurser i din miljö är endast möjlig med hanterade enheter.   

Den här artikeln förklarar hur du enhetshantering i Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Enheter kontrolleras av Azure AD

Om du vill ha en enhet som kontrolleras av Azure AD, har du två alternativ:

- Registrera 
- Koppla

**Registrera** en enhet till Azure AD kan du hantera en enhetens identitet. När en enhet registreras ger Azure AD-enhetsregistrering enheten med en identitet som används för att autentisera enheten när en användare loggar in till Azure AD. Du kan använda identiteten för att aktivera eller inaktivera en enhet.

När de kombineras med en lösning för enhetsattributen av mobila enheter, till exempel Microsoft Intune, uppdateras enhetens egenskaper i Azure AD med ytterligare information om enheten. På så sätt kan du skapa regler för villkorlig åtkomst som säkerställer att åtkomsten från enheter uppfyller dina säkerhets- och efterlevnadskrav. Mer information om registrering av enheter i Microsoft Intune finns i registrera enheter för hantering i Intune.

**Koppla** en enhet är en utökning av registrering av en enhet. Det innebär att den ger dig alla fördelar med att registrera en enhet och utöver detta är också lokala tillståndet ändras för en enhet. Om du ändrar lokala tillståndet kan användarna att logga in på en enhet med ett organisations arbets- eller skolkonto i stället för ett personligt konto.

## <a name="azure-ad-registered-devices"></a>Azure AD-registrerade enheter   

Målet med Azure AD-registrerade enheter är att förse dig med stöd för den **Bring Your Own Device (BYOD)** scenario. I det här scenariot kan en användare åtkomst till organisationens Azure Active Directory kontrolleras resurser med hjälp av en personlig enhet.  

![Azure AD-registrerade enheter](./media/device-management-introduction/03.png)

Åtkomsten baseras på ett arbets- eller skolkonto konto som har angetts på enheten.  
Till exempel kan Windows 10 du lägga till ett arbets- eller skolkonto konto i en personlig dator, surfplatta eller telefon.  
När en användare har lagt till ett arbets eller skolkonto, enheten är registrerad med Azure AD och du kan också registreras i den mobilenheter (MDM) system som din organisation har konfigurerat. Användarna i organisationen kan lägga till ett arbets eller skolkonto för att en personlig enhet bekvämt:

- När du kommer åt ett program för arbete för första gången
- Manuellt via den **inställningar** menyn när det gäller Windows 10 

Du kan konfigurera Azure AD-registrerade enheter för Windows 10, iOS, Android och macOS.

## <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

Målet med Azure AD-anslutna enheter är att förenkla:

- Windows-distributioner av work-enheter 
- Åtkomst till organisationens appar och resurser från valfri Windows-enhet
- Molnbaserad hantering av enheter som ägs av arbetet

![Azure AD-registrerade enheter](./media/device-management-introduction/02.png)

Azure AD Join kan distribueras med hjälp av någon av följande metoder: 
 - [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Grupp-distribution](https://docs.microsoft.com/intune/windows-bulk-enroll)
 - [Självbetjäning](device-management-azuread-joined-devices-frx.md) 

**Azure AD Join** är avsedd för organisationer som vill att molnorienterade (det vill säga i första hand använder molntjänster, med målet att minska användningen av en lokal infrastruktur) eller enbart i molnet (ingen lokal infrastruktur). Det finns inga begränsningar för storlek eller typ av organisationer som kan distribueras med Azure AD Join. Azure AD-anslutning fungerar bra även i en hybridmiljö att aktivera åtkomst till både i molnet och lokala appar och resurser.

Implementera Azure AD-anslutna enheter ger dig följande fördelar:

- **Single-Sign-On (SSO)** till din Azure hanterade SaaS-appar och tjänster. Användarna se inte meddelanden för ytterligare autentisering vid åtkomst till arbetsresurser. SSO-funktioner är även när de inte är anslutna till domännätverk som är tillgängliga.

- **Kompatibla företagsnätverksväxling** användarinställningar på domänanslutna enheter. Användarna behöver inte att ansluta ett Microsoft-konto (till exempel Hotmail) om du vill se inställningar på enheter.

- **Åtkomst till Windows Store för företag** med hjälp av en Azure AD-konto. Användarna kan välja från en förteckning över program som förvalt av organisationen.

- **Windows Hello** stöd för säker och smidig åtkomst till arbetsresurser.

- **Begränsning av åtkomst** till appar från enheter som uppfyller policyn för efterlevnad.

- **Sömlös åtkomst till lokala resurser** när enheten har åtkomst till den lokala domänkontrollanten. 


Azure AD join är främst avsedd för organisationer som inte har en lokal Windows Server Active Directory-infrastruktur, men du kan däremot använda den i scenarier där:

- Du vill övergå till molnbaserad infrastruktur med hjälp av Azure AD och MDM som Intune.

- Du kan inte använda en lokal domänanslutning, till exempel om du vill hämta mobila enheter, till exempel surfplattor och telefoner under kontroll.

- Användarna behöver främst åtkomst till Office 365 eller andra SaaS-appar som integreras med Azure AD.

- Du vill hantera en grupp av användare i Azure AD i stället för i Active Directory. Detta kan till exempel gälla säsongsarbetare, leverantörer eller studenter.

- Vill du ange sammanbinder funktioner till arbetare i filialkontor kontor med begränsad lokal infrastruktur.

Du kan konfigurera Azure AD-anslutna enheter för Windows 10-enheter.


## <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

För mer än ett årtionde har många organisationer används domänanslutning till sina lokala Active Directory för att aktivera:

- IT-avdelningar möjlighet att hantera arbete ägda enheter från en central plats.

- Användare att logga in på sina enheter med sina Active Directory arbets- eller skolkonton. 

Normalt organisationer med en lokal fotavtryck förlitar sig på imaging metoder för att etablera enheter och de använder ofta **System Center Configuration Manager (SCCM)** eller **Grupprincip (GP)** att hantera dem.

Om din miljö har en lokal AD-fotavtryck och du även vill utnyttja funktionerna i Azure Active Directory, du kan implementera hybrid Azure AD-anslutna enheter. Enheter är enheter som är både, ansluten till din lokala Active Directory och Azure Active Directory.

![Azure AD-registrerade enheter](./media/device-management-introduction/01.png)


Du bör använda Azure AD-hybrid-anslutna enheter om:

- Du har Win32-appar som distribuerats till enheterna som förlitar sig på Active Directory-autentisering för datorn.

- Du behöver GP att hantera enheter.

- Vill du fortsätta att använda avbildning lösningar för att konfigurera enheter för dina anställda.

Du kan konfigurera Hybrid Azure AD-anslutna enheter för Windows 10 och äldre enheter, till exempel Windows 8 och Windows 7.

## <a name="summary"></a>Sammanfattning

Med enhetshantering i Azure AD kan du: 

- För att förenkla processen för att göra enheter kontrolleras av Azure AD

- Ge användarna en lättanvänd åtkomst till din organisations molnbaserade resurser

Som en regel för en använder du:

- Azure AD-registrerade enheter:

    - För personliga enheter 

    - Att manuellt registrera enheter med Azure AD

- Azure AD-anslutna enheter: 

    - För enheter som ägs av organisationen

    - För enheter som är **inte** ansluten till en lokal AD

    - Att manuellt registrera enheter med Azure AD

    - Att ändra lokala tillståndet för en enhet

- Azure AD-anslutna enheter för enheter som är anslutna till en lokal AD     

    - För enheter som ägs av organisationen

    - För enheter som är anslutna till en lokal AD

    - Att automatiskt registrera enheter med Azure AD

    - Att ändra lokala tillståndet för en enhet



## <a name="next-steps"></a>Nästa steg

- Om du vill få en översikt över hur du hanterar enheten i Azure-portalen, se [hantering av enheter med hjälp av Azure portal](device-management-azure-portal.md)

- Läs mer om enhetsbaserad villkorlig åtkomst i [konfigurera principer för Azure Active Directory-enhetsbaserad villkorlig åtkomst](active-directory-conditional-access-policy-connected-applications.md).

- Att konfigurera:
    - Azure Active Directory-registrerade Windows 10-enheter, se [så här konfigurerar du Azure Active Directory-registrerade Windows 10-enheter](device-management-azuread-registered-devices-windows10-setup.md)
    - Azure Active Directory-anslutna enheter, se [så här konfigurerar du Azure Active Directory-anslutna enheter](device-management-azuread-joined-devices-setup.md)
    - Hybrid Azure AD-anslutna enheter, se [hur du konfigurerar hybrid Azure Active Directory-anslutna enheter](device-management-hybrid-azuread-joined-devices-setup.md).


