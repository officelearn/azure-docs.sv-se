# Översikt
## [Azure och IoT](iot-hub-what-is-azure-iot.md)
## [Vad är Azure IoT Hub?](iot-hub-what-is-iot-hub.md)
## [Översikt över enhetshantering](iot-hub-device-management-overview.md)

# [Kom igång](iot-hub-get-started.md)

## Konfigurera enheten
### [Simulera en enhet på datorn](iot-hub-get-started-simulated.md)
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.js](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### [Använda en onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md)

### [Använda en fysisk enhet](iot-hub-get-started-physical.md)
#### [Raspberry Pi med Node.js](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [Raspberry Pi med C](iot-hub-raspberry-pi-kit-c-get-started.md)
#### [Raspberry Pi med Python](iot-hub-raspberry-pi-kit-python-get-started.md)

#### [Intel Edison med Node.js](iot-hub-intel-edison-kit-node-get-started.md)
#### [Intel Edison med C](iot-hub-intel-edison-kit-c-get-started.md)

#### [Adafruit Feather HUZZAH ESP8266 med Arduino IDE](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [Sparkfun ESP8266 Thing Dev med Arduino IDE](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [Adafruit Feather M0 med Arduino IDE](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

#### Använda IoT Gateway-startpaketet
##### [Konfigurera Intel NUC som en gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
##### [Ansluta gatewayen till IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
##### [Använda gatewayen för datakonvertering](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

## Utökade IoT-scenarier
### [Hantera meddelanden mellan enheter och molnet med iothub-explorer](iot-hub-explorer-cloud-device-messaging.md)
### [Spara meddelanden från IoT Hub i datalagring för Azure](iot-hub-store-data-in-azure-table-storage.md)
### [Datavisualisering i Power BI](iot-hub-live-data-visualization-in-power-bi.md)
### [Datavisualisering med Web Apps](iot-hub-live-data-visualization-in-web-apps.md)
### [Väderprognoser med hjälp av Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md)
### [Enhetshantering med IoT Hub-utforskaren](iot-hub-device-management-iothub-explorer.md)
### [Fjärrövervakning och aviseringar med Logic Apps](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

# Gör så här för att
## Planera
### [Jämförelse mellan IoT Hub och Event Hubs](iot-hub-compare-event-hubs.md)
### [Skala din lösning](iot-hub-scaling.md)
### [Hög tillgänglighet och haveriberedskap](iot-hub-ha-dr.md)
### [Stöd för ytterligare protokoll](iot-hub-protocol-gateway.md)
## [Utveckla](iot-hub-how-to.md)
### [Utvecklarguide](iot-hub-devguide.md)
#### [Funktionsguide enheten till molnet](iot-hub-devguide-d2c-guidance.md)
#### [Funktionsguide molnet till enheten](iot-hub-devguide-c2d-guidance.md)
#### [Skicka och ta emot meddelanden](iot-hub-devguide-messaging.md)
##### [Skicka meddelanden från enhet till moln till IoT Hub](iot-hub-devguide-messages-d2c.md)
##### [Läsa meddelanden från enhet till moln från den inbyggda slutpunkten](iot-hub-devguide-messages-read-builtin.md)
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
### Bearbeta meddelanden från enhet till moln
#### [NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
### Skicka meddelanden från moln till enhet
#### [NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.js](iot-hub-node-node-c2d.md)
### [Ladda upp filer från enheter](iot-hub-csharp-csharp-file-upload.md)
### Kom igång med enhetstvillingar
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-twin-getstarted.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-twin-getstarted.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-twin-getstarted.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-twin-getstarted.md)
### Använda direkta metoder
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-direct-methods.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-direct-methods.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-direct-methods.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-direct-methods.md)
### Komma igång med enhetshantering
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-device-management-get-started.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-device-management-get-started.md)
#### [Java-serverdel/Java-enhet](iot-hub-java-java-device-management-getstarted.md)
### Så här använder du tvillingegenskaper
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-twin-how-to-configure.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-twin-how-to-configure.md)
#### [.NET-serverdel/.NET-enhet](iot-hub-csharp-csharp-twin-how-to-configure.md)
### Använda enhetsjobb för att uppdatera enhetens inbyggda programvara
#### [Node-serverdel/Node-enhet](iot-hub-node-node-firmware-update.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-firmware-update.md)
### Schemalägga och sända jobb
#### [Node.js-serverdel/Node.js-enhet](iot-hub-node-node-schedule-jobs.md)
#### [.NET-serverdel/Node.js-enhet](iot-hub-csharp-node-schedule-jobs.md)
#### [Java](iot-hub-java-java-schedule-jobs.md)
## Hantera
### Skapa en IoT Hub 
#### [Använda portalen](iot-hub-create-through-portal.md)
#### [Använda PowerShell](iot-hub-create-using-powershell.md)
#### [Använda CLI 2.0](iot-hub-create-using-cli.md)
#### [Använda CLI](iot-hub-create-using-cli-nodejs.md)
#### [Använda REST-API](iot-hub-rm-rest.md)
#### [Använda en mall från PowerShell](iot-hub-rm-template-powershell.md)
#### [Använda en mall från .NET](iot-hub-rm-template.md)
### Konfigurera filuppladdning
#### [Använda portalen](iot-hub-configure-file-upload.md)
#### [Använda PowerShell](iot-hub-configure-file-upload-powershell.md)
#### [Använda CLI 2.0](iot-hub-configure-file-upload-cli.md)
### [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
### [Användningsstatistik](iot-hub-metrics.md)
### [Övervakning av åtgärder](iot-hub-operations-monitoring.md)
### [Konfigurera IP-filtrering](iot-hub-ip-filtering.md)
## Skydda
### [Säkerhet från grunden](iot-hub-security-ground-up.md)
### [Metodtips för säkerhet](iot-hub-security-best-practices.md)
### [Säkerhetsarkitektur](iot-hub-security-architecture.md)
### [Skydda distributionen av IoT](iot-hub-security-deployment.md)
## Azure IoT Edge
### [Översikt](iot-hub-iot-edge-overview.md)
### Kom igång
#### [Linux](iot-hub-linux-iot-edge-get-started.md)
#### [Windows](iot-hub-windows-iot-edge-get-started.md)
### Simulera en enhet
#### [Linux](iot-hub-linux-iot-edge-simulated-device.md)
#### [Windows](iot-hub-windows-iot-edge-simulated-device.md)
### [Använda en riktig enhet](iot-hub-iot-edge-physical-device.md)
### Skapa en modul
#### [Java](iot-hub-iot-edge-create-module-java.md)
#### [.NET Framework](https://github.com/Azure-Samples/iot-edge-samples#how-to-run-the-net-module-sample-windows-10)
#### [.NET-standard](iot-hub-iot-edge-create-module-dotnet-core.md)
#### [Node.js](iot-hub-iot-edge-create-module-js.md)
### Utveckla
#### [.NET Framework](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample)
#### [.NET Core-modul](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_module_sample)
#### [.NET Core-hanterad gateway](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_managed_gateway)
#### [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample)
#### [Node.js](https://github.com/Azure/iot-edge/tree/master/samples/nodejs_simple_sample)
#### [Lägga till en modul dynamiskt](https://github.com/Azure/iot-edge/tree/master/samples/dynamically_add_module_sample)
#### [Processextern proxymodul](https://github.com/Azure/iot-edge/tree/master/samples/proxy_sample)
#### [Ursprunglig modulvärd](https://github.com/Azure/iot-edge/tree/master/samples/native_module_host_sample)

# Referens
## [Kodexempel](https://azure.microsoft.com/en-us/resources/samples/?service=iot-hub)
## [Azure CLI](/cli/azure/iot)
## [.NET (tjänst)](/dotnet/api/microsoft.azure.devices)
## [.NET (enheter)](/dotnet/api/microsoft.azure.devices.client)
## [Java (tjänst)](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java (enheter)](/java/api/com.microsoft.azure.sdk.iot.device)
## [Node.js-SDK:er](http://azure.github.io/azure-iot-sdk-node/)
## [C-enhets-SDK](https://azure.github.io/azure-iot-sdk-c/index.html)
## [Azure IoT Edge](http://azure.github.io/iot-edge/)
## [REST (resursprovider)](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST (enhetsidentiteter)](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST (enhetstvillingar)](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST (enhetsmeddelanden)](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST (jobb)](https://docs.microsoft.com/rest/api/iothub/jobapi)

# Relaterat
## [Azure IoT Suite](https://azure.microsoft.com/documentation/suites/iot-suite/)
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
