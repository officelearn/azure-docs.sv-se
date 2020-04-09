---
title: Migrera logikappar över prenumerationer, resursgrupper eller regioner
description: Migrera logikappar eller integrationskonton till andra Azure-prenumerationer, resursgrupper eller platser (regioner)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878739"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Flytta logikappresurser till andra Azure-resursgrupper, regioner eller prenumerationer

Om du vill migrera logikappen eller relaterade resurser till en annan Azure-resursgrupp, region eller prenumeration har du olika sätt att slutföra dessa uppgifter, till exempel Azure-portalen, Azure PowerShell, Azure CLI och REST API. Innan du flyttar resurser bör du granska följande: 

* Du kan bara flytta [specifika logikappresurstyper](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) mellan Azure-resursgrupper eller prenumerationer.

* Kontrollera [gränserna för](../logic-apps/logic-apps-limits-and-config.md) antalet logikappresurser som du kan ha i din Azure-prenumeration och i varje Azure-region. Dessa gränser påverkar om du kan flytta specifika resurstyper när regionen förblir densamma för prenumerationer eller resursgrupper. Du kan till exempel bara ha ett konto för integrering på den kostnadsfria nivån för varje Azure-region i varje Azure-prenumeration.

* När du flyttar resurser skapar Azure nya resurs-ID:n. Så se till att du använder de nya ID:na i stället och uppdatera skript eller verktyg som är associerade med de flyttade resurserna.

* När du har migrerat logikappar mellan prenumerationer, resursgrupper eller regioner måste du återskapa eller återauktorisera alla anslutningar som kräver öppen autentisering (OAuth).

* Du kan bara flytta en [integrationstjänstmiljö (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) till en annan resursgrupp som finns i samma Azure-region eller Azure-prenumeration. Du kan inte flytta en ISE till en resursgrupp som finns i en annan Azure-region eller Azure-prenumeration. Efter ett sådant drag måste du också uppdatera alla referenser till ISE i logikapparbetsflödena, integrationskonton, anslutningar och så vidare.

## <a name="prerequisites"></a>Krav

* Samma Azure-prenumeration som användes för att skapa logikappen eller integrationskontot som du vill flytta

* Resursägarebehörigheter för att flytta och ställa in de resurser du vill ha. Läs mer om [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Flytta resurser mellan prenumerationer

Om du vill flytta en resurs, till exempel en logikapp eller ett integrationskonto, till en annan Azure-prenumeration kan du använda Azure-portalen, Azure PowerShell, Azure CLI eller REST API. De här stegen täcker Azure-portalen, som du kan använda när resursens region förblir densamma. Andra steg och allmänna förberedelser finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. Leta reda på och välj den logikappresurs som du vill flytta i [Azure-portalen.](https://portal.azure.com)

1. Välj ändringslänken bredvid **Prenumeration**på **change** **resursens** översiktssida.

1. På sidan **Flytta resurser** väljer du logikappresursen och eventuella relaterade resurser som du vill flytta.

1. Välj målprenumerationen i listan **Prenumeration.**

1. Välj målresursgruppen i listan **Resursgrupp.** Om du vill skapa en annan resursgrupp väljer du **Skapa en ny grupp**.

1. Om du vill bekräfta att skript eller verktyg som är associerade med de flyttade resurserna inte fungerar förrän du uppdaterar dem med de nya resurs-ID:na markerar du bekräftelserutan och väljer sedan **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Flytta resurser mellan resursgrupper

Om du vill flytta en resurs, till exempel en logikapp, ett integrationskonto eller [en integrationstjänstmiljö (ISE),](connect-virtual-network-vnet-isolated-environment-overview.md)kan du använda Azure-portalen, Azure PowerShell, Azure CLI eller REST API. De här stegen täcker Azure-portalen, som du kan använda när resursens region förblir densamma. Andra steg och allmänna förberedelser finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Innan du flyttar resurser mellan grupper kan du testa om du kan flytta resursen till en annan grupp. Mer information finns i [Validera flytten](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. Leta reda på och välj den logikappresurs som du vill flytta i [Azure-portalen.](https://portal.azure.com)

1. Välj ändringslänken bredvid **Resursgrupp**på **change** **resursens** översiktssida.

1. På sidan **Flytta resurser** väljer du logikappresursen och eventuella relaterade resurser som du vill flytta.

1. Välj målresursgruppen i listan **Resursgrupp.** Om du vill skapa en annan resursgrupp väljer du **Skapa en ny grupp**.

1. Om du vill bekräfta att skript eller verktyg som är associerade med de flyttade resurserna inte fungerar förrän du uppdaterar dem med de nya resurs-ID:na markerar du bekräftelserutan och väljer sedan **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Flytta resurser mellan regioner

När du vill flytta en logikapp till en annan region beror dina alternativ på hur du skapade logikappen. Baserat på det alternativ du väljer måste du återskapa eller återauktorisera anslutningarna i logikappen.

* Återskapa logikappen i den nya regionen i Azure-portalen och konfigurera om arbetsflödesinställningarna. Om du vill spara tid kan du kopiera den underliggande arbetsflödesdefinitionen och anslutningarna från källappen till målappen. Om du vill visa "koden" bakom en logikapp väljer du **kodvyn**i verktygsfältet Logikappdesigner .

* Genom att använda Visual Studio och Azure Logic Apps Tools för Visual Studio kan du [öppna och ladda ned logikappen](../logic-apps/manage-logic-apps-with-visual-studio.md) från Azure-portalen som en Azure Resource [Manager-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Den här mallen är oftast klar för distribution och innehåller resursdefinitioner för logikappen, inklusive själva arbetsflödet och anslutningar. Mallen deklarerar också parametrar för de värden som ska användas vid distributionen. På så sätt kan du enklare ändra var och hur du distribuerar logikappen, baserat på dina behov. Om du vill ange plats och annan nödvändig information för distributionen kan du använda en separat parameterfil.

* Om du har skapat och distribuerat logikappen med hjälp av verktyg för kontinuerlig integrering (CI) och cd-verktyg (continuous delivery), till exempel Azure Pipelines i Azure DevOps, kan du distribuera din app till en annan region med hjälp av dessa verktyg.

Mer information om distributionsmallar för logikappar finns i följande avsnitt:

* [Översikt: Automatisera distributionen för Azure Logic Apps med hjälp av Azure Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Hitta, öppna och ladda ned logikappen från Azure-portalen till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Skapa Azure Resource Manager-mallar för Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Distribuera Azure Resource Manager-mallar för Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Relaterade resurser

Vissa Azure-resurser, till exempel lokala datagatewayresurser i Azure, kan finnas i en region som skiljer sig från logikapparna som använder dessa resurser. Andra Azure-resurser, till exempel länkade integrationskonton, måste dock finnas i samma region som dina logikappar. Baserat på ditt scenario kontrollerar du att dina logikappar kan komma åt de resurser som dina appar förväntar sig att finnas i samma region.

Om du till exempel vill länka en logikapp till ett integrationskonto måste båda resurserna finnas i samma region. I scenarier som haveriberedskap vill du vanligtvis ha integrationskonton som har samma konfiguration och artefakter. I andra scenarier kan du behöva integrationskonton med olika konfigurationer och artefakter.

Anpassade kopplingar i Azure Logic Apps är synliga för kopplingarnas författare och användare som har samma Azure-prenumeration och samma Azure Active Directory-klient. Dessa anslutningsappar är tillgängliga i samma region där logikappar distribueras. Mer information finns i [Dela anpassade anslutningsappar i organisationen](https://docs.microsoft.com/connectors/custom-connectors/share).

Mallen som du får från Visual Studio innehåller endast resursdefinitioner för logikappen och dess anslutningar. Så om logikappen använder andra resurser, till exempel ett integrationskonto och B2B-artefakter, till exempel partner, avtal och scheman, måste du exportera det integrationskontots mall med hjälp av Azure-portalen. Den här mallen innehåller resursdefinitioner för både integrationskontot och artefakter. Mallen är dock inte helt parameteriserad. Därför måste du manuellt parameterisera de värden som du vill använda för distribution.

### <a name="export-templates-for-integration-accounts"></a>Exportera mallar för integrationskonton

1. Leta reda på och öppna ditt integrationskonto i [Azure-portalen.](https://portal.azure.com)

1. Välj Exportera mall under **Inställningar**på integrationskontots **meny**.

1. Välj **Hämta**i verktygsfältet och spara mallen.

1. Öppna och redigera mallen för att parameterisera de nödvändiga värdena för distributionen.

## <a name="next-steps"></a>Nästa steg

[Flytta Azure-resurser till nya resursgrupper eller prenumerationer](../azure-resource-manager/management/move-resource-group-and-subscription.md)
