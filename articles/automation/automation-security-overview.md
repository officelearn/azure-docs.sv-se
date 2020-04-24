---
title: Introduktion till autentisering i Azure Automation
description: I den här artikeln ges en översikt över säkerheten i Automation och de olika autentiseringsmetoder som är tillgängliga för Automation-konton i Azure Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114538"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Introduktion till autentisering i Azure Automation

Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS). För att en runbook ska kunna utföra sina åtgärder måste den ha behörighet att komma åt resurserna på ett säkert sätt med den minsta behörighet som krävs i prenumerationen.

Den här artikeln beskriver de olika autentiserings scenarier som stöds av Azure Automation och hur du kommer igång baserat på miljön eller miljöerna som du behöver hantera.  

## <a name="automation-account-overview"></a>Översikt för Automation-konton

När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser (runbooks, tillgångar, konfigurationer) från resurserna som finns i andra Automation-konton. Du kan använda Automation-konton för att hålla resurser åtskilda i separata logiska miljöer. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö. Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen.

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men Automation-konton kan hantera alla resurser i din prenumeration. Huvudskälet till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en specifik region.

Alla aktiviteter som du utför mot resurser med hjälp av Azure Resource Manager och Azure-cmdletar i Azure Automation måste autentisera till Azure med hjälp av autentiseringsuppgiftsbaserad autentisering med organisationens Azure Active Directory-ID. Kör som-konton i Azure Automation tillhandahålla autentisering för att hantera resurser i Azure med hjälp av Azure-cmdletar. När du skapar ett Kör som-konto skapar det en ny tjänst huvud användare i Azure Active Directory (AD) och tilldelar rollen deltagare till den här användaren på prenumerations nivå. För Runbooks som använder hybrid Runbook Worker på Azure Virtual Machines kan du använda [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) i stället för kör som-konton för att autentisera till dina Azure-resurser.

Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörighet för tjänstens huvud namn under API- **behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

Rollbaserad åtkomstkontroll är tillgängligt i Azure Resource Manager-läge för att bevilja tillåtna åtgärder för ett Azure AD-användarkonto och Kör som-konto för att autentisera tjänstobjektet. Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).  

Runbooks som körs på en Hybrid Runbook Worker i ditt data Center eller mot data behandlings tjänster i andra moln miljöer som AWS, kan inte använda samma metod som används vanligt vis för Runbooks som autentiseras för Azure-resurser. Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt. Mer information om Runbook-autentisering med Runbook Worker finns i [autentisera Runbooks för Hybrid Runbook Worker](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Automation-konto från Azure Portal](automation-create-standalone-account.md).

* [Skapa ett Automation-konto med hjälp av Azure Resource Manager mall](automation-create-account-template.md).

* [Autentisera med Amazon Web Services (AWS)](automation-config-aws-account.md).
