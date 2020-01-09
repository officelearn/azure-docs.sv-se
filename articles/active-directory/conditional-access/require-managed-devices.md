---
title: Villkorlig åtkomst kräver hanterad enhet – Azure Active Directory
description: Lär dig hur du konfigurerar Azure Active Directory (Azure AD) enhets principer för villkorlig åtkomst som kräver hanterade enheter för åtkomst till Cloud App.
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
ms.openlocfilehash: 6cd3a0341d9cf041155b09d41d8ff84d0b0cc3dc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424821"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Gör så här: Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst

I en mobil-och mellanliggande värld, kan Azure Active Directory (Azure AD) använda enkel inloggning till appar och tjänster var som helst. Behöriga användare kan komma åt dina molnappar från ett brett utbud av enheter, inklusive mobila och personliga enheter. Många miljöer har dock minst ett fåtal appar som endast ska kunna nås av enheter som uppfyller dina standarder för säkerhet och efterlevnad. Dessa enheter kallas även hanterade enheter. 

Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst som kräver att hanterade enheter får åtkomst till vissa molnappar i din miljö. 

## <a name="prerequisites"></a>Krav

Krav på hanterade enheter för åtkomst till molnbaserad **Azure AD-villkorlig åtkomst** och **Azure AD-enhets hantering** . Om du inte är bekant med något av dessa områden än bör du läsa följande avsnitt, först:

- **[Villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** – den här artikeln ger en konceptuell översikt över villkorlig åtkomst och den relaterade terminologin.
- **[Introduktion till enhets hantering i Azure Active Directory](../devices/overview.md)** – den här artikeln ger dig en översikt över de olika alternativen för att hämta enheter under organisations kontroll. 

## <a name="scenario-description"></a>Scenariobeskrivning

Det är en utmaning att hantera balansen mellan säkerhet och produktivitet. Spridningen av enheter som stöds för att komma åt moln resurser hjälper till att förbättra användarnas produktivitet. På sidan vänd vill du förmodligen inte att vissa resurser i din miljö ska kunna nås av enheter med en okänd skydds nivå. För de berörda resurserna bör du kräva att användarna bara kan komma åt dem med hjälp av en hanterad enhet. 

Med villkorlig åtkomst i Azure AD kan du hantera det här kravet med en enda princip som ger åtkomst:

- Till valda molnappar
- För valda användare och grupper
- Kräver en hanterad enhet

## <a name="managed-devices"></a>Hanterade enheter  

I enkla termer är hanterade enheter enheter som har *en viss typ* av organisations kontroll. I Azure AD är förutsättningen för en hanterad enhet att den har registrerats med Azure AD. När du registrerar en enhet skapas en identitet för enheten i form av ett enhets objekt. Det här objektet används av Azure för att spåra statusinformation om en enhet. Som Azure AD-administratör kan du redan använda det här objektet för att växla (aktivera/inaktivera) status för en enhet.
  
![Enhets baserade villkor](./media/require-managed-devices/32.png)

Om du vill hämta en enhet som är registrerad i Azure AD har du tre alternativ: 

- **Registrerade enheter för Azure AD** – så här hämtar du en personlig enhet som är registrerad i Azure AD
- **Azure AD-anslutna enheter** – för att få en organisations Windows 10-enhet som inte är ansluten till en lokal AD som är registrerad i Azure AD. 
- **Hybrid Azure AD-anslutna enheter** – för att få en Windows 10-enhet eller stöd för en äldre enhet som är ansluten till en lokal AD som är registrerad i Azure AD.

De här tre alternativen beskrivs i artikeln [Vad är en enhets identitet?](../devices/overview.md)

För att bli en hanterad enhet måste en registrerad enhet antingen vara en **hybrid Azure AD-ansluten enhet** eller en **enhet som har marker ATS som kompatibel**.  

![Enhets baserade villkor](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Kräv hybrid Azure AD-anslutna enheter

I din princip för villkorlig åtkomst kan du välja **Kräv att hybrid Azure AD-ansluten enhet** ska ange att de valda molnappar bara kan nås med en hanterad enhet. 

![Enhets baserade villkor](./media/require-managed-devices/10.png)

Den här inställningen gäller endast för Windows 10-enheter eller äldre enheter, till exempel Windows 7 eller Windows 8 som är anslutna till en lokal AD. Du kan bara registrera dessa enheter med Azure AD med en hybrid Azure AD-anslutning, som är en [automatiserad process](../devices/hybrid-azuread-join-plan.md) för att få en Windows 10-enhet registrerad. 

![Enhets baserade villkor](./media/require-managed-devices/45.png)

Vad gör en hybrid Azure AD-ansluten enhet till en hanterad enhet?  För enheter som är anslutna till en lokal AD antas det att kontrollen över dessa enheter upprätthålls med hjälp av hanterings lösningar som **System Center Configuration Manager (SCCM)** eller **grup princip (GP)** för att hantera dem. Eftersom det inte finns någon metod för Azure AD för att avgöra om någon av dessa metoder har tillämpats på en enhet, är en hybrid Azure AD-ansluten enhet en relativt svag mekanism för att kräva en hanterad enhet. Det är upp till dig som administratör att bedöma om de metoder som tillämpas på dina lokala domänanslutna enheter är tillräckligt starka för att utgöra en hanterad enhet om en sådan enhet också är en hybrid Azure AD-ansluten enhet.

## <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten ska markeras som kompatibel

Alternativet att *kräva att en enhet markeras som kompatibel* är den starkaste formen för att begära en hanterad enhet.

![Enhets baserade villkor](./media/require-managed-devices/11.png)

Det här alternativet kräver att en enhet registreras med Azure AD och också markeras som kompatibel av:
         
- Intune
- Ett MDM-system (Mobile Device Management) från tredje part som hanterar Windows 10-enheter via Azure AD-integrering. MDM-system från tredje part för andra typer av enhets operativ system än Windows 10 stöds inte.
 
![Enhets baserade villkor](./media/require-managed-devices/46.png)

För en enhet som har marker ATS som kompatibel kan du anta att: 

- De mobila enheter som din personal använder för att komma åt företags data hanteras
- Mobila appar som används av din personal hanteras
- Företagets information skyddas genom att hjälpa till att kontrol lera hur din personal får åtkomst till och delar den
- Enheten och dess appar är kompatibla med företagets säkerhets krav

### <a name="known-behavior"></a>Känt beteende

På Windows 7, iOS, Android, macOS och vissa tredjeparts webbläsare i Azure AD identifierar enheten med ett klient certifikat som är etablerad när enheten har registrerats med Azure AD. När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Slutanvändaren måste välja det här certifikatet innan de kan fortsätta att använda webbläsaren.

## <a name="next-steps"></a>Nästa steg

Innan du konfigurerar en enhets beroende princip för villkorlig åtkomst i din miljö bör du ta en titt på [metod tipsen för villkorlig åtkomst i Azure Active Directory](best-practices.md).
