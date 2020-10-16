---
title: Hantera IoT Central program mässigt | Microsoft Docs
description: 'Den här artikeln beskriver hur du skapar och hanterar IoT Central program mässigt. Du kan visa, ändra och ta bort programmet med flera språk-SDK: er, till exempel Java Script, python, C#, ruby och go.'
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: 773793c7681890098fea1a37cc5b9912c0ecb75c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123002"
---
# <a name="manage-iot-central-programmatically"></a>Hantera IoT Central program mässigt

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central program på webbplatsen för [azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du hantera dina program program mässigt med hjälp av Azure SDK: er. Språk som stöds är Java Script, python, C#, ruby och go.

## <a name="install-the-sdk"></a>Installera SDK:n

I följande tabell visas SDK-databaserna och paket installations kommandona:

| SDK-lagringsplats | Installera paket |
| -------------- | ------------ |
| [Azure IotCentralClient SDK för Java Script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Microsoft Azure SDK för python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Azure SDK för .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Microsoft Azure SDK för ruby-resurs hantering (för hands version)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Maven-paket](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK för Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Paket versioner](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>Exempel

[Azure IoT Central arm SDK-exemplen](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) innehåller kod exempel för flera programmeringsspråk som visar hur du skapar, uppdaterar, listar och tar bort Azure IoT Central-program.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central program program mässigt är ett förslag till nästa steg att lära dig mer om tjänsten [Azure Resource Manager](../../azure-resource-manager/management/overview.md) .