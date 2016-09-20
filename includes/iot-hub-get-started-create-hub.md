## Skapa en IoT-hubb

Skapa en IoT-hubb som din simulerade enhet kan ansluta till. Följande steg visar hur du kan slutföra den här åtgärden med hjälp av Azure-portalen.

1. Logga in på [Azure-portalen][lnk-portal].

2. I Jumpbar klickar du på **Ny** > **Sakernas Internet** > **Azure IoT Hub**.

    ![Jumpbar i Azure-portalen][1]

3. Välj konfigurationen för din IoT-hubb i **IoT-hubb**-bladet.

    ![IoT-hubb-bladet][2]

    * I **Namn**-rutan anger du ett namn för din IoT-hubb. Om **namnet** är giltigt och tillgängligt, visas en grön kryssmarkering i **Namn**-rutan.
    * Välj en [prissättnings- och skalningsnivå][lnk-pricing]. Den här guiden kräver inte en specifik nivå. Använd den kostnadsfria F1-nivån för den här guiden.
    * I **Resursgrupp** skapar du en ny resursgrupp eller väljer en befintlig. Mer information finns i [Hantera Azure-resurser med hjälp av resursgrupper][lnk-resource-groups].
    * I **Plats** väljer du platsen som ska vara som värd för din IoT-hubb. Välj den plats som är närmast dig för den här guiden.

4. När du har valt dina konfigurationsalternativ för IoT-hubben, klickar du på **Skapa**.  Det kan ta några minuter för Azure för att skapa din IoT-hubb. Du kan kontrollera statusen genom att övervaka förloppet på Startsidan eller i Meddelandepanelen.

    ![Status för ny IoT-hubb][3]

5. När IoT-hubben har skapats, klickar du på den nya ikonen för din IoT-hubb i portalen för att öppna bladet för den. Anteckna **värdnamnet** och klicka sedan på **Principer för delad åtkomst**.

    ![Ny IoT-hubb-blad][4]

6. I bladet **Principer för delad åtkomst** klickar du på **iothubowner**-principen och kopierar samt antecknar anslutningssträngen i **iothubowner**-bladet. Mer information finns i [Åtkomstkontroll][lnk-access-control] i "Utvecklarguide för Azure IoT Hub."

    ![Bladet Principer för delad åtkomst][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide.md#accesscontrol



<!--HONumber=sep16_HO1-->


