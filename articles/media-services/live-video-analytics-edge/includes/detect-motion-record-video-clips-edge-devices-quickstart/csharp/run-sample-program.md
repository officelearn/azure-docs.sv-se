---
ms.openlocfilehash: 93c88f34e32e2057efd3eae25b1f41f58b948575
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682351"
---
1. Starta en felsökningssession genom att välja F5-nyckeln. **Terminalfönstret** skriver ut vissa meddelanden.
1. *operations.js* koden anropar de direkta metoderna `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter tidigare snabb starter kommer den här processen att returnera tomma listor och sedan pausa. Välj Retur.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
  
  **Terminalfönstret** visar nästa uppsättning med direkta metod anrop:  
  
  * Ett anrop till `GraphTopologySet` som använder `topologyUrl` 
  * Ett anrop till `GraphInstanceSet` som använder följande text:
  
  ```
  {
    "@apiVersion": "1.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
        }
      ]
    }
  }
  ```
    
  * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och film flödet.
  * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd.
1. Utdata i **terminalfönstret** pausas vid `Press Enter to continue` . Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder som du har anropat.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurserna:

    * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
    * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
    * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
    * Ett sista anrop till `GraphTopologyList` visar att listan nu är tom.
