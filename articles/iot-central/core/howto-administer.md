---
title: Ändra programinställningar för Azure IoT Central | Microsoft-dokument
description: Som administratör, hur du hanterar ditt Azure IoT Central-program genom att ändra programnamn, URL, ladda upp avbildning och ta bort ett program
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158697"
---
# <a name="change-iot-central-application-settings"></a>Ändra IoT Central-programinställningar



I den här artikeln beskrivs hur du som administratör kan hantera program genom att ändra programnamn och URL, ladda upp avbildning och ta bort ett program i ditt Azure IoT Central-program.

För att komma åt och använda avsnittet **Administration** måste du vara i **administratörsrollen** för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt **administratörsrollen** för det programmet.

## <a name="change-application-name-and-url"></a>Ändra programnamn och URL

På sidan **Programinställningar** kan du ändra programmets namn och URL och sedan välja **Spara**.

![Sidan Programinställningar](media/howto-administer/image0-a.png)

Om administratören skapar ett anpassat tema för ditt program innehåller den här sidan ett alternativ för att dölja **programnamnet** i användargränssnittet. Det här alternativet är användbart om programlogotypen i det anpassade temat innehåller programnamnet. Mer information finns [i Anpassa Azure IoT Central UI](./howto-customize-ui.md).

> [!Note]
> Om du ändrar webbadressen kan din gamla webbadress tas av en annan Azure IoT Central-kund. Om det händer är det inte längre tillgängligt för dig att använda. När du ändrar webbadressen fungerar inte längre den gamla webbadressen och du måste meddela användarna om den nya webbadressen som ska användas.

## <a name="delete-an-application"></a>Ta bort ett program

Använd knappen **Ta bort** för att ta bort ditt IoT Central-program permanent. Den här åtgärden tar bort alla data som är associerade med programmet permanent.

> [!Note]
> Om du vill ta bort ett program måste du också ha behörighet att ta bort resurser i den Azure-prenumeration som du valde när du skapade programmet. Mer information finns i [Använda rollbaserad åtkomstkontroll för att hantera åtkomst till dina Azure-prenumerationsresurser](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-programmatically"></a>Hantera programmässigt

IoT Central Azure Resource Manager SDK-paket är tillgängliga för Node, Python, C#, Ruby, Java och Go. Du kan använda dessa paket för att skapa, lista, uppdatera eller ta bort IoT Central-program. Paketen innehåller hjälpredatorer för att hantera autentisering och felhantering.

Du hittar exempel på hur du använder SDK:erna för Azure Resource Manager på [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Mer information finns i följande GitHub-databaser och paket:

| Språk | Lagringsplats | Paket |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om hur du administrerar ditt Azure IoT Central-program är det föreslagna nästa steget att lära dig mer om [Hantera användare och roller](howto-manage-users-roles.md) i Azure IoT Central.
