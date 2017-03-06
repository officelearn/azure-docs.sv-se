## <a name="view-device-telemetry-in-the-dashboard"></a>Visa enhetstelemetri i instrumentpanelen
Instrumentpanelen i fjärrövervakningslösningen visar den telemetri som enheterna skickar till IoT Hub.

1. Gå tillbaka till fjärrövervakningslösningens instrumentpanel i webbläsaren och klicka på **Enheter** i den vänstra panelen för att navigera till **Enhetslistan**.
2. I **enhetslistan** bör du se att statusen för din enhet är **Körs**. Om den inte körs klickar du på **Aktivera enhet** i panelen **Enhetsinformation**.
   
    ![Visa enhetsstatus][18]
3. Klicka på **Instrumentpanel** för att gå tillbaka till instrumentpanelen och välj din enhet i listrutan **Enhet att visa** för att se dess telemetri. Telemetrin från exempelprogrammet är 50 enheter för intern temperatur, 55 enheter för extern temperatur och 50 enheter för fuktighet.
   
    ![Visa enhetstelemetri][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Anropa en metod på enheten
Instrumentpanelen i fjärrövervakningslösningen låter dig anropa metoder på enheterna genom IoT Hub. Du kan till exempel anropa en metod för att simulera en omstart av en enhet i fjärrövervakningslösningen.

1. I fjärrövervakningslösningens instrumentpanel klickar du på **Enheter** i den vänstra panelen för att navigera till **Enhetslistan**.
2. Klicka på din enhets **Enhets-ID** i **enhetslistan**.
3. I panelen **Enhetsinformation** klickar du på **Metoder**.
   
    ![Enhetsmetoder][13]
4. I listrutan **Metod**väljer du **InitiateFirmwareUpdate** och sedan anger du en dummy-URL i **FWPACKAGEURI**. Klicka på **Anropa metod** för att anropa metoden på enheten.
   
    ![Anropa en enhetsmetod][14]
   

5. Du ser ett meddelande i konsolen som kör enhetskoden när enheten hanterar metoden. Resultaten av metoden läggs till i historiken i lösningsportalen:

    ![Visa metodhistorik][img-method-history]

## <a name="next-steps"></a>Nästa steg
Artikeln [Anpassning av förkonfigurerade lösningar][lnk-customize] beskriver några sätt du kan utöka det här exemplet. Möjliga tillägg kan vara att använda riktiga sensorer och att implementera ytterligare kommandon.

Du kan lära dig mer om [behörigheter på webbplatsen azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
