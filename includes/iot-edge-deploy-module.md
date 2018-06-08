En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera moduler till IoT Edge-enheter från molnet. En IoT Edge-modul är ett körbart paket som implementeras som en behållare. I det här avsnittet ska du distribuera en modul som genererar telemetri för din simulerade enhet. 

1. Gå till din IoT-hubb på Azure Portal.
1. Gå till **IoT Edge (förhandsversion)** och välj IoT Edge-enhet.
1. Välj **Ange moduler**.
1. Välj **Lägg till IoT Edge-modul**.
1. I **Namn**-fältet skriver du `tempSensor`. 
1. I **URI för avbildning**-fältet skriver du `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Lämna de andra inställningarna som de är och välj **Spara**.

   ![Spara IoT Edge-modulen när du har angett namn och bild-URI](./media/iot-edge-deploy-module/name-image.png)

1. När du är tillbaka i steget **Lägg till moduler** väljer du **Nästa**.
1. Välj **Nästa** i steget **Ange rutter**.
1. Välj **Skicka** i steget **Granska mall**.
1. Återgå till informationssidan om enheten och välj **Uppdatera**. Nu bör du se den nya tempSensor-modulen som körs tillsammans med IoT Edge-körningen. 

   ![Visa tempSensor i listan med distribuerade moduler][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png