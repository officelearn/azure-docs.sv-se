## <a name="view-the-telemetry"></a>Visa telemetrin

Hallon Pi nu skicka telemetri till fjärranslutna övervakningslösning. Du kan visa telemetrin på instrumentpanelen för lösningen. Du kan också skicka meddelanden till din hallon Pi från instrumentpanelen lösning.

- Gå till instrumentpanelen lösning.
- Välj din enhet i den **enhet för att visa** listrutan.
- Telemetri från hallon Pi visas på instrumentpanelen.

![Visa telemetri från hallon Pi][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>Initiera firmware-uppdatering

Den inbyggda programvara uppdateringen hämtas och installeras en uppdaterad version av klientprogrammet enheten på hallon Pi. Mer information om uppdateringen inbyggd programvara finns i beskrivningen av inbyggd programvara update mönstret i [översikt över hantering av enheter med IoT-hubben][lnk-update-pattern].

Du startar uppdateringen firmware genom att anropa en metod på enheten. Den här metoden är asynkron och returnerar så fort börjar uppdateringsprocessen. Enheten använder rapporterade egenskaper för att meddela lösning om förloppet för uppdateringen.

Du kan anropa metoder på din hallon Pi från instrumentpanelen lösning. När hallon Pi först ansluter till den fjärranslutna övervakningslösning skickar information om hur den stöder. 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
