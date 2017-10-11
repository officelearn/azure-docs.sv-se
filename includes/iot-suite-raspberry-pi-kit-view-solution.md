## <a name="view-the-solution-dashboard"></a>Visa instrumentpanelen för lösningen

På instrumentpanelen för lösningen kan du hantera den distribuerade lösningen. Du kan till exempel visa telemetri, Lägg till enheter och anropa metoder.

1. När etableringen har slutförts och panelen för din förkonfigurerade lösning visar statusen **Klar** klickar du på **Starta** så öppnas portalen för fjärrövervakningslösningen på en ny flik.

    ![Starta den förkonfigurerade lösningen][img-launch-solution]

1. Som standard visar lösningsportalen *instrumentpanelen*. Du kan navigera till andra delar av lösningsportalen via menyn till vänster på sidan.

    ![Instrumentpanel för den förkonfigurerade fjärrövervakningslösningen][img-menu]

## <a name="add-a-device"></a>Lägg till en enhet

För att en enhet ska kunna ansluta till den förkonfigurerade lösningen måste den identifiera sig för IoT Hub med giltiga autentiseringsuppgifter. Du kan hämta enhetens autentiseringsuppgifter från lösningens instrumentpanel. Du kan inkludera enhetsautentiseringsuppgifterna i klientprogrammet senare i den här självstudien.

Om du inte redan gjort det, lägger du till en anpassad enhet din fjärranslutna övervakningslösning. Utför följande steg i instrumentpanelen för lösningen:

1. Klicka på **Lägg till en enhet** i det nedre vänstra hörnet på instrumentpanelen.

   ![Lägg till en enhet][1]

1. I panelen **Anpassad enhet** klickar du på **Lägg till ny**.

   ![Lägg till en anpassad enhet][2]

1. Välj **Låt mig ange mitt eget enhets-ID**. Ange ett enhets-ID som **rasppi**, klickar du på **kontrollera ID** att verifiera att du inte redan använt namnet i din lösning och klicka sedan på **skapa** att etablera enheten.

   ![Lägg till enhets-ID][3]

1. Notera enheten autentiseringsuppgifter (**enhets-ID**, **IoT-hubb Hostname**, och **enhetsnyckel**). Ditt klientprogram på hallon Pi måste dessa värden för att ansluta till den fjärranslutna övervakningslösning. Klicka sedan på **Klar**.

    ![Visa enhetsautentiseringsuppgifter][4]

1. Välj enheten i enhetslistan i lösningens instrumentpanel. Klicka sedan på **Aktivera enhet** i panelen **Enhetsinformation**. Statusen för din enhet är nu **Körs**. Fjärrövervakningslösningen kan nu ta emot telemetri från enheten och anropa metoder på enheten.

[img-launch-solution]: media/iot-suite-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-raspberry-pi-kit-view-solution/suite3.png