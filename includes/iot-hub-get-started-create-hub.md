## <a name="create-an-iot-hub"></a>Skapa en IoT Hub
Skapa en IoT Hub som din simulerade enhetsapp kan ansluta till. Följande steg visar hur du kan slutföra den här åtgärden med hjälp av Azure Portal.

1. Logga in på [Azure Portal][lnk-portal].
1. I Jumpbar klickar du på **Ny** > **Sakernas Internet** > **IoT Hub**.
   
    ![Jumpbar i Azure Portal][1]
1. Välj konfigurationen för din IoT Hub i **IoT Hub**-bladet.
   
    ![IoT Hub-bladet][2]
   
   1. I **Namn**-rutan anger du ett namn för din IoT Hub. Om **namnet** är giltigt och tillgängligt, visas en grön kryssmarkering i **Namn**-rutan.
   1. Välj en [pris- och skalningsnivå][lnk-pricing]. Den här guiden kräver inte en specifik nivå. Använd den kostnadsfria F1-nivån för den här guiden.
   1. I **Resursgrupp** skapar du antingen en resursgrupp eller väljer en befintlig. Mer information finns i [Using resource groups to manage your Azure resources][lnk-resource-groups] (Hantera dina Azure-resurser med hjälp av resursgrupper).
   1. I **Plats** väljer du platsen som ska vara som värd för din IoT Hub. Välj den plats som är närmast dig för den här guiden.
1. När du har valt dina konfigurationsalternativ för IoT Hub, klickar du på **Skapa**.  Det kan ta några minuter för Azure för att skapa din IoT Hub. Du kan kontrollera statusen genom att övervaka förloppet på Startsidan eller i Meddelandepanelen.
   
    ![Status för ny IoT Hub][3]
1. När IoT Hub har skapats klickar du på den nya ikonen för din IoT Hub i Azure Portal för att öppna bladet för den. Anteckna **värdnamnet** och klicka sedan på **Principer för delad åtkomst**.
   
    ![Ny IoT Hub-blad][4]
1. I bladet **Principer för delad åtkomst** klickar du på **iothubowner**-principen och kopierar samt antecknar IoT Hub-anslutningssträngen i **iothubowner**-bladet. Mer information finns i avsnittet om [åtkomstkontroll][lnk-access-control] i ”utvecklarhandboken för IoT Hub”.
   
    ![Bladet Principer för delad åtkomst][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
