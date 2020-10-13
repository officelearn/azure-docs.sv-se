---
title: Översikt över Azure Automation konto autentisering
description: Den här artikeln innehåller en översikt över Azure Automation autentisering av konton.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766099"
---
# <a name="automation-account-authentication-overview"></a>Översikt över autentisering av Automation-konto

Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS). Du kan använda Runbooks för att automatisera dina uppgifter eller en Hybrid Runbook Worker om du har affärs-eller drift processer för att hantera utanför Azure. Att arbeta i någon av dessa miljöer kräver behörighet att säkert komma åt resurserna med minsta möjliga behörighet.

Den här artikeln beskriver de autentiseringsmetoder som stöds av Azure Automation och ger information om hur du kommer igång baserat på miljön eller miljöerna som du behöver hantera.

## <a name="automation-account"></a>Automation-konto

När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser, Runbooks, till gångar, konfigurationer från andra kontons resurser. Du kan använda Automation-konton för att hålla resurser åtskilda i separata logiska miljöer. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö. Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen. En introduktion till hur du skapar ett Automation-konto finns i [skapa ett Automation-konto](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Automation-resurser

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men kontot kan hantera alla resurser i din Azure-prenumeration. Huvud anledningen till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en särskild region.

Alla aktiviteter som du skapar mot resurser med hjälp av Azure Resource Manager och PowerShell-cmdletar i Azure Automation måste autentisera till Azure med hjälp av Azure Active Directory (Azure AD) autentisering baserad på autentiseringsuppgifter.

## <a name="run-as-accounts"></a>Kör som-konton

Kör som-konton i Azure Automation tillhandahålla autentisering för att hantera Azure Resource Manager resurser eller resurser som distribueras i den klassiska distributions modellen. Det finns två typer av kör som-konton i Azure Automation:

* Kör som-konto i Azure
* Det klassiska kör som-kontot i Azure

Mer information om de här två distributions modellerna finns i [Resource Manager och klassisk distribution](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Azure Cloud Solution Provider (CSP)-prenumerationer stöder endast Azure Resource Managers modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration skapas inte det klassiska kör som-kontot i Azure, men kör som-kontot i Azure skapas. Mer information om CSP-prenumerationer finns i [tillgängliga tjänster i CSP-prenumerationer](/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>Kör som-konto

Kör som-kontot i Azure hanterar Azure-resurser baserat på Azure Resource Manager distributions-och hanterings tjänst för Azure.

När du skapar ett Kör som-konto utför det följande uppgifter:

* Skapar ett Azure AD-program med ett självsignerat certifikat, skapar ett tjänst objekts konto för programmet i Azure AD och tilldelar rollen [deltagare](../role-based-access-control/built-in-roles.md#contributor) för kontot i din aktuella prenumeration. Du kan ändra certifikat inställningen till ägare eller en annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).

* Skapar en Automation-certifikat till gång med namnet `AzureRunAsCertificate` i det angivna Automation-kontot. Certifikat till gången innehåller certifikatets privata nyckel som Azure AD-programmet använder.

* Skapar en Automation-anslutning till gång med namnet `AzureRunAsConnection` i det angivna Automation-kontot. Anslutnings till gången innehåller program-ID, klient-ID, prenumerations-ID och tumavtryck för certifikatet.

### <a name="azure-classic-run-as-account"></a>Klassiskt Kör som-konto i Azure

Det klassiska kör som-kontot i Azure hanterar de klassiska Azure-resurserna baserat på den klassiska distributions modellen. Du måste vara en medadministratör i prenumerationen för att kunna skapa eller förnya den här typen av kör som-konto.

När du skapar ett klassiskt kör som-konto i Azure utförs följande uppgifter.

* Skapar ett hanterings certifikat i prenumerationen.

* Skapar en Automation-certifikat till gång med namnet `AzureClassicRunAsCertificate` i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.

* Skapar en Automation-anslutning till gång med namnet `AzureClassicRunAsConnection` i det angivna Automation-kontot. Anslutnings till gången innehåller prenumerations namnet, prenumerations-ID och certifikatets till gångs namn.

>[!NOTE]
>Det klassiska kör som-kontot i Azure skapas inte som standard på samma gång när du skapar ett Automation-konto. Det här kontot skapas individuellt enligt stegen som beskrivs i artikeln [Hantera kör som-konto](manage-runas-account.md#create-a-run-as-account-in-azure-portal) .

## <a name="service-principal-for-run-as-account"></a>Tjänstens huvud namn för kör som-konto

Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörighet för tjänstens huvud namn under **API-behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till ditt webb-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Rollbaserad åtkomst kontroll är tillgänglig med Azure Resource Manager för att bevilja tillåtna åtgärder till ett Azure AD-användarkonto och kör som-konto och autentisera tjänstens huvud namn. Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-autentisering med Hybrid Runbook Worker

Runbooks som körs på en Hybrid Runbook Worker i ditt data Center eller från data behandlings tjänster i andra moln miljöer som AWS, kan inte använda samma metod som används vanligt vis för Runbooks som autentiseras för Azure-resurser. Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt. Mer information om Runbook-autentisering med Runbook Worker finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

För Runbooks som använder hybrid Runbook Worker på virtuella Azure-datorer kan du använda [Runbook-autentisering med hanterade identiteter](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) i stället för kör som-konton för att autentisera till dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar ett Automation-konto från Azure Portal finns i [skapa ett fristående Azure Automation-konto](automation-create-standalone-account.md).
* Om du föredrar att skapa ditt konto med hjälp av en mall kan du läsa [skapa ett Automation-konto med hjälp av en Azure Resource Manager-mall](quickstart-create-automation-account-template.md).
* För autentisering med hjälp av Amazon Web Services, se [autentisera Runbooks med Amazon Web Services](automation-config-aws-account.md).