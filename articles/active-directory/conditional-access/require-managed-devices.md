---
title: Hur – kräver hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar principer för Azure Active Directory (Azure AD) enhetsbaserad villkorlig åtkomst som kräver hanterade enheter för åtkomst till appen i molnet.
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
ms.openlocfilehash: 7bcfb8d23b9f92db8c5ccae87fceef5fa96ed457
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601999"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Så här: Kräv hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst

I en mobil- och molnorienterade värld, Azure Active Directory (Azure AD) som möjliggör enkel inloggning till appar och tjänster från var som helst. Auktoriserade användare kan komma åt dina appar i molnet från en mängd olika enheter, inklusive mobila och personliga enheter. Men har många miljöer minst några appar som ska bara användas av enheter som uppfyller dina krav för säkerhet och efterlevnad. Dessa enheter är även känd som hanterade enheter. 

Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst som kräver hanterade enheter får åtkomst till vissa molnappar i din miljö. 


## <a name="prerequisites"></a>Förutsättningar

Krav på hanterade enheter för cloud app åtkomst ties **Azure AD villkorsstyrd åtkomst** och **Azure AD-enhetshantering** tillsammans. Om du inte är bekant med någon av dessa områden ännu, bör du läsa följande avsnitt kommer först:

- **[Villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md)**  -den här artikeln ger en översikt över villkorlig åtkomst och termer som är relaterade.

- **[Introduktion till hantering av enheter i Azure Active Directory](../devices/overview.md)**  -den här artikeln får du en överblick över de olika alternativ som du behöver hämta enheter organisationens kontrolleras. 


## <a name="scenario-description"></a>Scenariobeskrivning

Mastering balans mellan säkerhet och produktivitet är en utmaning. Det hjälper dig för att förbättra användarnas produktivitet ökningen av enheter som stöds till dina molnresurser. Å andra sidan vill du förmodligen inte vissa resurser i din miljö för att användas av enheter med en okänd skyddsnivån. För de berörda resurserna bör du kräva att användare kan bara komma åt dem med hjälp av en hanterad enhet. 

Med Azure AD villkorlig åtkomst, kan du lösa det här kravet med en enda princip som tilldelar åtkomst:

- Till valda molnappar

- För valda användare och grupper

- Kräver en hanterad enhet


## <a name="managed-devices"></a>Hanterade enheter  

Enkelt uttryckt hanterade enheter är enheter som är under *någon typ* för organisationens kontroll. I Azure AD är de nödvändiga komponenterna för en hanterad enhet har registrerats med Azure AD. Registrera en enhet skapas en identitet för enheten i form av ett enhetsobjekt. Det här objektet används av Azure för att spåra statusinformation om en enhet. Som Azure AD-administratör kan använder du redan det här objektet ska visa/dölj (aktivera/inaktivera) tillståndet för en enhet.
  
![Enhetsbaserad villkor](./media/require-managed-devices/32.png)

Om du vill ha en enhet har registrerats med Azure AD har du tre alternativ:

- **[Azure AD-registrerade enheter](../devices/overview.md#azure-ad-registered-devices)**  – om du vill hämta en personlig enhet registrerad med Azure AD

- **[Azure AD-anslutna enheter](../devices/overview.md#azure-ad-joined-devices)**  – om du vill hämta en organisations Windows 10-enhet som inte är ansluten till en lokal AD-registrerade med Azure AD. 

- **[Azure AD-anslutna hybridenheter](../devices/overview.md#hybrid-azure-ad-joined-devices)**  – om du vill hämta en Windows 10 eller stöd äldre enheter som är ansluten till en lokal AD-registrerade med Azure AD.

Om du vill bli en hanterad enhet, en registrerad enhet måste vara antingen en **Hybrid Azure AD-ansluten enhet** eller en **enhet som har markerats som kompatibel**.  

![Enhetsbaserad villkor](./media/require-managed-devices/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Kräv Hybrid Azure AD-anslutna enheter

I princip för villkorlig åtkomst kan du välja **Kräv Hybrid Azure AD-domänansluten enhet** till att de valda molnapparna kan bara användas med en hanterad enhet. 

![Enhetsbaserad villkor](./media/require-managed-devices/10.png)

Den här inställningen gäller endast Windows 10 eller äldre enheter, till exempel Windows 7 eller Windows 8 som är anslutna till en lokal AD. Du kan bara registrera enheterna med Azure AD med en Hybrid Azure AD-anslutning, vilket är en [automatiserad process](../devices/hybrid-azuread-join-plan.md) att hämta en Windows 10-enhet som har registrerats. 

![Enhetsbaserad villkor](./media/require-managed-devices/45.png)

Det som gör en Hybrid Azure AD ansluten enhet till en hanterad enhet?  För enheter som är anslutna till en lokal AD, förutsätts att kontrollen över enheterna tillämpas med hanteringslösningar som **System Center Configuration Manager (SCCM)** eller **Grupprincip (GP)** ska hanteras. Eftersom det finns ingen metod för Azure AD för att avgöra om någon av dessa metoder har kopplats till en enhet, kräver en hybrid Azure AD enhet är en relativt svag mekanism för att kräva en hanterad enhet. Det är upp till dig som en administratör att bedöma om de metoder som tillämpas på din lokala domänanslutna enheter är starkt nog för att utgöra en hanterad enhet, om sådana en enhet är även en Hybrid Azure AD enhet.


## <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten är markerad som kompatibel

Alternativet att *kräver en enhet är markerad som kompatibel* är den starkaste formen att begära en hanterad enhet.

![Enhetsbaserad villkor](./media/require-managed-devices/11.png)

Det här alternativet kräver en enhet som ska registreras med Azure AD och är markerad som kompatibel genom att:
         
- Intune.
- Ett tredje parts mobila enheter (MDM) hanteringssystem som hanterar Windows 10-enheter via Azure AD-integrering. Tredje parts MDM-system för enhetstyper operativsystem än Windows 10 stöds inte.
 
![Enhetsbaserad villkor](./media/require-managed-devices/46.png)



För en enhet som har markerats som kompatibel kan anta du att: 

- Mobila enheter som anställda använder för att komma åt företagets data ska hanteras
- Mobila apparna som personalen använder hanteras
- Företagets information skyddas genom att styra hur anställda får åtkomst till och delar det
- Enheten och dess program är kompatibla med företagets säkerhetskrav




## <a name="next-steps"></a>Nästa steg

Innan du konfigurerar principer för enhetsbaserad villkorlig åtkomst i din miljö, bör du ta en titt på de [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md).

