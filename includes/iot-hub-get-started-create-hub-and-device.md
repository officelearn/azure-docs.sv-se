## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Nu när du har skapat en IoT-hubb kan hitta viktig information som du använder för att ansluta enheter och program till din IoT-hubb. 

1. När IoT-hubben har skapats klickar du på den på instrumentpanelen. Anteckna **värdnamnet** och klicka sedan på **Principer för delad åtkomst**.

   ![Hämta värdnamnet för IoT-hubben](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. I rutan **Policyer för delad åtkomst** klickar du på principen **iothubowner** och kopierar och skriver ner IoT-hubbens **anslutningssträng**. Mer information finns i [Control access to IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md) (Kontrollera åtkomsten till IoT Hub).

> [!NOTE] 
Du behöver inte den här iothubowner-anslutningssträngen i den här konfigurationskursen. Men du kan behöva den i vissa självstudiekurser om olika IoT-scenarier när du har slutfört den här konfigurationen.

   ![Hämta IoT-hubbens anslutningssträng](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrera en enhet i IoT-hubben för din enhet

1. Öppna IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. Klicka på **IoT-enheter**.
3. I fönstret IoT-enheter klickar du på **Lägg till** att lägga till en enhet till din IoT-hubb. Gör något av följande:

   **Enhets-ID**: Ange ID:t för den nya enheten. Enhets-ID är skiftlägeskänsliga.

   **Autentiseringstyp**: Välj **Symmetrisk nyckel**.

   **Generera nycklar automatiskt**: Markera den här kryssrutan.

   **Anslut enhet till IoT Hub**: Klicka på **Aktivera**.

   ![Lägga till en enhet i IoT-enheter för din IoT-hubb](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-iot-devices-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Klicka på **Spara**.
5. När enheten har skapats kan du öppna enheten i den **IoT-enheter** fönstret.

   ![Listan över IoT-enheter i IoT-hubb](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_device-list-in-iot-devices-portal.png)

6. Skriv ner anslutningssträngens primära nyckel.

   ![Hämta enhetens anslutningssträng](../articles/iot-hub/media/iot-hub-create-hub-and-device/8_get-device-connection-string-in-iot-devices-portal.png)
