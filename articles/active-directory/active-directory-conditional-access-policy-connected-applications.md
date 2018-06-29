---
title: Hur ska - kräver hanterade enheter för åtkomst till appen moln med Azure Active Directory för villkorlig åtkomst | Microsoft Docs
description: Lär dig hur du konfigurerar Azure Active Directory (AD Azure) enhetsbaserad villkorliga åtkomstprinciper som kräver hanterade enheter för åtkomst till appen i molnet.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 066d25e8953a2be4bd64cdd1af79b7f2a25dd5f9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036360"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Så här: Kräver hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst

I mobile första, molnet först världen aktiverar Azure Active Directory (AD Azure) enkel inloggning till appar och tjänster från var som helst. Auktoriserade användare kan komma åt dina molnappar från en mängd olika enheter, inklusive mobila och personliga enheter. Men har många miljöer minst några appar som bara kan användas av enheter som uppfyller dina krav för säkerhet och efterlevnad. Dessa enheter kallas även för hanterade enheter. 

Den här artikeln förklarar hur du kan konfigurera villkorliga åtkomstprinciper som kräver hanterade enheter får åtkomst till vissa molnappar i din miljö. 


## <a name="prerequisites"></a>Förutsättningar

Krav på hanterade enheter för cloud app åtkomst ties **villkorlig åtkomst i Azure AD** och **Azure AD enhetshantering** tillsammans. Om du inte är bekant med något av dessa områden ännu, bör du först läsa i följande avsnitt:

- **[Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  -den här artikeln ger en översikt över relaterade terminologi och villkorlig åtkomst.

- **[Introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)**  -den här artikeln innehåller en översikt över de olika alternativ som du behöver hämta enheter under organisationens kontroll. 


## <a name="scenario-description"></a>Scenariobeskrivning

Hantering av balansen mellan säkerhet och produktivitet är en utmaning. Den ökande mängden av enheter som stöds till molnresurser hjälper oss för att förbättra användarnas produktivitet. På sidan Vänd vill du förmodligen inte vissa resurser i miljön som kan nås av enheter med en okänd skyddsnivå. Du bör kräva att användare kan bara komma åt dem med hjälp av en hanterad enhet för de berörda resurserna. 

Du kan åtgärda det här kravet med en enda princip som beviljar åtkomst med Azure AD villkorlig åtkomst:

- Till valda molnappar

- För valda användare och grupper

- Kräver en hanterad enhet


## <a name="managed-devices"></a>Hanterade enheter  

Enkelt uttryckt hanterade enheter är enheter som är *vissa sortera* för organisationens kontroll. I Azure AD är nödvändiga för en hanterad enhet har registrerats med Azure AD. Registrerar en enhet skapas en identitet för enheten i form av ett enhetsobjekt. Det här objektet används av Azure för att spåra statusinformation om en enhet. Som en Azure AD-administratör kan du redan använder det här objektet ska växla (aktivera/inaktivera) tillstånd för en enhet.
  
![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/32.png)

För att få en enhet som registrerats med Azure AD, finns det tre alternativ:

- **[Azure AD registrerade enheter](device-management-introduction.md#azure-ad-registered-devices)**  - om du vill hämta en personlig enhet som registrerats med Azure AD

- **[Azure AD-anslutna enheter](device-management-introduction.md#azure-ad-joined-devices)**  - om du vill hämta en organisations Windows 10-enhet som inte är ansluten till en lokal AD registrerad med Azure AD. 

- **[Hybrid Azure AD-anslutna enheter](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  - om du vill hämta en Windows 10-enhet som är ansluten till en lokal AD registrerad med Azure AD.

Om du vill bli en hanterad enhet, en registrerad enhet måste vara antingen en **Hybrid Azure AD-ansluten enhet** eller en **enhet som har markerats som kompatibel**.  

![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Kräver Hybrid Azure AD-anslutna enheter

Du kan välja i principen för villkorlig åtkomst **kräver Azure AD-hybridlösning enhet** till att de valda molnapparna kan bara användas med en hanterad enhet. 

![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Den här inställningen gäller endast Windows 10-enheter som är anslutna till en lokal AD. Du kan bara registrera dessa enheter med Azure AD med hjälp av en Hybrid Azure AD-koppling som är en [automatiserad process](device-management-hybrid-azuread-joined-devices-setup.md) får Windows 10-enheten registrerad. 

![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Vad gör en Azure AD-hybridlösning ansluten enhet till en hanterad enhet?  För enheter som är anslutna till en lokal AD, förutsätts att kontroll över enheterna tillämpas med hanteringslösningar, till exempel **System Center Configuration Manager (SCCM)** eller **Grupprincip (GP)** ska hanteras. Eftersom det finns ingen metod för Azure AD för att avgöra om någon av dessa metoder har kopplats till en enhet, kräver en Azure AD-hybridlösning enhet är en relativt svag mekanism för att kräva en hanterad enhet. Det är du som administratör för att bedöma om de metoder som tillämpas på din lokala domänanslutna enheter är starkt att utgöra en hanterad enhet, om sådana en enhet är också en Azure AD-hybridlösning enhet.


## <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten är markerad som kompatibel

Alternativet att *kräver en enhet har markerats som kompatibel* är den starkaste formen att begära en hanterad enhet.

![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Det här alternativet kräver att en enhet registreras med Azure AD och även för att markeras som är kompatibla med:
         
- Intune.
- En tredjeparts mobila enheter (MDM) hanteringssystem som hanterar Windows 10-enheter via integrering med Azure AD. Tredje parts MDM-system för enhetstyper operativsystem än Windows 10 stöds inte.
 
![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/46.png)



För en enhet som har markerats som kompatibelt kan anta du att: 

- Mobila enheter som personalen använder för att komma åt företagets data ska hanteras
- Mobila appar som använder din arbetsstyrka hanteras
- Företagsinformationen skyddas genom att styra hur personalen åtkomst och delar det
- Enheten och dess program är kompatibla med företagets säkerhetskrav




## <a name="next-steps"></a>Nästa steg

Innan du konfigurerar en princip för enhetsbaserad villkorlig åtkomst i din miljö bör du ta en titt på den [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md).

