## <a name="view-device-telemetry"></a>Visa enhetstelemetri

Du kan visa telemetri som skickas från enheten på den **enheter** sida i lösningen.

1. Välj den enhet som du har etablerats i listan över enheter på den **enheter** sidan. En panel visar information om enheten till exempel ett diagram där enheten telemetri:

    ![Se detaljer för enhet](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Välj **tryck** ändra visningen av telemetri:

    ![Visa trycket telemetri](media/iot-suite-visualize-connecting/devicespressure.png)

1. Om du vill visa diagnostisk information om enheten rulla ned till **diagnostik**:

    ![Visa enheten diagnostik](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Fungerar på din enhet

Om du vill anropa metoder på dina enheter, använder den **enheter** sida i fjärranslutna övervakningslösning. Till exempel i fjärranslutna övervakningslösning **kylaggregat** enheter implementera en **omstart** metod.

1. Välj **enheter** att navigera till den **enheter** sida i lösningen.

1. Välj den enhet som du har etablerats i listan över enheter på den **enheter** sidan:

    ![Välj den fysiska enheten](media/iot-suite-visualize-connecting/devicesselect.png)

1. Om du vill visa en lista över de metoder som du kan anropa på enheten, Välj **schema**. Du kan välja flera enheter i listan om du vill schemalägga en metod för att köras på flera enheter. Den **schema** panelen visas typerna av metoden som är gemensamma för alla enheter som du har valt.

1. Välj **omstart**, ange Jobbnamnet på **RebootPhysicalChiller**, och välj **Verkställ**:

    ![Schemalägga omstarten](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Ett meddelande visas i konsolen köra din kod för enheten när enheten hanterar metoden.

> [!NOTE]
> För att spåra status för jobb i lösningen, Välj **visa**.

## <a name="next-steps"></a>Nästa steg

Artikeln [anpassa fjärråtkomst övervakning förkonfigurerade lösningen](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) beskrivs några sätt att anpassa den förkonfigurerade lösningen.