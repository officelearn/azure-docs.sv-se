---
title: Resurser, roller och åtkomstkontroll i Azure Application Insights | Microsoft Docs
description: Ägare, deltagare och läsare av insikter som din organisation.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: mbullwin
ms.openlocfilehash: 0f348e3e7dc2812bf354d1f8ec86330b0742439a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361871"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Resurser, roller och åtkomstkontroll i Application Insights

Du kan styra vem som har läs- och uppdatera åtkomst till dina data i Azure [Programinsikter][start], med hjälp av [rollbaserad åtkomstkontroll i Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Tilldela åtkomst till användare i den **resursgrupp eller prenumeration** som din programresurs tillhör – inte i själva resursen. Tilldela den **Application Insights-komponentdeltagare** roll. Detta säkerställer uniform kontroll av åtkomst till webbtester och aviseringar tillsammans med din programresurs. [Läs mer](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Resurser, grupper och prenumerationer

Första, vissa definitioner:

* **Resursen** – en instans av en Microsoft Azure-tjänst. Application Insights-resursen samlar in, analysera och visa dessa data skickas från ditt program.  Andra typer av Azure-resurser är webbappar, databaser och virtuella datorer.
  
    Om du vill se dina resurser, öppna den [Azure-portalen][portal], logga in och klicka på alla resurser. Om du vill hitta en resurs, skriver du datorns namn i filterfältet.
  
    ![Lista över Azure-resurser](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Resursgrupp** ] [ group] -varje resurs tillhör en grupp. En grupp är ett praktiskt sätt att hantera relaterade resurser, särskilt för åtkomstkontroll. Du kan till exempel placera en Webbapp, en Application Insights-resurs för att övervaka appen och en Storage-resurs för att hålla exporterade data i i en resursgrupp.

* [**Prenumeration** ](https://portal.azure.com) – om du vill använda Application Insights eller andra Azure-resurser som du loggar in på en Azure-prenumeration. Varje resursgrupp tillhör en Azure-prenumeration där du väljer ditt pris-paket och, om det är en organisation-prenumeration, Välj medlemmar och deras behörigheter.
* [**Microsoft-konto** ] [ account] -användarnamnet och lösenordet som används för att logga in på Microsoft Azure-prenumerationer, XBox Live, Outlook.com och andra Microsoft-tjänster.

## <a name="access"></a> Kontrollera åtkomst i resursgruppen

Det är viktigt att förstå att förutom den resurs som du skapade för ditt program, finns även separata dolda resurser för aviseringar och webbtester. De är anslutna till samma [resursgrupp](#resource-group) som Application Insights-resursen. Du kan också har placerat andra Azure-tjänster där, till exempel webbplatser eller lagring.

Styra åtkomsten till dessa resurser bör därför att:

* Styra åtkomsten på de **resursgrupp eller prenumeration** nivå.
* Tilldela den **Application Insights-komponent deltagare** roll till användare. Detta gör att de kan redigera webbtester och aviseringar Application Insights-resurser utan att ge åtkomst till andra tjänster i gruppen.

## <a name="to-provide-access-to-another-user"></a>Att ge åtkomst till en annan användare

Du måste ha behörighet för ägare till prenumerationen eller resursgruppen.

Användaren måste ha en [Account][account], eller få åtkomst till sina [Microsoft Account](../../active-directory/fundamentals/sign-up-organization.md). Du kan ge åtkomst till enskilda användare och även till användargrupper som definierats i Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navigera till resursgruppen eller direkt till själva resursen

Välj **åtkomstkontroll (IAM)** på den vänstra menyn.

![Skärmbild av Access control knappen i Azure-portalen](./media/resources-roles-access-control/0001-access-control.png)

Välj **Lägg till rolltilldelning**

![Skärmbild av behörighet kontrollmenyn med knappen Lägg till markerad i rött](./media/resources-roles-access-control/0002-add.png)

Den **Lägg till behörigheter** vyn nedan gäller främst till Application Insights-resurser, om du såg behörighet för åtkomstkontroll från en högre nivå som resursgrupper, ser du ytterligare icke-program Insights-centric roller.

Att visa information om alla rollbaserad åtkomstkontroll i Azure inbyggda roller använder den [officiella Referensinnehåll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Skärmbild av användaren rollen åtkomstkontrollistan](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Välj en roll

Tillämpliga länkar vi till den associerade officiella referensdokumentationen.

| Roll | I resursgrupp |
| --- | --- |
| [Ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Kan ändra allt, även användarnas åtkomst. |
| [Deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Kan redigera allt, även alla resurser. |
| [Application Insights-komponentdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Kan redigera Application Insights-resurser, webbtester och aviseringar. |
| [Läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Kan visa men inte ändra något. |
| [Application Insights Snapshot Debugger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Ger användarbehörighet att använda Application Insights Snapshot Debugger funktioner. Observera att den här rollen ingår i varken ägare eller deltagare roller. |
| Deltagare för versionshantering av Azure Service-distributionen | Deltagarrollen för tjänster som distribuerar via Azure Service-distributionen. |
| [Data Purger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Särskilda krav på roll för att rensa personliga data. Se våra [vägledning för personuppgifter](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) för mer information.   |
| ExpressRoute-administratör | Skapa ta bort och hantera expressvägarna.|
| [Log Analytics Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Log Analytics Contributor kan läsa alla övervakningsdata och redigera övervakningsinställningarna. Redigera övervakningsinställningarna omfattar att lägga till VM-tillägget för virtuella datorer; läsa lagringskontonycklar för att kunna konfigurera loggsamlingar från Azure-lagring. Skapa och konfigurera automationskonton; lägga till lösningar och konfigurera Azure diagnostics på alla Azure-resurser.  |
| [Log Analytics Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Log Analytics Reader kan visa och söka i alla övervakningsdata och dessutom visa övervakningsinställningar, bl.a. konfigurationen av Azure Diagnostics på alla Azure-resurser. |
| masterreader | Användaren kan visa allt, men inte göra några ändringar. |
| [Övervaka deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Kan läsa alla övervakningsdata och uppdatera inställningarna för övervakning. |
| [Övervaka mått utgivare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Aktiverar publicering av måtten mot Azure-resurser. |
| [Övervaka läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Kan läsa alla övervakningsdata. |
| Deltagare för resursprincip (förhandsversion) | Användare från EA med behörighet att skapa/ändra resursprinciper, skapa ett supportärende och läsa resurser/hierarkier.  |
| [Administratör för användaråtkomst](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Låter en användare att hantera åtkomsten för andra användare till Azure-resurser.|
| [Webbplatsdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Låter dig hantera webbplatser (inte webbplaner), men inte tillgång till dem....|

”Redigera” innehåller skapa, ta bort och uppdatera:

* Resurser
* Webbtest
* Aviseringar
* Löpande export

#### <a name="select-the-user"></a>Välj användaren

Om du vill att användaren inte är i katalogen, kan du bjuda in vem som helst med ett Microsoft-konto.
(Om de använder tjänster som Outlook.com, OneDrive, Windows Phone-eller XBox Live, har ett Microsoft-konto.)

## <a name="related-content"></a>Relaterat innehåll

* [Rollbaserad åtkomstkontroll i Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>PowerShell-fråga för att fastställa rollmedlemskap

Eftersom vissa roller kan bara kopplas till meddelanden och e-aviseringar kan det vara bra att kunna generera en lista över användare som tillhör en viss roll. Vi erbjuder följande exempelfrågor som kan ställas in så att de passar dina behov för att hjälpa med att generera dessa typer av listor:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Fråga hela prenumerationen för administratörsroller + deltagarroller

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Fråga inom ramen för en specifik Application Insights-resurs för ägare och deltagare

```powershell
$resourceGroup = “RGNAME”
$resourceName = “AppInsightsName”
$resourceType = “microsoft.insights/components”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Fråga inom ramen för en specifik resursgrupp för ägare och deltagare

```powershell
$resourceGroup = “RGNAME”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
