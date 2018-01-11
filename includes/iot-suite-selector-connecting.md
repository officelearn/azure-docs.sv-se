> [!div class="op_single_selector"]
> * [C i Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C i Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (generisk)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js på Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C på Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-c.md)

I kursen får du implementerar en **kylaggregat** enhet som skickar följande telemetri för fjärråtkomst övervakning [förkonfigurerade lösningen](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md):

* Temperatur
* Tryck
* Fuktighet

För enkelhetens skull koden genererar exempelvärden telemetri för den **kylaggregat**. Du kan utöka exemplet genom att ansluta verkliga sensorer till enheten och skicka verkliga telemetri.

Exempel enheten också:

* Skickar metadata i lösningen för att beskriva dess funktioner.
* Svarar på åtgärder som utlöses från den **enheter** sida i lösningen.
* Svarar på konfigurationsändringar skicka från den **enheter** sida i lösningen.

Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Innan du börjar

Innan du kan skriva kod för enheten måste du etablera din förkonfigurerade lösning för fjärrövervakning och etablera en ny anpassad enhet i lösningen.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Etablera din förkonfigurerade lösning för fjärrövervakning

Den **kylaggregat** enhet som du skapar i den här självstudiekursen skickar data till en instans av den [fjärrövervaknings](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) förkonfigurerade lösningen. Om du inte redan har etablerats fjärråtkomst övervakning förkonfigurerade lösningen i ditt Azure-konto, se [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

När etableringen av fjärrövervakningslösningen är klar klickar du på **Starta** för att öppna lösningens instrumentpanel i webbläsaren.

![Infopanelen lösning](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Etablera enheten i fjärrövervakningslösningen

> [!NOTE]
> Om du redan har etablerat en enhet i din lösning kan du hoppa över det här steget. När du skapar klientprogrammet behöver du autentiseringsuppgifter för enheten.

För att en enhet ska kunna ansluta till den förkonfigurerade lösningen måste den identifiera sig för IoT Hub med giltiga autentiseringsuppgifter. Du kan hämta autentiseringsuppgifter för enheten från lösningen **enheter** sidan. Du kan inkludera enhetsautentiseringsuppgifterna i klientprogrammet senare i den här självstudien.

Lägg till en enhet i din lösning för fjärråtkomst övervakning, utför följande steg på den **enheter** sida i lösningen:

1. Välj **etablera**, och välj sedan **fysiska** som den **enhetstyp**:

    ![Etablera en fysisk enhet](media/iot-suite-selector-connecting/devicesprovision.png)

1. Ange **fysiska kylaggregat** som enhets-ID. Välj den **symmetrisk nyckel** och **automatiskt generera nycklar** alternativ:

    ![Välj alternativ för enheten](media/iot-suite-selector-connecting/devicesoptions.png)

Gå till Azure-portalen i webbläsaren för att hitta de autentiseringsuppgifter som din enhet måste använda för att ansluta till den förkonfigurerade lösningen. Logga in till din prenumeration.

1. Leta upp resursgruppen som innehåller Azure-tjänster använder din fjärranslutna övervakningslösning. Resursgruppen har samma namn som den fjärranslutna övervakningslösning som du har etablerats.

1. Navigera till IoT-hubben i den här resursgruppen. Välj **IoT-enheter**:

    ![Enheten explorer](media/iot-suite-selector-connecting/deviceexplorer.png)

1. Välj den **enhets-ID** du skapade på den **enheter** sida i fjärranslutna övervakningslösning.

1. Anteckna den **enhets-ID** och **primärnyckel** värden. Du kan använda de här värdena när du lägger till kod för att ansluta enheten till lösningen.

Du har nu etablerats en fysisk enhet för fjärranslutna övervakningen förkonfigurerade lösningen. I följande avsnitt kan du implementera klientprogram som använder enheten autentiseringsuppgifterna för att ansluta till din lösning.

Klientprogrammet implementerar inbyggt **kylaggregat** enhetsmodell. En förkonfigurerade lösningen enhetsmodell anger följande om en enhet:

* Egenskaperna enheten rapporterar till lösningen. Till exempel en **kylaggregat** enheten rapporterar information om den inbyggda programvaran och plats.
* Vilka typer av telemetri enheten skickar till lösningen. Till exempel en **kylaggregat** enheten skickar temperatur, fuktighet och tryckvärden.
* Metoderna som du kan schemalägga från lösningen ska köras på enheten. Till exempel en **kylaggregat** enheten måste implementera **omstart**, **FirmwareUpdate**, **EmergencyValveRelease**, och  **IncreasePressuree** metoder.