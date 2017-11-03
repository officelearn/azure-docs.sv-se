## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
Skapa en IoT Hub som din simulerade enhetsapp kan ansluta till. Följande steg visar hur du kan slutföra den här åtgärden med hjälp av Azure Portal.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Nu när du har skapat en IoT-hubb kan hitta viktig information som du använder för att ansluta enheter och program till din IoT-hubb. 

1. När IoT-hubben har skapats klickar du på den nya ikonen för IoT-hubben i Azure-portalen för att öppna egenskapsfönstret för den. Anteckna **värdnamnet** och klicka sedan på **Principer för delad åtkomst**.
   
    ![Fönster för ny IoT-hubb][4]
1. I **Principer för delad åtkomst** klickar du på **iothubowner**-principen och kopierar samt antecknar IoT Hub-anslutningssträngen i fönstret **iothubowner**. Mer information finns i avsnittet om [åtkomstkontroll][lnk-access-control] i ”utvecklarhandboken för IoT Hub”.
   
    ![Principer för delad åtkomst][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
