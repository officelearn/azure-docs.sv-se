---
title: Katalog-synkronisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå detta mönster för synkronisering
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
ms.openlocfilehash: 862d17948cb09c18f9372f8b8b981e5efa6be71b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114317"
---
# <a name="directory-synchronization"></a>Katalogsynkronisering

Många organisationer har en hybrid infrastruktur som omfattar både lokala och molnbaserade komponenter. Genom att synkronisera användarnas identiteter mellan lokala och moln kataloger kan användare komma åt resurser med en enda uppsättning autentiseringsuppgifter. 

Synkronisering är processen för 

* skapa ett objekt baserat på vissa villkor
* hålla objektet uppdaterat
* tar bort objektet när villkoren inte längre uppfylls. 

Lokal etablering innebär etablering från lokala källor (t. ex. Active Directory) till Azure Active Directory (Azure AD). 

## <a name="use-when"></a>Använd när

Du måste synkronisera identitets data från dina lokala Active Directory miljöer till Azure AD.

![arkitektur diagram](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: ansluter till ett program med hjälp av Azure AD.

* **Webbläsare**: komponenten som användaren interagerar med för att få åtkomst till den externa URL: en för programmet.

* **Program**: webbapp som förlitar sig på användningen av Azure AD för autentisering och auktorisering.

* **Azure AD**: synkroniserar identitets information från organisationens lokala katalog via Azure AD Connect. 

* **Azure AD Connect**: ett verktyg för att ansluta lokala identitets infrastrukturer till Microsoft Azure AD. Guiden och de guidade upplevelserna hjälper dig att distribuera och konfigurera krav och komponenter som krävs för anslutningen, inklusive synkronisering och inloggning från aktiva kataloger till Azure AD. 

* **Active Directory**: Active Directory är en katalog tjänst som ingår i de flesta Windows Server-operativsystem. Servrar som kör Active Directory Domain Services (AD DS) kallas domänkontrollanter. De autentiserar och auktoriserar alla användare och datorer i domänen.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Implementera katalog-synkronisering med Azure AD

* [Vad är identitetsetablering?](https://docs.microsoft.com/azure/active-directory/cloud-provisioning/what-is-provisioning) 

* [Verktyg för katalog integrering med hybrid identitet](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Azure AD Connect installations översikt](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap)
