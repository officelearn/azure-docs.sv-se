---
title: Hantera ditt Azure IoT Central-program | Microsoft Docs
description: Som administratör kan du hantera ditt Azure IoT Central-program genom att ändra program namn, URL, ladda upp avbildning, kopiera och ta bort ett program
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4c729d2502464df021df4d03e989d489b2d1ecea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954596"
---
# <a name="manage-your-iot-central-application"></a>Hantera ditt IoT Central program

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur du, som administratör, kan hantera program genom att ändra program namn och URL, ladda upp avbildning, och du kan också lära dig hur du kopierar och tar bort ett program i ditt Azure IoT Central-program.

För att få åtkomst till och använda avsnittet **Administration** måste du ha rollen **administratör** för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt rollen som **administratör** för programmet. 

## <a name="change-application-name-and-url"></a>Ändra program namn och URL

På sidan **program inställningar** kan du ändra namnet och URL: en för ditt program och sedan välja **Spara**.

![Sidan program inställningar](media/howto-administer/image0-a.png)

Om administratören skapar ett anpassat tema för ditt program, innehåller den här sidan ett alternativ för att dölja **program namnet** i användar gränssnittet. Detta är användbart om program logo typen i det anpassade temat innehåller programmets namn. Mer information finns i [Anpassa Azure IoT Central UI](./howto-customize-ui.md).

> [!Note]
> Om du ändrar din URL kan din gamla URL tas av en annan Azure IoT Central-kund. Om detta händer är det inte längre tillgängligt för dig att använda. När du ändrar din URL fungerar inte längre den gamla URL: en och du måste meddela användarna om den nya URL: en som ska användas.

## <a name="prepare-and-upload-image"></a>Förbereda och ladda upp avbildning

Information om hur du ändrar program avbildningen finns i [förbereda och ladda upp bilder till ditt Azure IoT Central-program](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopiera ett program

Du kan skapa en kopia av alla program, minus enhets instanser, enhets data historik och användar data. Kopieringen är ett program som du betalar per användning som du debiteras för. Du kan inte skapa ett utvärderings program på det här sättet.

Välj **Kopiera**. I dialog rutan anger du information för det nya programmet betala per användning. Välj sedan **Kopiera** för att bekräfta att du vill fortsätta. Lär dig mer om fälten i det här formuläret i skapa snabb start för [program](quick-deploy-iot-central.md) .

![Sidan program inställningar](media/howto-administer/appcopy2.png)

När kopieringen av appen lyckas kan du navigera till det nya programmet med hjälp av länken.

![Sidan program inställningar](media/howto-administer/appcopy3a.png)

När du kopierar ett program kopieras även definitionen av regler och e-poståtgärden. Vissa åtgärder som Flow, Logic Apps osv. är knutna till vissa regler via regel-ID: t. När en regel kopieras till ett annat program får den ett eget regel-ID. I det här fallet måste användarna skapa en ny åtgärd och sedan koppla den nya regeln till den. I allmänhet är det en bra idé att kontrol lera reglerna och åtgärderna för att se till att de är uppdaterade i den nya appen.

> [!WARNING]
> Om en instrument panel innehåller paneler som visar information om specifika enheter visas **inte den begärda resursen** i det nya programmet. Du måste konfigurera om panelerna för att visa information om enheter i det nya programmet.

## <a name="delete-an-application"></a>Ta bort ett program

Använd knappen **ta bort** för att ta bort ditt IoT Central program permanent. Den här åtgärden tar permanent bort alla data som är associerade med programmet.

> [!Note]
> Om du vill ta bort ett program måste du också ha behörighet att ta bort resurser i Azure-prenumerationen som du valde när du skapade programmet. Läs mer i [använda rollbaserad åtkomst kontroll för att hantera åtkomst till dina Azure-prenumerations resurser](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Hantera program mässigt

IoT Central Azure Resource Manager SDK-paket är tillgängliga för Node, python C#, ruby, Java och go. Du kan använda paketen för att skapa, Visa, uppdatera eller ta bort IoT Central program. Paketen innehåller hjälp program för hantering av autentiserings-och fel hantering.

Du hittar exempel på hur du kan använda Azure Resource Manager SDK: er på [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Mer information finns i följande GitHub-databaser och paket:

| Språk | Lagrings platsen | Paket |
| ---------| ---------- | ------- |
| Nod | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Nästa steg
 
Nu när du har lärt dig hur du administrerar ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig mer om att [Hantera användare och roller](howto-manage-users-roles.md) i Azure IoT Central.