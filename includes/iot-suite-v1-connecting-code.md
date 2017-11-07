## <a name="specify-the-behavior-of-the-iot-device"></a>Ange beteende för IoT-enheten

Klientbiblioteket för IoT Hub-serialiseraren använder en modell för att ange formatet på de meddelanden som enheten utbyter med IoT Hub.

1. Lägg till följande variabeldeklarationer efter `#include`-instruktionerna. Ersätta platshållarvärdena [enhets-ID] och [enhetsnyckel] med värdena som du antecknade för din enhet i instrumentpanelen för fjärråtkomst övervakning lösning. Använd värdnamnet för IoT Hub från lösningens instrumentpanel för att ersätta [IoTHub-namn]. Om ditt värdnamn för IoT Hub exempelvis är **contoso.azure-devices.net** ersätter du [IoTHub-namn] med **contoso**:
   
    ```c
    static const char* deviceId = "[Device Id]";
    static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
    ```

1. Lägg till följande kod för att definiera den modell som aktiverar enheten till att kommunicera med IoT Hub. Modellen anger att enheten kan göra följande:

   - Skicka temperatur, externa temperatur, fuktighet och enhets-ID som telemetri.
   - Skicka metadata om enheten till IoT Hub. Enheten skickar grundläggande metadata i ett **DeviceInfo**-objekt vid start.
   - Skicka rapporterade egenskaper till enhetstvillingen i IoT Hub. De rapporterade egenskaperna grupperas i konfigurations-, enhets- och systemegenskaper.
   - Ta emot och arbeta med önskade egenskaper som angetts i enhetstvillingen i IoT Hub.
   - Svara på de direkta metoderna **Reboot** och **InitiateFirmwareUpdate** som anropas via lösningsportalen. Enheten använder rapporterade egenskaper för att skicka information om direkta metoder som stöds.
   
    ```c
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    /* Reported properties */
    DECLARE_STRUCT(SystemProperties,
      ascii_char_ptr, Manufacturer,
      ascii_char_ptr, FirmwareVersion,
      ascii_char_ptr, InstalledRAM,
      ascii_char_ptr, ModelNumber,
      ascii_char_ptr, Platform,
      ascii_char_ptr, Processor,
      ascii_char_ptr, SerialNumber
    );

    DECLARE_STRUCT(LocationProperties,
      double, Latitude,
      double, Longitude
    );

    DECLARE_STRUCT(ReportedDeviceProperties,
      ascii_char_ptr, DeviceState,
      LocationProperties, Location
    );

    DECLARE_MODEL(ConfigProperties,
      WITH_REPORTED_PROPERTY(double, TemperatureMeanValue),
      WITH_REPORTED_PROPERTY(uint8_t, TelemetryInterval)
    );

    /* Part of DeviceInfo */
    DECLARE_STRUCT(DeviceProperties,
      ascii_char_ptr, DeviceID,
      _Bool, HubEnabledState
    );

    DECLARE_DEVICETWIN_MODEL(Thermostat,
      /* Telemetry (temperature, external temperature and humidity) */
      WITH_DATA(double, Temperature),
      WITH_DATA(double, ExternalTemperature),
      WITH_DATA(double, Humidity),
      WITH_DATA(ascii_char_ptr, DeviceId),

      /* DeviceInfo */
      WITH_DATA(ascii_char_ptr, ObjectType),
      WITH_DATA(_Bool, IsSimulatedDevice),
      WITH_DATA(ascii_char_ptr, Version),
      WITH_DATA(DeviceProperties, DeviceProperties),

      /* Device twin properties */
      WITH_REPORTED_PROPERTY(ReportedDeviceProperties, Device),
      WITH_REPORTED_PROPERTY(ConfigProperties, Config),
      WITH_REPORTED_PROPERTY(SystemProperties, System),

      WITH_DESIRED_PROPERTY(double, TemperatureMeanValue, onDesiredTemperatureMeanValue),
      WITH_DESIRED_PROPERTY(uint8_t, TelemetryInterval, onDesiredTelemetryInterval),

      /* Direct methods implemented by the device */
      WITH_METHOD(Reboot),
      WITH_METHOD(InitiateFirmwareUpdate, ascii_char_ptr, FwPackageURI),

      /* Register direct methods with solution portal */
      WITH_REPORTED_PROPERTY(ascii_char_ptr_no_quotes, SupportedMethods)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>Implementera enhetens beteende
Lägg till kod som implementerar det beteende som definierats i modellen.

1. Lägg till följande funktioner som hanterar de önskade egenskaperna i lösningens instrumentpanel. Dessa önskade egenskaper definieras i modellen:

    ```c
    void onDesiredTemperatureMeanValue(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TemperatureMeanValue = %f\r\n", thermostat->TemperatureMeanValue);

    }

    void onDesiredTelemetryInterval(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TelemetryInterval = %d\r\n", thermostat->TelemetryInterval);
    }
    ```

1. Lägg till följande funktioner som hanterar de direkta metoder som anropas via IoT Hub. Dessa direkta metoder definieras i modellen:

    ```c
    /* Handlers for direct methods */
    METHODRETURN_HANDLE Reboot(Thermostat* thermostat)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Rebooting\"");
      printf("Received reboot request\r\n");
      return result;
    }

    METHODRETURN_HANDLE InitiateFirmwareUpdate(Thermostat* thermostat, ascii_char_ptr FwPackageURI)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Initiating Firmware Update\"");
      printf("Recieved firmware update request. Use package at: %s\r\n", FwPackageURI);
      return result;
    }
    ```

1. Lägg till följande funktion som skickar ett meddelande till den förkonfigurerade lösningen:
   
    ```c
    /* Send data to IoT Hub */
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
      free((void*)buffer);
    }
    ```

1. Lägg till följande motringningshanterare som körs när enheten har skickat nya rapporterade egenskapsvärden till den förkonfigurerade lösningen:

    ```c
    /* Callback after sending reported properties */
    void deviceTwinCallback(int status_code, void* userContextCallback)
    {
      (void)(userContextCallback);
      printf("IoTHub: reported properties delivered with status_code = %u\n", status_code);
    }
    ```

1. Lägg till följande funktion för att ansluta enheten till den förkonfigurerade lösningen i molnet och utbyta data. Den här funktionen utför följande steg:

    - Initierar plattformen.
    - Registrerar namnområdet Contoso med serialiseringsbiblioteket.
    - Initierar klienten med enhetens anslutningssträng.
    - Skapar en instans av modellen **Termostat**.
    - Skapar och skickar rapporterade egenskapsvärden.
    - Skickar ett **DeviceInfo**-objekt.
    - Skapar en loop för att skicka telemetri varje sekund.
    - Avinitierar alla resurser.

    ```c
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\n");
      }
      else
      {
        if (SERIALIZER_REGISTER_NAMESPACE(Contoso) == NULL)
        {
          printf("Unable to SERIALIZER_REGISTER_NAMESPACE\n");
        }
        else
        {
          IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, MQTT_Protocol);
          if (iotHubClientHandle == NULL)
          {
            printf("Failure in IoTHubClient_CreateFromConnectionString\n");
          }
          else
          {
    #ifdef MBED_BUILD_TIMESTAMP
            // For mbed add the certificate information
            if (IoTHubClient_SetOption(iotHubClientHandle, "TrustedCerts", certificates) != IOTHUB_CLIENT_OK)
            {
                printf("Failed to set option \"TrustedCerts\"\n");
            }
    #endif // MBED_BUILD_TIMESTAMP
            Thermostat* thermostat = IoTHubDeviceTwin_CreateThermostat(iotHubClientHandle);
            if (thermostat == NULL)
            {
              printf("Failure in IoTHubDeviceTwin_CreateThermostat\n");
            }
            else
            {
              /* Set values for reported properties */
              thermostat->Config.TemperatureMeanValue = 55.5;
              thermostat->Config.TelemetryInterval = 3;
              thermostat->Device.DeviceState = "normal";
              thermostat->Device.Location.Latitude = 47.642877;
              thermostat->Device.Location.Longitude = -122.125497;
              thermostat->System.Manufacturer = "Contoso Inc.";
              thermostat->System.FirmwareVersion = "2.22";
              thermostat->System.InstalledRAM = "8 MB";
              thermostat->System.ModelNumber = "DB-14";
              thermostat->System.Platform = "Plat 9.75";
              thermostat->System.Processor = "i3-7";
              thermostat->System.SerialNumber = "SER21";
              /* Specify the signatures of the supported direct methods */
              thermostat->SupportedMethods = "{\"Reboot\": \"Reboot the device\", \"InitiateFirmwareUpdate--FwPackageURI-string\": \"Updates device Firmware. Use parameter FwPackageURI to specify the URI of the firmware file\"}";

              /* Send reported properties to IoT Hub */
              if (IoTHubDeviceTwin_SendReportedStateThermostat(thermostat, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
              {
                printf("Failed sending serialized reported state\n");
              }
              else
              {
                printf("Send DeviceInfo object to IoT Hub at startup\n");
    
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = 0;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = 1;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;

                unsigned char* buffer;
                size_t bufferSize;

                if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties) != CODEFIRST_OK)
                {
                  (void)printf("Failed serializing DeviceInfo\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                /* Send telemetry */
                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %f, Humidity = %f\n", thermostat->Temperature, thermostat->Humidity);

                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                  ThreadAPI_Sleep(1000);
                }

                IoTHubDeviceTwin_DestroyThermostat(thermostat);
              }
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
      }
      platform_deinit();
    }
    ```
   
    Här är ett exempel på ett **Telemetri**-meddelande som skickats till den förkonfigurerade lösningen:
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```