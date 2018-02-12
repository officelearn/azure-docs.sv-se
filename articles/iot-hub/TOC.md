# Översikt
## [Azure och IoT](iot-hub-what-is-azure-iot.md)
## [Vad är Azure IoT Hub?](iot-hub-what-is-iot-hub.md)
## [Översikt över enhetshantering](iot-hub-device-management-overview.md)

# [Komma igång](iot-hub-get-started.md)

## Konfigurera enheten
### [Simulera en enhet på datorn](iot-hub-get-started-simulated.md)
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.js](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### [Använda en onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md)

### [Använda en fysisk enhet](iot-hub-get-started-physical.md)
#### [Raspberry Pi med Python](iot-hub-raspberry-pi-kit-python-get-started.md)
#### [Raspberry Pi med Node.js](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [Raspberry Pi med C](iot-hub-raspberry-pi-kit-c-get-started.md)

#### [MXChip IoT DevKit med Arduino](iot-hub-arduino-iot-devkit-az3166-get-started.md)

#### [Intel Edison med Node.js](iot-hub-intel-edison-kit-node-get-started.md)
#### [Intel Edison med C](iot-hub-intel-edison-kit-c-get-started.md)

#### [Adafruit Feather HUZZAH ESP8266 med Arduino IDE](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [Sparkfun ESP8266 Thing Dev med Arduino IDE](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [Adafruit Feather M0 med Arduino IDE](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

## Utökade IoT-scenarier
### [Hantera meddelanden mellan enheter och molnet med iothub-explorer](iot-hub-explorer-cloud-device-messaging.md)
### [Spara meddelanden från IoT Hub i datalagring för Azure](iot-hub-store-data-in-azure-table-storage.md)
### [Datavisualisering i Power BI](iot-hub-live-data-visualization-in-power-bi.md)
### [Datavisualisering med Web Apps](iot-hub-live-data-visualization-in-web-apps.md)
### [Väderprognoser med hjälp av Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md)
### [Enhetshantering med IoT Hub-utforskaren](iot-hub-device-management-iothub-explorer.md)
### [Enhetshantering med IoT-tillägg för Azure CLI 2.0](iot-hub-device-management-iot-extension-azure-cli-2-0.md)
### [Fjärrövervakning och aviseringar med Logic Apps](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

## Använda MXChip IoT DevKit
### [Använda Azure IoT Suite för fjärrövervakning](iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)

# Instruktionsguider
## Planera
### [Jämföra IoT Hub och Event Hubs](iot-hub-compare-event-hubs.md)
### [Skala din lösning](iot-hub-scaling.md)
### [Hög tillgänglighet och haveriberedskap](iot-hub-ha-dr.md)
### [Stöd för ytterligare protokoll](iot-hub-protocol-gateway.md)
### [Jämföra meddelande- och händelsedirigering](iot-hub-event-grid-routing-comparison.md)
## [Utveckla](iot-hub-how-to.md)
### [Utvecklarguide](iot-hub-devguide.md)
#### [Funktionsguide enheten till molnet](iot-hub-devguide-d2c-guidance.md)
#### [Funktionsguide molnet till enheten](iot-hub-devguide-c2d-guidance.md)
#### [Skicka och ta emot meddelanden](iot-hub-devguide-messaging.md)
##### [Skicka meddelanden från enhet till moln till IoT Hub](iot-hub-devguide-messages-d2c.md)
##### [Läsa meddelanden från enhet till moln från den inbyggda slutpunkten](iot-hub-devguide-messages-read-builtin.md)
##### [Reagera på IoT Hub-händelser](iot-hub-event-grid.md)
##### [Använda anpassade slutpunkter och routningsregler för meddelanden från enhet till moln](iot-hub-devguide-messages-read-custom.md)
##### [Skicka meddelanden från moln till enhet från IoT Hub](iot-hub-devguide-messages-c2d.md)
##### [Skapa och läsa IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md)
##### [Välja ett kommunikationsprotokoll](iot-hub-devguide-protocols.md)
#### [Ladda upp filer från en enhet](iot-hub-devguide-file-upload.md)
#### [Hantera enhetsidentiteter](iot-hub-devguide-identity-registry.md)
#### [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)
#### [Förstå enhetstvillingar](iot-hub-devguide-device-twins.md)
#### [Anropa direktmetoder på en enhet](iot-hub-devguide-direct-methods.md)
#### [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)
#### [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
#### [Frågespråk](iot-hub-devguide-query-language.md)
#### [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
#### [Prissättningsexempel](iot-hub-devguide-pricing.md)
#### [SDK:er för tjänst och enhet](iot-hub-devguide-sdks.md)
#### [MQTT-support](iot-hub-mqtt-support.md)
#### [Ordlista](iot-hub-devguide-glossary.md)
### [Använd SDK för IoT-enhet för C](iot-hub-device-sdk-c-intro.md)
#### [Använda IoTHubClient](iot-hub-device-sdk-c-iothubclient.md)
#### [Använda serialiseraren](iot-hub-device-sdk-c-serializer.md)
### Meddelanderoutning
#### [.NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
#### [Node.js](iot-hub-node-node-process-d2c.md)
#### [Python](iot-hub-python-python-process-d2c.md)
### Skicka meddelanden från moln till enhet
#### [.NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.js](iot-hub-node-node-c2d.md)
#### [Python](iot-hub-python-python-c2d.md)
### Ladda upp filer från enheter
#### [.NET](iot-hub-csharp-csharp-file-upload.md)
#### [Java](iot-hub-java-java-file-upload.md)
#### [Node.js](iot-hub-node-node-file-upload.md)
### Kom igång med enhetstvillingar
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-twin-getstarted.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-twin-getstarted.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-twin-getstarted.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-twin-getstarted.md)
#### [Python-serverdel/Python-enhet](iot-hub-python-twin-getstarted.md)
### Använda direkta metoder
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-direct-methods.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-direct-methods.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-direct-methods.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-direct-methods.md)
#### [Python-serverdel/Python-enhet](iot-hub-python-python-direct-methods.md)
### Komma igång med enhetshantering
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-device-management-get-started.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-device-management-get-started.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-device-management-get-started.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-device-management-getstarted.md)
### Så här använder du tvillingegenskaper
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-twin-how-to-configure.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-twin-how-to-configure.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-twin-how-to-configure.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-twin-how-to-configure.md)
### Använda enhetsjobb för att uppdatera enhetens inbyggda programvara
#### [Node-serverdel/Node-enhet](iot-hub-node-node-firmware-update.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-firmware-update.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-firmware-update.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-firmware-update.md)
### Schemalägga och sända jobb
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-schedule-jobs.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-schedule-jobs.md)
#### [Java](iot-hub-java-java-schedule-jobs.md)

## Hantera
### Skapa en IoT Hub 
#### [Använda Azure Portal](iot-hub-create-through-portal.md)
#### [Använda Azure PowerShell](iot-hub-create-using-powershell.md)
#### [Använda Azure CLI](iot-hub-create-using-cli.md)
#### [Använda CLI](iot-hub-create-using-cli-nodejs.md)
#### [Använda REST-API](iot-hub-rm-rest.md)
#### [Använda en mall från Azure PowerShell](iot-hub-rm-template-powershell.md)
#### [Använda en mall från .NET](iot-hub-rm-template.md)
### Konfigurera filuppladdning
#### [Använda Azure Portal](iot-hub-configure-file-upload.md)
#### [Använda Azure PowerShell](iot-hub-configure-file-upload-powershell.md)
#### [Använda Azure CLI](iot-hub-configure-file-upload-cli.md)
### [Övervaka med diagnostik](iot-hub-monitor-resource-health.md)
#### [Migrera till diagnostikinställningar](iot-hub-migrate-to-diagnostics-settings.md)
#### [Övervakning av åtgärder](iot-hub-operations-monitoring.md)
### [Användningsstatistik](iot-hub-metrics.md)
### [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
### [Konfigurera IP-filtrering](iot-hub-ip-filtering.md)

## Skydda
### [Säkerhet från grunden](iot-hub-security-ground-up.md)
### [Metodtips för säkerhet](iot-hub-security-best-practices.md)
### [Säkerhetsarkitektur](iot-hub-security-architecture.md)
### [Skydda distributionen av IoT](iot-hub-security-deployment.md)
### Skydda med X.509 CA-certifikat
#### [Översikt över certifikatsäkerhet med X.509 CA](iot-hub-x509ca-overview.md)
##### [Koncept för certifikatsäkerhet med X.509 CA](iot-hub-x509ca-concept.md)
#### [Kom igång med certifikatsäkerhet med X.509 CA](iot-hub-security-x509-get-started.md)
##### [Skapa certifikat – PowerShell](iot-hub-security-x509-create-certificates.md)

# Referens
## [Kodexempel](https://azure.microsoft.com/en-us/resources/samples/?service=iot-hub)
## [Azure CLI](/cli/azure/iot)
## [.NET (tjänst)](/dotnet/api/microsoft.azure.devices)
## [.NET (enheter)](/dotnet/api/microsoft.azure.devices.client)
## [Java (tjänst)](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java (enheter)](/java/api/com.microsoft.azure.sdk.iot.device)
## [Node.js (enheter)](https://docs.microsoft.com/javascript/api/azure-iot-device/)
## [Node.js (tjänst)](https://docs.microsoft.com/javascript/api/azure-iothub)
## [C-enhets-SDK](https://azure.github.io/azure-iot-sdk-c/index.html)
## [Azure IoT Edge](http://azure.github.io/iot-edge/)
## [REST (resursprovider)](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST (enhetsidentiteter)](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST (enhetstvillingar)](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST (enhetsmeddelanden)](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST (jobb)](https://docs.microsoft.com/rest/api/iothub/jobapi)

# Relaterat
## [Azure IoT Suite](https://azure.microsoft.com/documentation/suites/iot-suite/)
## [Azure IoT Hub Device Provisioning-tjänst](https://azure.microsoft.com/documentation/services/iot-dps/)
## [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)
## [Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/)
## [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

# Resurser
## [Azure Certified for IoT-enhetskatalog](https://catalog.azureiotsuite.com/)
## [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot/)
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=internet-of-things)
## [DeviceExplorer-verktyg](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
## [iothub-diagnostics-verktyg](https://github.com/Azure/iothub-diagnostics)
## [iothub-explorer-verktyg](https://github.com/Azure/iothub-explorer)
## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/iot-hub/)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azureiothub)
## [Prissättning](https://azure.microsoft.com/pricing/details/iot-hub/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=iot-hub)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-iot-hub)
## [Tekniska fallstudier](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=iot-hub)
