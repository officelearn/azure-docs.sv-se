1. Logga in på [Azure Portal][lnk-portal].
1. Välj **skapar du en resurs** > **Sakernas Internet** > **IoT-hubb**.
   
    ![Skärmbild av Azure portal navigering till IoT-hubb][1]

1. I rutan **IoT-hubb** anger du följande information för IoT-hubben:

   * **Namn**: Skapa ett namn för din IoT Hub. Om namnet som du anger är giltigt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Pris och skalnivå**: För den här självstudien väljer du nivån **F1 – Kostnadsfri**. Mer information finns i avsnittet om [pris- och skalnivåer][lnk-pricing].

   * **Resursgrupp**: Skapa en resursgrupp som ska vara värd för IoT-hubben eller använd en befintlig. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser][lnk-resource-groups]

   * **Plats**: Välj den plats som är närmast dig.

   * **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

    ![Fönstret IoT hub][2]

1. Klicka på **Skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelandefönstret**.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md