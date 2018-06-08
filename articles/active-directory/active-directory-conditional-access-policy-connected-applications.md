---
title: Konfigurera principer för Azure Active Directory enhetsbaserad villkorlig åtkomst | Microsoft Docs
description: Lär dig hur du konfigurerar principer för Azure Active Directory enhetsbaserad villkorlig åtkomst.
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
ms.date: 06/07/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1c21c915bc0a83cdafb221a2cd592890577437ee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849533"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Konfigurera principer för Azure Active Directory enhetsbaserad villkorlig åtkomst

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare kan komma åt dina resurser. Du kan till exempel begränsa åtkomst till vissa resurser till hanterade enheter. En princip för villkorlig åtkomst som kräver en hanterad enhet kallas även för principen för enhetsbaserad villkorlig åtkomst.

Det här avsnittet beskrivs hur du kan konfigurera principer för enhetsbaserad villkorlig åtkomst för Azure AD-anslutna program. 


## <a name="before-you-begin"></a>Innan du börjar

Enhetsbaserad villkorlig åtkomst ties **villkorlig åtkomst i Azure AD** och **Azure AD-enhetshantering tillsammans**. Om du inte är bekant med något av dessa områden ännu, bör du först läsa i följande avsnitt:

- **[Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  -det här avsnittet ger en översikt över relaterade terminologi och villkorlig åtkomst.

- **[Introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)**  -det här avsnittet innehåller en översikt över de olika alternativ som du måste ansluta enheter med Azure AD. 



## <a name="managed-devices"></a>Hanterade enheter  

I en mobile första, molnet först värld kan Azure Active Directory enkel inloggning till enheter, appar och tjänster från var som helst. För vissa kanske resurser i din miljö och bevilja åtkomst till rätt användare inte tillräckligt bra. Förutom rätt användare kan du också kräva att åtkomstförsök kan endast utföras med hjälp av en hanterad enhet.

En hanterad enhet är en enhet som uppfyller dina krav för säkerhet och efterlevnad. Enkelt uttryckt hanterade enheter är enheter som är *vissa sortera* för organisationens kontroll. I Azure AD är nödvändiga för en hanterad enhet har registrerats med Azure AD. Registrerar en enhet skapas en identitet för enheten i form av ett enhetsobjekt. Det här objektet används av Azure för att spåra statusinformation om en enhet. Som en Azure AD-administratör kan du redan använder det här objektet ska växla (aktivera/inaktivera) tillstånd för en enhet.
  
![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/32.png)

För att få en enhet som registrerats med Azure AD, finns det tre alternativ:

- **[Azure AD registrerade enheter](device-management-introduction.md#azure-ad-registered-devices)**  - om du vill hämta en personlig enhet som registrerats med Azure AD

- **[Azure AD-anslutna enheter](device-management-introduction.md#azure-ad-joined-devices)**  - om du vill hämta en organisations Windows 10-enhet som inte är ansluten till en lokal AD registrerad med Azure AD. 

- **[Hybrid Azure AD-anslutna enheter](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  - om du vill hämta en Windows 10-enhet som är ansluten till en lokal AD registrerad med Azure AD.

Om du vill bli en hanterad enhet, kan en registrerad enhet vara en Hybrid Azure AD-ansluten enhet eller en enhet som har markerats som kompatibla.  

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
         
- Intune 
- En mobil enhet från tredje part hanteras system som hanterar Windows 10-enheter via Azure AD-integrering 
 
![Enhetsbaserad villkor](./media/active-directory-conditional-access-policy-connected-applications/46.png)



För en enhet som har markerats som kompatibelt kan anta du att: 

- Mobila enheter som personalen använder för att komma åt företagets data ska hanteras
- Mobila appar som använder din arbetsstyrka hanteras
- Företagsinformationen skyddas genom att styra hur personalen åtkomst och delar det
- Enheten och dess program är kompatibla med företagets säkerhetskrav




## <a name="next-steps"></a>Nästa steg

Innan du konfigurerar en princip för enhetsbaserad villkorlig åtkomst i din miljö bör du ta en titt på den [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md).

