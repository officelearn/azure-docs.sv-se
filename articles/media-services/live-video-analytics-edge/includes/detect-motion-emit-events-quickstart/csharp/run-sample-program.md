---
ms.openlocfilehash: 2349939d4997ddc57d0c0c56a21eeec0357bf0ec
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828789"
---
Följ de här stegen för att köra exempel koden:

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj **Inställningar för tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Tilläggs inställningar"
          }
        ]
      }
    }
    ```
     
    * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och film flödet.
    * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd.
1. Utdata i **terminalfönstret** pausas vid `Press Enter to continue` . Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurser:

    * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
    * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
    * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
    * Ett sista anrop till `GraphTopologyList` visar att listan är tom.
