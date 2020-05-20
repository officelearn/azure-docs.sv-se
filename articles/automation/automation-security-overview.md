---
title: Översikt över Azure Automation konto autentisering
description: Den här artikeln innehåller en översikt över Azure Automation säkerhets-och autentiseringsmetoder för Automation-konton.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: c9e3c2b15b45eb15b40782a5d0ecfe1e736fc013
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683516"
---
# <a name="azure-automation-account-authentication-overview"></a>Översikt över Azure Automation konto autentisering

Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS). För att en Runbook ska kunna utföra de åtgärder som krävs måste den ha behörighet att på ett säkert sätt komma åt resurserna med de minimala rättigheter som krävs i prenumerationen.

När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser (runbooks, tillgångar, konfigurationer) från resurserna som finns i andra Automation-konton. Du kan använda Automation-konton för att hålla resurser åtskilda i separata logiska miljöer. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö. Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen.

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men Automation-konton kan hantera alla resurser i din prenumeration. Huvudskälet till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en specifik region.

Alla aktiviteter som du utför mot resurser med hjälp av Azure Resource Manager och Azure-cmdletar i Azure Automation måste autentisera till Azure med hjälp av Azure Active Directory (Azure AD) autentisering baserad på autentiseringsuppgifter. Kör som-konton i Azure Automation tillhandahålla autentisering för att hantera resurser i Azure med hjälp av Azure-cmdletar. När du skapar ett Kör som-konto skapas en ny tjänst huvud användare i Azure AD och deltagar rollen tilldelas den här användaren på prenumerations nivå. För Runbooks som använder hybrid Runbook Worker på Azure Virtual Machines kan du använda [Runbook-autentisering med hanterade identiteter](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) i stället för kör som-konton för att autentisera till dina Azure-resurser.

Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörighet för tjänstens huvud namn under API- **behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

Rollbaserad åtkomstkontroll är tillgängligt i Azure Resource Manager-läge för att bevilja tillåtna åtgärder för ett Azure AD-användarkonto och Kör som-konto för att autentisera tjänstobjektet. Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).  

Runbooks som körs på en Hybrid Runbook Worker i ditt data Center eller mot data behandlings tjänster i andra moln miljöer som AWS, kan inte använda samma metod som används vanligt vis för Runbooks som autentiseras för Azure-resurser. Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt. Mer information om Runbook-autentisering med Runbook Worker finns i [autentisera Runbooks för Hybrid Runbook Worker](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Automation-konto från Azure Portal](automation-create-standalone-account.md).
* [Skapa ett Automation-konto med hjälp av Azure Resource Manager mall](automation-create-account-template.md).
* [Autentisera med Amazon Web Services (AWS)](automation-config-aws-account.md).
