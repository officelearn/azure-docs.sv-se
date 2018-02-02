## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
Skapa en IoT Hub som din simulerade enhetsapp kan ansluta till. Följande steg visar hur du kan slutföra den här åtgärden med hjälp av Azure Portal.

1. Logga in på [Azure Portal][lnk-portal].

1. Välj **Ny** > **Sakernas Internet** > **IoT Hub**.
   
    ![Jumpbar i Azure Portal][1]

1. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

   * **Namn**: Skapa ett namn för din IoT Hub. Om namnet som du anger är giltigt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Pris och skalnivå**: För den här självstudien väljer du nivån **F1 – Kostnadsfri**. Mer information finns i avsnittet om [pris- och skalnivåer][lnk-pricing].

   * **Resursgrupp**: Skapa en resursgrupp som ska vara värd för IoT-hubben eller använd en befintlig. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser][lnk-resource-groups]

   * **Plats**: Välj den plats som är närmast dig.

   * **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

    ![Fönstret IoT hub][2]

1. Klicka på **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelandefönstret**.

1. När din nya IoT-hubb är klar klickar du på dess panelen i Azure portal för att öppna fönstret dess egenskaper. Nu när du har skapat en IoT-hubb kan hitta viktig information som du använder för att ansluta enheter och program till din IoT-hubb. Anteckna **värdnamnet** och klicka sedan på **Principer för delad åtkomst**.
   
    ![Fönster för ny IoT-hubb][4]

1. I **principer för delad åtkomst**, klicka på den **iothubowner** princip, och anteckna sedan anslutningssträngen IoT-hubb i den **iothubowner** fönster. Mer information finns i avsnittet om [åtkomstkontroll][lnk-access-control] i ”utvecklarhandboken för IoT Hub”.
   
    ![Principer för delad åtkomst][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
