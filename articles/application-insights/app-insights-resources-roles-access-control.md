---
title: "Resurser, roller och åtkomst kontroll i Azure Application Insights | Microsoft Docs"
description: "Ägare, deltagare och läsare av din organisations insikter."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: e3aa1d432cf2c510c09b13d4f07833edcb1e9b53
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Resurser, roller och åtkomstkontroll i Application Insights
Du kan styra vem som har läs- och uppdatera åtkomst till dina data i Azure [Programinsikter][start], med hjälp av [rollbaserad åtkomstkontroll i Microsoft Azure](../active-directory/role-based-access-control-configure.md).

> [!IMPORTANT]
> Tilldela åtkomst till användare i den **resursgrupp eller prenumeration** som programmet resurs tillhör - inte i resursen sig själv. Tilldela den **Application Insights component contributor** roll. Detta säkerställer enhetlig kontroll över åtkomsten till webbtester och aviseringar tillsammans med din programresurs. [Läs mer](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Resurser, grupper och prenumerationer
Första vissa definitioner:

* **Resursen** - en instans av en Microsoft Azure-tjänst. Application Insights-resurs samlar in, analyseras och visar telemetridata skickas från ditt program.  Andra typer av Azure-resurser är webbappar, databaser och virtuella datorer.
  
    Om du vill se dina resurser, öppna den [Azure Portal][portal], logga in och på alla resurser. Om du vill hitta en resurs, skriver du datorns namn i filterfältet.
  
    ![Lista över Azure-resurser](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Resursgruppen** ] [ group] -alla resurser som tillhör en grupp. En grupp är ett praktiskt sätt att hantera relaterade resurser, särskilt för åtkomstkontroll. Till exempel kan en resursgrupp du placera en Webbapp Application Insights-resurs för att övervaka appen och en lagringsresurs så att exporterade data.

    ![Klicka på Bläddra, resursgrupper, och välj sedan en grupp](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Prenumerationen** ](https://manage.windowsazure.com) - om du vill använda Application Insights eller andra Azure-resurser som du loggar in på en Azure-prenumeration. Varje resursgrupp som hör till en Azure-prenumeration där du väljer pris-paketet och, om det är en organisation prenumeration, Välj medlemmar och deras behörigheter för åtkomst.
* [**Microsoft-konto** ] [ account] -användarnamn och lösenord som du använder för att logga in på Microsoft Azure-prenumerationer, XBox Live, Outlook.com och andra Microsoft-tjänster.

## <a name="access"></a>Kontrollera åtkomst i resursgruppen
Det är viktigt att förstå att förutom den resurs du skapat för ditt program, finns också separat dolda resurser för aviseringar och webbtester. De är kopplade till samma [resursgruppen](#resource-group) som ditt program. Du kan också har placerat andra Azure-tjänster i det finns till exempel webbplatser eller lagring.

![Resurser i Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Styra åtkomsten till dessa resurser bör därför att:

* Kontrollera åtkomsten till den **resursgrupp eller prenumeration** nivå.
* Tilldela den **Application Insights Component contributor** roll till användare. Detta gör att de kan redigera webbtester, aviseringar och Application Insights-resurser utan att ge tillgång till andra tjänster i gruppen.

## <a name="to-provide-access-to-another-user"></a>Att ge åtkomst till en annan användare
Du måste ha behörigheten ägare till prenumerationen eller resursgruppen.

Användaren måste ha en [Account][account], eller åtkomst till sina [Account Microsoft](../active-directory/sign-up-organization.md). Du kan ge åtkomst till enskilda personer och till användargrupper som definierats i Azure Active Directory.

#### <a name="navigate-to-the-resource-group"></a>Gå till resursgruppen.
Lägg till användaren där.

![I resursbladet för ditt program, öppna Essentials, öppna resursgruppen och välj det inställningar och användare. Klicka på Add (Lägg till).](./media/app-insights-resources-roles-access-control/01-add-user.png)

Eller gå in en annan och lägga till användaren till prenumerationen.

#### <a name="select-a-role"></a>Välj en roll
![Välj en roll för den nya användaren](./media/app-insights-resources-roles-access-control/03-role.png)

| Roll | I resursgruppen |
| --- | --- |
| Ägare |Ändra vad som helst, inklusive användaråtkomst |
| Deltagare |Redigera något, inklusive alla resurser |
| Application Insights Component contributor |Redigera Application Insights-resurser, webbtester och aviseringar |
| Läsare |Kan visa men inte ändra någonting |

'Redigera' omfattar att skapa, ta bort och uppdatera:

* Resurser
* Webbtester
* Aviseringar
* Löpande export

#### <a name="select-the-user"></a>Välj användaren

Om användaren inte är i katalogen, kan du bjuda in vem som helst med ett Microsoft-konto.
(Om de använder tjänster som Outlook.com, OneDrive, Windows Phone och XBox Live, har ett Microsoft-konto.)

## <a name="related-content"></a>Relaterat innehåll

* [Rollbaserad åtkomstkontroll i Azure](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
