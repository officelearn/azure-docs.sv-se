---
title: Privileged Identity Management-prenumerationerna - Azure | Microsoft Docs
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
ms.topic: conceptual
ms.component: pim
ms.date: 06/01/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dbe4efa57a7c4c31b2bdca84665eef8179564a8f
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617077"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Krav för Azure Active Directory Privileged Identity Management-prenumeration

Azure AD Privileged Identity Management är tillgängligt som en del av Premium P2-versionen av Azure AD. Mer information om andra funktioner för P2 och jämförelse med Premium P1 finns [Azure Active Directory-versioner](../active-directory-editions.md).

>[!NOTE]
När Azure Active Directory (Azure AD) Privileged Identity Management fanns i en förhandsversion, fanns det ingen licens-kontroller för en klient att prova tjänsten.  Nu när Azure AD Privileged Identity Management har blivit allmänt tillgängliga, måste en utvärderingsversion eller betald prenumeration vara tillgänglig för klienten att fortsätta använda Privileged Identity Management efter December 2016.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Bekräfta din utvärderingsversion eller betald prenumeration

Om du inte är säker på om din organisation har en utvärderingsversion eller köpte en prenumeration kan du kontrollera om det finns en prenumeration i din klient med hjälp av kommandon som ingår i Azure Active Directory-modulen för Windows PowerShell V1. 
1. Öppna ett PowerShell-fönster.
2. Ange `Connect-MsolService` autentisera sig som en användare i din klient.
3. Ange `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Det här kommandot hämtar en lista över prenumerationer i din klient. Om det finns inga rader som returneras kan behöver du skaffa en Azure AD Premium P2 utvärderingsversion, inköp en Azure AD Premium P2-prenumeration eller EMS E5-prenumeration för att använda Azure AD Privileged Identity Management.  För att få en utvärderingsversion och börja använda Azure AD Privileged Identity Management kan du läsa [Kom igång med Azure AD Privileged Identity Management](pim-getting-started.md).

Om det här kommandot returnerar en rad i vilken SkuPartNumber är ”AAD_PREMIUM_P2” eller ”EMSPREMIUM” och IsTrial är ”True”, innebär en utvärderingsversion av Azure AD Premium P2 finns i klienten.  Om prenumerationen inte är aktiverad och du inte har en Azure AD Premium P2- eller EMS E5-prenumeration köpa, måste sedan du köpa en Azure AD Premium P2-prenumeration eller EMS E5-prenumeration för att fortsätta använda Azure AD Privileged Identity Management.

Azure AD Premium P2 är tillgänglig via en [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [Open Volume License-programmet](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx), och [Cloud Solution Providers programmet](https://partner.microsoft.com/cloud-solution-provider). Azure och Office 365-prenumeranter kan även köpa Azure AD Premium P2 online.  Mer information om priser för Azure AD Premium och hur du beställa online kan hittas på [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Azure AD Privileged Identity Management är inte tillgänglig i klienten

Azure AD Privileged Identity Management är inte längre tillgängliga i din klientorganisation om:
- Din organisation använde Azure AD Privileged Identity Management när den fanns i en förhandsversion och väljer att inte köpa Azure AD Premium P2-prenumeration eller EMS E5-prenumeration.
- Din organisation har en Azure AD Premium P2- eller EMS E5-utvärderingen som upphört att gälla.
- Din organisation hade en köpta prenumeration som har upphört att gälla.

När en Azure AD Premium P2-prenumeration eller EMS E5-prenumeration upphör att gälla eller en organisation som använder Azure AD Privileged Identity Management i förhandsversion inte att hämta Azure AD Premium P2- eller EMS E5-prenumeration:

- Permanent rolltilldelningar till Azure AD-roller påverkas.
- Azure AD Privileged Identity Management-tillägget för Azure-portalen, som Graph API-cmdletar och PowerShell-gränssnitt i Azure AD Privileged Identity Management, längre inte tillgänglig för användare att aktivera Privilegierade roller, hantera Privilegierade komma åt eller utföra åtkomstgranskningar av Privilegierade roller.
- Berättigade rolltilldelningar för Azure AD-roller tas bort, eftersom användarna kommer inte längre att kunna aktivera Privilegierade roller.
- Alla pågående åtkomstgranskningar av Azure AD-roller kommer att avslutas och konfigurationsinställningar för Azure AD Privileged Identity Management kommer att tas bort.
- Azure AD Privileged Identity Management kommer inte längre skicka e-postmeddelanden på ändringarna för tilldelningen av rollen.

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure AD Privileged Identity Management](pim-getting-started.md)
- [Roller i Azure AD Privileged Identity Management](pim-roles.md)
