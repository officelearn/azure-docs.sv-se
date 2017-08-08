## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

1. På [Azure-portalen](https://portal.azure.com/) klickar du på **Nytt** > **Sakernas Internet** > **IoT Hub**.

   ![Skapa en IoT-hubb på Azure-portalen](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

     **Namn**: Ange namnet på IoT-hubben. Om namnet som du anger är giltigt visas en grön bockmarkering.

     **Pris- och skalnivå**: Välj nivån **F1 - Kostnadsfri**. Det här alternativet är tillräckligt för den här demonstrationen. Mer information finns i avsnittet om [pris- och skalnivåer](https://azure.microsoft.com/pricing/details/iot-hub/).

     **Resursgrupp**: Skapa en resursgrupp som ska vara värd för IoT-hubben eller använd en befintlig. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](../articles/azure-resource-manager/resource-group-portal.md).

     **Plats**: Välj den plats som är närmast dig där IoT-hubben ska skapas.

     **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Ange information för att skapa IoT-hubben](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. Klicka på **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan se förloppet i **meddelandefönstret**.

   ![Visa meddelanden om IoT-hubbens förlopp](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. När IoT-hubben har skapats klickar du på den på instrumentpanelen. Anteckna **värdnamnet** och klicka sedan på **Principer för delad åtkomst**.

   ![Hämta värdnamnet för IoT-hubben](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. I rutan **Policyer för delad åtkomst** klickar du på principen **iothubowner** och kopierar och skriver ner IoT-hubbens **anslutningssträng**. Mer information finns i [Control access to IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md) (Kontrollera åtkomsten till IoT Hub).

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
