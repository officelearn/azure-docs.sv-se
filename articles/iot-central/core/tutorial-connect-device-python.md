---
title: Självstudie – ansluta en generisk python-app till Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du, som enhets utvecklare, ansluter en enhet som kör en python-app till ditt Azure IoT Central-program. Du ändrar den automatiskt genererade enhets mal len genom att lägga till vyer som låter en operatör interagera med en ansluten enhet.
author: dominicbetts
ms.author: dobett
ms.date: 11/03/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 47a178447533493911ae1f8ada5c617119bc3479
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987696"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Självstudie: Skapa och anslut ett klientprogram till ditt Azure IoT Central-program (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Den här självstudien visar hur du, som enhets utvecklare, ansluter ett python-klientprogram till ditt Azure IoT Central-program. Python-programmet simulerar beteendet hos en termostat-enhet. När programmet ansluter till IoT Central, skickas modell-ID: t för enhets modellen termostat. IoT Central använder modell-ID: t för att hämta enhets modellen och skapa en enhets mall åt dig. Du kan lägga till anpassningar och vyer i enhets mal len så att en operatör kan interagera med en enhet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa och kör python-enhets koden och se hur den ansluter till ditt IoT Central-program.
> * Visa den simulerade telemetri som skickas från enheten.
> * Lägg till anpassade vyer i en enhets mall.
> * Publicera enhets mal len.
> * Använd en vy för att hantera enhets egenskaper.
> * Anropa ett kommando för att kontrol lera enheten.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av den **anpassade program** mal len. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md). Programmet måste ha skapats den 14 juli 2020 eller senare.
* En utvecklings dator med [python](https://www.python.org/) version 3,7 eller senare installerad. Du kan köra `python --version` på kommando raden för att kontrol lera din version. Python är tillgängligt för många olika operativ system. Anvisningarna i den här självstudien förutsätter att du kör **python** -kommandot i kommando tolken i Windows.
* En lokal kopia av [Microsoft Azure IoT SDK för python](https://github.com/Azure/azure-iot-sdk-python) GitHub-lagringsplatsen som innehåller exempel koden. Använd den här länken för att ladda ned en kopia av lagrings platsen: [Ladda ned ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Zippa sedan upp filen till en lämplig plats på den lokala datorn.

## <a name="review-the-code"></a>Granska koden

I kopian av Microsoft Azure IoT SDK för python som du laddade ned tidigare, öppnar du filen *Azure-IoT-SDK-python/Azure-IoT-Device/samples/PnP/simple_thermostat. py* i en text redigerare.

När du kör exemplet för att ansluta till IoT Central, används enhets etablerings tjänsten (DPS) för att registrera enheten och skapa en anslutnings sträng. Exemplet hämtar den information om DPS-anslutningen som krävs från kommando rads miljön.

`main`Funktionen:

* Använder DPS för att etablera enheten. Etablerings informationen innehåller modell-ID: t.
* Skapar ett- `Device_client` objekt och anger `dtmi:com:example:Thermostat;1` modell-ID: t innan det öppnar anslutningen.
* Skickar `maxTempSinceLastReboot` egenskapen till IoT Central.
* Skapar en lyssnare för `getMaxMinReport` kommandot.
* Skapar egenskaps lyssnare, för att lyssna efter skrivbara egenskaps uppdateringar.
* Startar en slinga för att skicka temperatur telemetri var 10: e sekund.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

`provision_device`Funktionen använder DPS för att etablera enheten och registrera den med IoT Central. Funktionen inkluderar enhets modell-ID: t i etablerings nytto lasten:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener`Funktionen hanterar kommando begär Anden, kör `max_min_handler` funktionen när enheten tar emot `getMaxMinReport` kommandot och kör `create_max_min_report_response` funktionen för att generera svaret:

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener`Hanterar skrivbara egenskaps uppdateringar som `targetTemperature` och genererar JSON-svaret:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

`send_telemetry_from_thermostat`Funktionen skickar telemetri-meddelandena till IoT Central:

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>Hämta anslutningsinformation

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Kör koden

Kör exempel programmet genom att öppna en kommando rads miljö och navigera till mappen *Azure-IoT-SDK-python/Azure-IoT-Device/samples/PnP* som innehåller exempel filen *simple_thermostat. py* .

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Installera de nödvändiga paketen:

```cmd/sh
pip install azure-iot-device
```

Kör exemplet:

```cmd/sh
python simple_thermostat.py
```

Följande utdata visar enhets registrering och anslutning till IoT Central. Exemplet skickar `maxTempSinceLastReboot` egenskapen innan den börjar skicka telemetri:

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

Du kan se hur enheten svarar på kommandon och egenskaps uppdateringar:

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```

## <a name="view-raw-data"></a>Visa rå data

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## <a name="next-steps"></a>Nästa steg

Om du föredrar att fortsätta med en uppsättning IoT Central själv studie kurser och lära dig mer om hur du skapar en IoT Central lösning, se:

> [!div class="nextstepaction"]
> [Mall för att skapa en gateway-enhet](./tutorial-define-gateway-device-type.md)

Som en enhets utvecklare har du nu lärt dig grunderna för hur du skapar en enhet med hjälp av python, men vissa föreslagna nästa steg är att:

* Läs [Vad är enhets mallar?](./concepts-device-templates.md) om du vill lära dig mer om rollen hets mallar när du implementerar din enhets kod.
* Läs [bli ansluten till Azure IoT Central](./concepts-get-connected.md) om du vill veta mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhets anslutningar.
* Läs [telemetri, egenskaper och kommando nytto laster](concepts-telemetry-properties-commands.md) för att lära dig mer om de data som enheten utbyter med IoT Central.
