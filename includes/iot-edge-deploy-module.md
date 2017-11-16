En av de viktigaste funktionerna i Azure IoT kant är att kunna distribuera moduler till IoT-gränsenheterna från molnet. En IoT-Edge-modul är en körbar paket som implementeras som en behållare. I det här avsnittet kan du distribuera en modul som genererar telemetri för den simulerade enheten. 

1. Navigera till din IoT-hubb i Azure-portalen.
1. Gå till **IoT kant (förhandsgranskning)** och välj IoT-Edge-enhet.
1. Välj **ange moduler**.
1. Välj **lägga till IoT kant modul**.
1. I den **namn** anger `tempSensor`. 
1. I den **avbildningen URI** anger `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Lämna de andra inställningarna oförändrade och välj **spara**.
1. I den **lägga till moduler** steg, Välj **nästa**.
1. I den **ange vägar** steg, Välj **nästa**.
1. I den **granska mallen** steg, Välj **skicka**.
1. Gå tillbaka till informationssidan om enheten och välj **uppdatera**. Du bör se den nya tempSensor-modulen kör längs IoT kant-körningsmiljön. 

   ![Visa tempSensor i listan över installerade moduler][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png