---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 07/14/2020
ms.openlocfilehash: b47580ad3aa14cee3ea3658ecd3b921e8e738882
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352809"
---
## <a name="download-the-model"></a>Ladda ned modellen

Senare använder du Azure IoT Explorer för att visa enheten när den ansluter till din IoT-hubb. Azure IoT Explorer behöver en lokal kopia av modell filen som matchar det **modell-ID** som din enhet skickar. Med modell filen kan Azure IoT Explorer Visa telemetri, egenskaper och kommandon som enheten implementerar.

Om du inte redan har hämtat exempel modellens filer:

1. Skapa en mapp med namnet *modeller* på den lokala datorn.

1. Högerklicka på [TemperatureController.jspå](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) och spara JSON-filen i mappen *modeller* .

1. Högerklicka på [Thermostat.jspå](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) och spara JSON-filen i mappen *modeller* .
