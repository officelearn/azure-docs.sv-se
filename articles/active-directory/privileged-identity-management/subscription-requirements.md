---
title: Privileged Identity Management - prenumerationer i Azure | Microsoft Docs
description: Beskriver prenumerationen och Licensieringskrav för att hantera och använda Azure AD Privileged Identity Management i din klient
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 69a27a2a75eb2a08a93b8b70648733673eac36db
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260054"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Krav för Azure Active Directory Privileged Identity Management-prenumeration

Azure AD Privileged Identity Management är tillgänglig som en del av Premium P2-versionen av Azure AD. Mer information om andra funktioner P2 och jämförelse med Premium P1 finns [Azure Active Directory-versioner](../active-directory-editions.md).

>[!NOTE]
När Azure Active Directory (Azure AD) Privileged Identity Management i förhandsgranskningen, fanns det ingen licens kontroller för en klient att prova tjänsten.  Nu när Azure AD Privileged Identity Management har nått allmän tillgänglighet, måste en utvärderingsversion eller betald prenumeration finnas att fortsätta använda Privileged Identity Management efter December 2016-klienten.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Bekräfta din utvärderingsversion eller betald prenumeration

Om du inte är säker på om din organisation har en utvärderingsversion eller köpt prenumeration kan du kontrollera om det finns en prenumeration i din klient med hjälp av kommandon som ingår i Azure Active Directory-modulen för Windows PowerShell V1. 
1. Öppna ett PowerShell-fönster.
2. Ange `Connect-MsolService` autentisera sig som en användare i din klient.
3. Ange `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Detta kommando hämtar en lista över prenumerationer i din klient. Om det finns inga rader som returneras kan behöver du skaffa en utvärderingsversion, inköp i Azure AD Premium P2 en Azure AD Premium P2-prenumeration eller EMS E5 prenumeration att använda Azure AD Privileged Identity Management.  Om du vill hämta en utvärderingsversion och börja använda Azure AD Privileged Identity Management läsa [Kom igång med Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Om det här kommandot returnerar en rad i vilka SkuPartNumber är ”AAD_PREMIUM_P2” eller ”EMSPREMIUM” och IsTrial är ”True”, innebär en utvärderingsversion av Azure AD Premium P2 finns i klienten.  Om du inte har en Azure AD Premium P2- eller EMS E5 prenumeration köpa prenumeration inte är aktiverad, måste sedan du köpa en Azure AD Premium P2-prenumeration eller EMS E5 prenumeration fortsätta att använda Azure AD Privileged Identity Management.

Azure AD Premium P2 är tillgänglig via en [Microsoft Enterprise-avtal](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [öppna volymlicensprogram](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx), och [Cloud Solution Providers programmet](https://partner.microsoft.com/en-US/cloud-solution-provider). Azure och Office 365-prenumeranter kan också köpa Azure AD Premium P2 online.  Mer information om priser för Azure AD Premium och beställa online finns på [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Azure AD Privileged Identity Management är inte tillgänglig i klient

Azure AD Privileged Identity Management kommer inte längre tillgängliga i din klientorganisation om:
- Din organisation använde Azure AD Privileged Identity Management när den är i förhandsvisning och inte köpa Azure AD Premium P2-prenumeration eller EMS E5-prenumeration.
- Din organisation har en Azure AD Premium P2- eller EMS E5 utvärderingsversion ämne som upphört att gälla.
- Din organisation har en prenumeration du köpt som upphört att gälla.

När en Azure AD Premium P2-prenumeration eller EMS E5 prenumeration upphör att gälla eller en organisation som använder Azure AD Privileged Identity Management i förhandsgranskningen inte att hämta Azure AD Premium P2- eller EMS E5 prenumeration:

- Permanent rolltilldelningar till Azure AD-roller påverkas.
- Azure AD Privileged Identity Management-tillägget i Azure portal samt Graph API-cmdlets och PowerShell-gränssnitt för Azure AD Privileged Identity Management längre inte tillgänglig för användare att aktivera Privilegierade roller, hantera privilegierad åtkomst, eller utföra åtkomst granskning av Privilegierade roller.
- Berättigad rolltilldelningar för Azure AD-roller ska tas bort, och användare kommer inte längre att kunna aktivera Privilegierade roller.
- Alla pågående åtkomst granskningar av Azure AD-roller avslutas och Azure AD Privileged Identity Management konfigurationsinställningar kommer att tas bort.
- Azure AD Privileged Identity Management kommer inte längre att skicka e-postmeddelanden om ändringarna för tilldelningen av rollen.

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)
- [Roller i Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-roles.md)
