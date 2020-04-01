---
title: Skapa Namnområde och kö för Azure Service Bus med Azure-mall
description: 'Snabbstart: Skapa ett tjänstbussnamnområde och en kö med Azure Resource Manager-mall'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422645"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Snabbstart: Skapa ett tjänstbussnamnområde och en kö med hjälp av en Azure Resource Manager-mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar ett servicebussnamnområde och en kö inom det namnområdet. Artikeln förklarar hur du anger vilka resurser som ska distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Inget

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Skapa ett servicebussnamnområde och en kö

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

De resurser som definieras i mallen omfattar:

- [**Microsoft.ServiceBus/namnområden**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/köer**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Följande Azure Resource Manager-mallar är tillgängliga för hämtning och distribution.
>
> * [Skapa ett servicebussnamnområde med kö- och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett servicebussnamnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett servicebussnamnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)

Du hittar fler mallar från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>Distribuera mallen

Med den här mallen distribuerar du ett servicebussnamnområde med en kö.

[Service Bus köer](service-bus-queues-topics-subscriptions.md#queues) erbjuder First In, First Out (FIFO) meddelande leverans till en eller flera konkurrerande konsumenter.

Om du vill köra distributionen automatiskt klickar du på följande knapp: Skapa en ny resursgrupp för distributionen så att du enkelt kan rensa senare.

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Välj **Meddelanden** högst upp om du vill se status för distributionen. Vänta tills distributionen lyckas. Välj sedan **Gå till resursgrupp** i meddelandemeddelandet för att navigera till sidan för resursgruppen som innehåller namnområdet Service Bus. 

    ![Meddelande från distribution](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Bekräfta att du ser namnområdet Service Bus i listan över resurser. 

    ![Resursgrupp - namnområde](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Välj namnområdet i listan om du vill visa sidan **Service Bus Namespace.** 

## <a name="cleanup-resources"></a>Rensa resurser

1. Navigera till sidan **Resursgrupp** för resursgruppen i Azure-portalen.
2. Välj **Ta bort resursgrupp** i verktygsfältet. 
3. Skriv namnet på resursgruppen och välj **Ta bort**. 

    ![Resursgrupp - ta bort](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Nästa steg

Se följande avsnitt som visar hur du skapar en auktoriseringsregel för namnområdet/kön:

[Skapa en servicebussauktoriseringsregel för namnområde och kö med hjälp av en Azure Resource Manager-mall](service-bus-resource-manager-namespace-auth-rule.md)

Lär dig hur du hanterar dessa resurser genom att visa följande artiklar:

* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera servicebussresurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
