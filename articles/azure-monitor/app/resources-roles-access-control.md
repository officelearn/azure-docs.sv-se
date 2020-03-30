---
title: Resurser, roller och åtkomstkontroll i Azure Application Insights | Microsoft-dokument
description: Ägare, bidragsgivare och läsare av organisationens insikter.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 1e57af269c4052d0dcd4a8f7970ca23017024299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473140"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Resurser, roller och åtkomstkontroll i Application Insights

Du kan styra vem som har läs- och uppdateringsåtkomst till dina data i Azure [Application Insights][start], med hjälp av [rollbaserad åtkomstkontroll i Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Tilldela åtkomst till användare i **resursgruppen eller prenumerationen** som programresursen tillhör - inte i själva resursen. Tilldela rollen **Application Insights-komponentdeltagare.** Detta säkerställer enhetlig kontroll av åtkomst till webbtester och aviseringar tillsammans med din programresurs. [Läs mer](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Resurser, grupper och prenumerationer

För det första några definitioner:

* **Resurs** - En instans av en Microsoft Azure-tjänst. Application Insights-resursen samlar in, analyserar och visar telemetridata som skickas från ditt program.  Andra typer av Azure-resurser är webbappar, databaser och virtuella datorer.
  
    Om du vill se dina resurser öppnar du [Azure-portalen,][portal]loggar in och klickar på Alla resurser. Om du vill hitta en resurs skriver du en del av namnet i filterfältet.
  
    ![Lista över Azure-resurser](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Resursgrupp**][group] - Varje resurs tillhör en grupp. En grupp är ett bekvämt sätt att hantera relaterade resurser, särskilt för åtkomstkontroll. Till exempel i en resursgrupp kan du placera en Web App, en Application Insights-resurs för att övervaka appen och en lagringsresurs för att hålla exporterade data.

* [**Prenumeration**](https://portal.azure.com) - Om du vill använda Application Insights eller andra Azure-resurser loggar du in på en Azure-prenumeration. Varje resursgrupp tillhör en Azure-prenumeration, där du väljer ditt prispaket och, om det är en organisationsprenumeration, väljer medlemmar och deras åtkomstbehörigheter.
* [**Microsoft-konto**][account] – Användarnamnet och lösenordet som du använder för att logga in på Microsoft Azure-prenumerationer, Xbox Live, Outlook.com och andra Microsoft-tjänster.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a>Kontrollera åtkomsten i resursgruppen

Det är viktigt att förstå att förutom den resurs som du skapade för ditt program, det finns också separata dolda resurser för aviseringar och webbtester. De är kopplade till samma [resursgrupp](#resource-group) som application insights-resursen. Du kan också ha placerat andra Azure-tjänster där, till exempel webbplatser eller lagring.

För att kontrollera åtkomsten till dessa resurser rekommenderas därför att:

* Kontrollera åtkomsten på **resursgrupps- eller prenumerationsnivå.**
* Tilldela **deltagarrollen Application Insights Component** till användare. På så sätt kan de redigera webbtester, aviseringar och application insights-resurser, utan att ge åtkomst till andra tjänster i gruppen.

## <a name="to-provide-access-to-another-user"></a>Så här ger du åtkomst till en annan användare

Du måste ha ägarrättigheter till prenumerationen eller resursgruppen.

Användaren måste ha ett [Microsoft-konto][account]eller ha åtkomst till sitt [organisatoriska Microsoft-konto](../../active-directory/fundamentals/sign-up-organization.md). Du kan ge åtkomst till enskilda personer och även till användargrupper som definierats i Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navigera till resursgruppen eller direkt till själva resursen

Välj **Åtkomstkontroll (IAM)** på menyn till vänster.

![Skärmbild av knappen Åtkomstkontroll i Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Välj **Lägg till rolltilldelning**

![Skärmbild av Kontrollmenyn i Access med knappen Lägg till markerad i rött](./media/resources-roles-access-control/0002-add.png)

**Vyn Lägg till behörigheter** nedan är i första hand specifik för Application Insights-resurser, om du visade åtkomstkontrollbehörigheterna från en högre nivå som resursgrupper, skulle du se ytterligare icke-Application Insights-centrerade roller.

Om du vill visa information om alla azure-rollbaserade åtkomstkontrolls inbyggda roller använder du det [officiella referensinnehållet](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Skärmbild av användarrolllistan för åtkomstkontroll](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Välj en roll

I förekommande fall länkar vi till tillhörande officiella referensdokumentation.

| Roll | I resursgruppen |
| --- | --- |
| [Ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Kan ändra vad som helst, inklusive användaråtkomst. |
| [Deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Kan redigera vad som helst, inklusive alla resurser. |
| [Deltagare i application insights-komponent](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Kan redigera application insights-resurser. |
| [Läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Kan visa men inte ändra någonting. |
| [Ögonblicksbildfelsökare för programinsikter](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Ger användaren behörighet att använda funktioner för ögonblicksbildfelsökning av programinsikter. Observera att den här rollen ingår i varken rollerna Ägare eller Deltagare. |
| Azure Service Deploy Release Management Contributor | Deltagarroll för tjänster som distribueras via Azure Service Deploy. |
| [Datarensare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Särskild roll för att rensa personuppgifter. Se vår [vägledning för personuppgifter](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) för mer information.   |
| ExpressRoute-administratör | Kan skapa borttagnings- och hantering av expressvägar.|
| [Log Analytics Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Log Analytics Contributor kan läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningarna inkluderar att lägga till vm-tillägget till virtuella datorer. läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage; skapa och konfigurera Automation-konton; Lägga till lösningar; och konfigurera Azure-diagnostik på alla Azure-resurser.  |
| [Log Analytics Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Log Analytics Reader kan visa och söka efter alla övervakningsdata samt och visa övervakningsinställningar, inklusive visning av konfigurationen av Azure-diagnostik på alla Azure-resurser. |
| masterreader (masterreader) | Tillåter en användare att visa allt men inte göra ändringar. |
| [Övervakningsbidragsgivare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Kan läsa alla övervakningsdata och uppdatera övervakningsinställningar.|
| [Utgivare av övervakningsmått](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Aktiverar publiceringsmått mot Azure-resurser. |
| [Övervakningsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Kan läsa alla övervakningsdata. |
| Resursprincipdeltagare (förhandsgranskning) | Återuppfyllda användare från EA, med behörighet att skapa/ändra resursprincip, skapa supportbiljett och läsa resurs/hierarki.  |
| [Administratör för användaråtkomst](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Tillåter en användare att hantera åtkomst för andra användare till Azure-resurser.|
| [Webbplatsdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Gör att du kan hantera webbplatser (inte webbplaner), men inte tillgång till dem..|

"Redigering" omfattar att skapa, ta bort och uppdatera:

* Resurser
* Webbtester
* Aviseringar
* Löpande export

#### <a name="select-the-user"></a>Välj användaren

Om användaren du vill använda inte finns i katalogen kan du bjuda in alla med ett Microsoft-konto.
(Om de använder tjänster som Outlook.com, OneDrive, Windows Phone eller Xbox Live har de ett Microsoft-konto.)

## <a name="related-content"></a>Relaterat innehåll

* [Rollbaserad åtkomstkontroll i Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>PowerShell-fråga för att avgöra rollmedlemskap

Eftersom vissa roller kan kopplas till meddelanden och e-postaviseringar kan det vara bra att kunna generera en lista över användare som tillhör en viss roll. För att hjälpa till med att generera dessa typer av listor erbjuder vi följande exempelfrågor som kan justeras för att passa dina specifika behov:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Fråga hela prenumerationen för administratörsroller + deltagarroller

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Fråga inom ramen för en specifik Application Insights-resurs för ägare och deltagare

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Fråga inom ramen för en specifik resursgrupp för ägare och deltagare

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
