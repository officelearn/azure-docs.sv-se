---
ms.openlocfilehash: ed7be82146f38cc7ae57fd863bb0c1b8e6910fd2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691180"
---
Följ de här stegen för att köra exempel koden:

1. I Visual Studio Code går du till *src/Cloud-to-Device-console-app/operations.jspå*.
1. Se till att du ser följande värde på **GraphTopologySet** -noden:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. På **GraphInstanceSet** -och **GraphTopologyDelete**  -noderna kontrollerar du att värdet för `topologyName` motsvarar värdet för `name` egenskapen i graf-topologin:

    `"topologyName" : "MotionDetection"`
    
1. Starta en felsökningssession genom att välja F5-nyckeln. I **terminalfönstret** visas vissa meddelanden.
1. *operations.jspå* filen börjar med anrop till `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter att du har avslutat tidigare snabb starter, returnerar den här processen tomma listor och pausar sedan. Fortsätt genom att välja retur nyckeln.

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
     * Ett anrop till `GraphTopologySet` som använder föregående `topologyUrl`
     * Ett anrop till `GraphInstanceSet` som använder följande text:
         
    ```
    {
      "@apiVersion": "1.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
1. Utdata i **terminalfönstret** pausas vid `Press Enter to continue` . Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurser:

    * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
    * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
    * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
    * Ett sista anrop till `GraphTopologyList` visar att listan är tom.
