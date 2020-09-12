---
title: Översikt över Azure Automation konto autentisering
description: Den här artikeln innehåller en översikt över Azure Automation autentisering av konton.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 8068d6ebe67dee1408420441aacd83726a1986df
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434273"
---
# <a name="automation-account-authentication-overview"></a>Översikt över autentisering av Automation-konto

Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS). Du kan använda Runbooks för att automatisera dina uppgifter eller en Hybrid Runbook Worker om du har icke-Azure-uppgifter att hantera. Antingen måste miljön ha behörighet att säkert komma åt resurserna med de minimala rättigheter som krävs i Azure-prenumerationen.

Den här artikeln beskriver de autentiseringsmetoder som stöds av Azure Automation och ger information om hur du kommer igång baserat på miljön eller miljöerna som du behöver hantera.

## <a name="automation-account"></a>Automation-konto

När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser, Runbooks, till gångar, konfigurationer från andra kontons resurser. Du kan använda Automation-konton för att hålla resurser åtskilda i separata logiska miljöer. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö. Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen. En introduktion till hur du skapar ett Automation-konto finns i [skapa ett Automation-konto](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Automation-resurser

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men kontot kan hantera alla resurser i din Azure-prenumeration. Huvud anledningen till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en särskild region.

Alla aktiviteter som du skapar mot resurser med hjälp av Azure Resource Manager och PowerShell-cmdletar i Azure Automation måste autentisera till Azure med hjälp av Azure Active Directory (Azure AD) autentisering baserad på autentiseringsuppgifter.

## <a name="run-as-account"></a>Kör som-konto

Kör som-konton i Azure Automation tillhandahåller autentisering för hantering av Azure-resurser med hjälp av PowerShell-cmdletar. När du skapar ett Kör som-konto skapas en ny tjänst huvud användare i Azure AD och deltagar rollen tilldelas den här användaren på prenumerations nivå. För Runbooks som använder hybrid Runbook Worker på virtuella Azure-datorer kan du använda [Runbook-autentisering med hanterade identiteter](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) i stället för kör som-konton för att autentisera till dina Azure-resurser.

## <a name="service-principal-for-run-as-account"></a>Tjänstens huvud namn för kör som-konto

Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörighet för tjänstens huvud namn under **API-behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till ditt webb-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Rollbaserad åtkomst kontroll är tillgänglig med Azure Resource Manager för att bevilja tillåtna åtgärder till ett Azure AD-användarkonto och kör som-konto och autentisera tjänstens huvud namn. Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-autentisering med Hybrid Runbook Worker

Runbooks som körs på en Hybrid Runbook Worker i ditt data Center eller från data behandlings tjänster i andra moln miljöer som AWS, kan inte använda samma metod som används vanligt vis för Runbooks som autentiseras för Azure-resurser. Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt. Mer information om Runbook-autentisering med Runbook Worker finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar ett Automation-konto från Azure Portal finns i [skapa ett fristående Azure Automation-konto](automation-create-standalone-account.md).
* Om du föredrar att skapa ditt konto med hjälp av en mall kan du läsa [skapa ett Automation-konto med hjälp av en Azure Resource Manager-mall](quickstart-create-automation-account-template.md).
* För autentisering med hjälp av Amazon Web Services, se [autentisera Runbooks med Amazon Web Services](automation-config-aws-account.md).