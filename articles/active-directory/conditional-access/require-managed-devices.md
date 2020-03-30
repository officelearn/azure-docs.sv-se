---
title: Villkorlig åtkomst kräver hanterad enhet - Azure Active Directory
description: Lär dig hur du konfigurerar Azure Active Directory (Azure AD) enhetsbaserade principer för villkorlig åtkomst som kräver hanterade enheter för åtkomst till molnappar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481491"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Så här: Kräv hanterade enheter för molnappåtkomst med villkorlig åtkomst

I en första dator med första molnet möjliggör Azure Active Directory (Azure AD) enkel inloggning till appar och tjänster var som helst. Behöriga användare kan komma åt dina molnappar från ett brett spektrum av enheter, inklusive mobila och även personliga enheter. Många miljöer har dock åtminstone ett fåtal appar som endast bör nås av enheter som uppfyller dina standarder för säkerhet och efterlevnad. Dessa enheter kallas även hanterade enheter. 

I den här artikeln beskrivs hur du kan konfigurera principer för villkorlig åtkomst som kräver hanterade enheter för att komma åt vissa molnappar i din miljö. 

## <a name="prerequisites"></a>Krav

Kräver hanterade enheter för moln app åtkomst band **Azure AD Villkorlig åtkomst** och Azure AD **enhetshantering** tillsammans. Om du inte är bekant med något av dessa områden ännu bör du läsa följande ämnen, först:

- **[Villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** - Den här artikeln ger dig en begreppsmässig översikt över villkorlig åtkomst och tillhörande terminologi.
- **[Introduktion till enhetshantering i Azure Active Directory](../devices/overview.md)** – Den här artikeln ger dig en översikt över de olika alternativ du har för att få enheter under organisationskontroll. 
- För Chrome-stöd i **Windows 10 Creators Update (version 1703)** eller senare installerar du [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Det här tillägget krävs när en princip för villkorlig åtkomst kräver enhetsspecifik information.

>[!NOTE] 
> Vi rekommenderar att du använder Azure AD-enhetsbaserad policy för villkorlig åtkomst för att få bästa möjliga efterlevnad efter inledande enhetsautentisering. Detta inkluderar att stänga sessioner om enheten faller utanför kompatibilitet och enhetskodflöde.


## <a name="scenario-description"></a>Scenariobeskrivning

Att bemästra balansen mellan säkerhet och produktivitet är en utmaning. Spridningen av enheter som stöds för att komma åt dina molnresurser bidrar till att förbättra användarnas produktivitet. På baksidan vill du förmodligen inte att vissa resurser i din miljö ska nås av enheter med en okänd skyddsnivå. För de berörda resurserna bör du kräva att användarna bara kan komma åt dem med hjälp av en hanterad enhet. 

Med Azure AD-villkorlig åtkomst kan du åtgärda det här kravet med en enda princip som ger åtkomst:

- Till valda molnappar
- För valda användare och grupper
- Kräver en hanterad enhet

## <a name="managed-devices"></a>Hanterade enheter  

Enkelt uttryckt är hanterade enheter enheter som är under *någon form* av organisatorisk kontroll. I Azure AD är förutsättningen för en hanterad enhet att den har registrerats med Azure AD. Om du registrerar en enhet skapas en identitet för enheten i form av ett enhetsobjekt. Det här objektet används av Azure för att spåra statusinformation om en enhet. Som Azure AD-administratör kan du redan använda det här objektet för att växla (aktivera/inaktivera) tillståndet för en enhet.
  
![Enhetsbaserade villkor](./media/require-managed-devices/32.png)

Om du vill få en enhet registrerad med Azure AD har du tre alternativ: 

- **Azure AD-registrerade enheter** – för att få en personlig enhet registrerad med Azure AD
- **Azure AD-anslutna enheter** – för att hämta en organisationell Windows 10-enhet som inte är ansluten till en lokal AD som är registrerad med Azure AD. 
- **Hybrid Azure AD-anslutna enheter** – för att få en Windows 10- eller ned-nivå-enhet som stöds till en lokal AD som är registrerad med Azure AD.

Dessa tre alternativ diskuteras i artikeln [Vad är en enhetsidentitet?](../devices/overview.md)

För att bli en hanterad enhet måste en registrerad enhet vara antingen en **Hybrid Azure AD-ansluten enhet** eller en enhet som har **markerats som kompatibel**.  

![Enhetsbaserade villkor](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Kräv Hybrid Azure AD-anslutna enheter

I principen villkorlig åtkomst kan du välja **Kräv Hybrid Azure AD-ansluten enhet** för att ange att de valda molnapparna endast kan nås med en hanterad enhet. 

![Enhetsbaserade villkor](./media/require-managed-devices/10.png)

Den här inställningen gäller endast för Windows 10- eller ned-nivå-enheter som Windows 7 eller Windows 8 som är anslutna till en lokal AD. Du kan bara registrera dessa enheter med Azure AD med hjälp av en Hybrid Azure AD-koppling, vilket är en [automatiserad process](../devices/hybrid-azuread-join-plan.md) för att få en Windows 10-enhet registrerad. 

![Enhetsbaserade villkor](./media/require-managed-devices/45.png)

Vad gör en Hybrid Azure AD-ansluten enhet till en hanterad enhet?  För enheter som är anslutna till en lokal AD antas kontrollen över dessa enheter med hjälp av hanteringslösningar som **Configuration Manager** eller **grupprincip (GP)** för att hantera dem. Eftersom det inte finns någon metod för Azure AD för att avgöra om någon av dessa metoder har tillämpats på en enhet, är det en relativt svag mekanism som kräver en hanterad enhet att kräva en hanterad enhet. Det är upp till dig som administratör att bedöma om de metoder som tillämpas på dina lokala domänanslutna enheter är tillräckligt starka för att utgöra en hanterad enhet om en sådan enhet också är en Hybrid Azure AD-ansluten enhet.

## <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten ska markeras som kompatibel

Alternativet att *kräva att en enhet ska markeras som kompatibel* är det starkaste formuläret för att begära en hanterad enhet.

![Enhetsbaserade villkor](./media/require-managed-devices/11.png)

Det här alternativet kräver att en enhet registreras med Azure AD och även markeras som kompatibel av:
         
- Intune
- Ett MDM-system (Mobile Device Management) från tredje part som hanterar Windows 10-enheter via Azure AD-integrering. Mdm-system från tredje part för andra enhetstyper än Windows 10 stöds inte.
 
![Enhetsbaserade villkor](./media/require-managed-devices/46.png)

För en enhet som är markerad som kompatibel kan du anta att: 

- De mobila enheter som personalen använder för att komma åt företagsdata hanteras
- Mobilappar som personalen använder hanteras
- Din företagsinformation skyddas genom att hjälpa till att kontrollera hur din personal får åtkomst till och delar den
- Enheten och dess appar är kompatibla med företagets säkerhetskrav

### <a name="known-behavior"></a>Känt beteende

På Windows 7 identifierar iOS, Android, macOS och vissa webbläsare från tredje part Azure AD enheten med hjälp av ett klientcertifikat som etableras när enheten är registrerad med Azure AD. När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Slutanvändaren måste välja det här certifikatet innan han eller hon kan fortsätta att använda webbläsaren.

## <a name="next-steps"></a>Nästa steg

Innan du konfigurerar en enhetsbaserad princip för villkorlig åtkomst i din miljö bör du ta en titt på [metodtipsen för villkorlig åtkomst i Azure Active Directory](best-practices.md).
