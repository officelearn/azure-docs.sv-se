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

2. Klicka på **Enhetsutforskare**.
3. Klicka på **Lägg till** i rutan Enhetsutforskare för att lägga till en enhet till IoT-hubben. Gör något av följande:

   **Enhets-ID**: Ange ID:t för den nya enheten. Enhets-ID är skiftlägeskänsliga.

   **Autentiseringstyp**: Välj **Symmetrisk nyckel**.

   **Generera nycklar automatiskt**: Markera den här kryssrutan.

   **Anslut enhet till IoT Hub**: Klicka på **Aktivera**.

   ![Lägg till en enhet i Enhetsutforskaren för IoT-hubben](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Klicka på **Spara**.
5. När enheten har skapats öppnar du enheten i rutan **Enhetsutforskare**.
6. Skriv ner anslutningssträngens primära nyckel.

   ![Hämta enhetens anslutningssträng](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
