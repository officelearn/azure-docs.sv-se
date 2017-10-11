## <a name="view-the-telemetry"></a>Visa telemetrin

Hallon Pi nu skicka telemetri till fjärranslutna övervakningslösning. Du kan visa telemetrin på instrumentpanelen för lösningen. Du kan också skicka meddelanden till din hallon Pi från instrumentpanelen lösning.

- Gå till instrumentpanelen lösning.
- Välj din enhet i den **enhet för att visa** listrutan.
- Telemetri från hallon Pi visas på instrumentpanelen.

![Visa telemetri från hallon Pi][img-telemetry-display]

## <a name="act-on-the-device"></a>Fungerar på enheten

Du kan anropa metoder på din hallon Pi från instrumentpanelen lösning. När hallon Pi ansluter till den fjärranslutna övervakningslösning, skickar information om hur den stöder.

- I lösningen instrumentpanelen, klickar du på **enheter** besöka den **enheter** sidan. Välj din Raspberry Pi i den **enhetslistan**. Välj **metoder**:

    ![Lista över enheter i instrumentpanelen][img-list-devices]

- På den **anropa metoden** väljer **LightBlink** i den **metoden** listrutan.

- Välj **InvokeMethod**. Indikatorn är anslutna till hallon Pi gånger flera gånger. Appen på hallon Pi skickar en bekräftelse tillbaka till instrumentpanelen lösningen:

    ![Visa historiken för metoden][img-method-history]

- Du kan växla Indikatorn och inaktivera med hjälp av den **ChangeLightStatus** metod med en **LightStatusValue** inställd på **1** för på eller **0** för av.

> [!WARNING]
> Om du lämnar den fjärranslutna övervakningslösning som körs i ditt Azure-konto, debiteras du för den tid som den körs. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config]. Ta bort den förkonfigurerade lösningen från ditt Azure-konto när du är klar.


[img-telemetry-display]: media/iot-suite-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md