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

Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Innan du börjar

Innan du skriva kod för enheten Distribuera fjärråtkomst övervakning förkonfigurerade lösningar och lägger till en ny fysisk enhet i lösningen.

### <a name="deploy-your-remote-monitoring-preconfigured-solution"></a>Distribuera fjärråtkomst övervakning förkonfigurerade lösningar

Den **kylaggregat** enhet som du skapar i den här självstudiekursen skickar data till en instans av den [fjärrövervaknings](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) förkonfigurerade lösningen. Om du inte redan har etablerats fjärråtkomst övervakning förkonfigurerade lösningen i ditt Azure-konto, se [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

När distributionsprocessen för fjärranslutna övervakningslösning är klar klickar du på **starta** att öppna lösningen instrumentpanelen i webbläsaren.

![Infopanelen lösning](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Lägg till din enhet i fjärranslutna övervakningslösning

> [!NOTE]
> Om du redan har lagt till en enhet i din lösning kan du hoppa över det här steget. Nästa steg kräver dock anslutningssträngen enhet. Du kan hämta anslutningssträngen för en enhet från den [Azure-portalen](https://portal.azure.com) eller med hjälp av den [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI-verktyget.

För att en enhet ska kunna ansluta till den förkonfigurerade lösningen måste den identifiera sig för IoT Hub med giltiga autentiseringsuppgifter. Du har möjlighet att spara anslutningssträngen för enheten som innehåller dessa autentiseringsuppgifter när du lägger till enheten lösningen. Du inkludera enheten anslutningssträngen i ditt klientprogram senare i den här kursen.

Lägg till en enhet i din lösning för fjärråtkomst övervakning, utför följande steg på den **enheter** sida i lösningen:

1. Välj **+ ny enhet**, och välj sedan **fysiska** som den **enhetstyp**:

    ![Lägg till en fysisk enhet](media/iot-suite-selector-connecting/devicesprovision.png)

1. Ange **fysiska kylaggregat** som enhets-ID. Välj den **symmetrisk nyckel** och **automatiskt generera nycklar** alternativ:

    ![Välj alternativ för enheten](media/iot-suite-selector-connecting/devicesoptions.png)

1. Välj **tillämpa**. Sedan anteckna den **enhets-ID**, **primärnyckel**, och **anslutning sträng primärnyckel** värden:

    ![Hämta autentiseringsuppgifter](media/iot-suite-selector-connecting/credentials.png)

Du har nu lagt till en fysisk enhet till fjärråtkomst övervakning förkonfigurerade lösningen och anges sin enhet anslutningssträng. I följande avsnitt kan du implementera klientprogram som använder enheten anslutningssträngen för att ansluta till din lösning.

Klientprogrammet implementerar inbyggt **kylaggregat** enhetsmodell. En förkonfigurerade lösningen enhetsmodell anger följande om en enhet:

* Egenskaperna enheten rapporterar till lösningen. Till exempel en **kylaggregat** enheten rapporterar information om den inbyggda programvaran och plats.
* Vilka typer av telemetri enheten skickar till lösningen. Till exempel en **kylaggregat** enheten skickar temperatur, fuktighet och tryckvärden.
* Metoderna som du kan schemalägga från lösningen ska köras på enheten. Till exempel en **kylaggregat** enheten måste implementera **omstart**, **FirmwareUpdate**, **EmergencyValveRelease**, och  **IncreasePressure** metoder.