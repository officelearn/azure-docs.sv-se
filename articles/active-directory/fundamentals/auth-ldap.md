---
title: LDAP-autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå LDAP-autentisering med Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5314758acecae2a9d68f2405fc1c3d2196950b4
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577064"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>LDAP-autentisering med Azure Active Directory

LDAP (Lightweight Directory Access Protocol) är ett program protokoll för att arbeta med olika katalog tjänster. Katalog tjänster, till exempel Active Directory, [lagra användar-och konto information](https://www.dnsstuff.com/active-directory-service-accounts)samt säkerhets information som lösen ord. Tjänsten tillåter sedan att informationen delas med andra enheter i nätverket. Företags program, till exempel e-post, kund Relations hanterare (CRMs) och personal program (HR) kan använda LDAP för att autentisera, komma åt och hitta information. 

Azure Active Directory (Azure AD) stöder det här mönstret via Azure AD Domain Services (AD DS). Det gör det möjligt för organisationer som använder en molnbaserad strategi att modernisera sin miljö genom att flytta sina lokala LDAP-resurser till molnet. De omedelbara fördelarna är: 

* Integrerad med Azure AD. Tillägg av användare och grupper eller attributändringar synkroniseras automatiskt från din Azure AD-klient till AD DS. Ändringar av objekt i lokala Active Directory synkroniseras till Azure AD och sedan till AD DS.

* Förenkla driften. Minskar behovet av att manuellt behålla och korrigera lokala infrastrukturer. 

* Informations. Du får hanterade tjänster med hög tillgänglighet 

## <a name="use-when"></a>Använd när

Det finns ett behov av att ett program eller en tjänst ska använda LDAP-autentisering.

![Diagram över arkitektur](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare** : använder LDAP-beroende program via en webbläsare.

* **Webbläsare** : gränssnittet som användaren interagerar med för att få åtkomst till den externa URL: en för programmet.

* **Virtual Network** : ett privat nätverk i Azure genom vilket det äldre programmet kan använda LDAP-tjänster. 

* **Äldre program** : program-eller Server arbets belastningar som kräver att LDAP distribueras antingen i ett virtuellt nätverk i Azure eller som har synlighet för AD DS-instansens IP-adresser via nätverks vägar. 

* **Azure AD** : synkroniserar identitets information från organisationens lokala katalog via Azure AD Connect.

* **Azure AD Domain Services (AD DS)** : utför en enkelriktad synkronisering från Azure AD för att ge åtkomst till en central uppsättning användare, grupper och autentiseringsuppgifter. AD DS-instansen tilldelas till ett virtuellt nätverk. Program, tjänster och virtuella datorer i Azure som ansluter till det virtuella nätverk som tilldelats till AD DS kan använda vanliga AD DS-funktioner som LDAP, domän anslutning, grup princip, Kerberos och NTLM-autentisering.
   > [!NOTE]
   >  I miljöer där organisationen inte kan synkronisera lösenords-hashar eller användare loggar in med smartkort, rekommenderar vi att du använder en resurs skog i AD DS. 

* **Azure AD Connect** : ett verktyg för synkronisering av lokal identitets information till Microsoft Azure AD. Distributions guiden och de guidade upplevelserna hjälper dig att konfigurera nödvändiga komponenter och komponenter som krävs för anslutningen, inklusive synkronisering och inloggning från Active Directory till Azure AD. 

* **Active Directory** : Katalog tjänst som lagrar [lokal identitets information, till exempel användar-och konto information](https://www.dnsstuff.com/active-directory-service-accounts), samt säkerhets information som lösen ord.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Implementera LDAP-autentisering med Azure AD

* [Skapa och konfigurera en Azure AD DS-instans](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance) 

* [Konfigurera virtuella nätverk för en Azure AD DS-instans](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-networking) 

* [Konfigurera säkert LDAP för en Azure AD DS-hanterad domän](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps) 

* [Skapa ett utgående skogs förtroende till en lokal domän i Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-forest-trust)

 
