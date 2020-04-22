---
title: Självstudiekurs - Anslut en allmän Python-klientapp till Azure IoT Central | Microsoft-dokument
description: Den här självstudien visar hur du som enhetsutvecklare ansluter en enhet som kör en Python-klientapp till ditt Azure IoT Central-program. Du skapar en enhetsmall genom att importera en enhetskapacitetsmodell och lägga till vyer som gör att du kan interagera med en ansluten enhet
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d6c44c81db78fa76eeaf4b7181cca34fb8e81523
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758181"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Självstudiekurs: Skapa och ansluta ett klientprogram till ditt Azure IoT Central-program (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Den här artikeln gäller lösningsbyggare och enhetsutvecklare.*

Den här självstudien visar hur du som enhetsutvecklare ansluter ett Python-klientprogram till ditt Azure IoT Central-program. Python-programmet simulerar beteendet hos en miljösensorenhet. Du använder en _exempelenhetsfunktionsmodell_ för att skapa en _enhetsmall_ i IoT Central. Du lägger till vyer i enhetsmallen så att en operatör kan interagera med en enhet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Importera en enhetsfunktionsmodell för att skapa en enhetsmall.
> * Lägg till standardvyer och anpassade vyer i en enhetsmall.
> * Publicera en enhetsmall och lägg till en riktig enhet i ditt IoT Central-program.
> * Skapa och kör Python-enhetskoden och se den ansluta till ditt IoT Central-program.
> * Visa den simulerade telemetrin som skickas från enheten.
> * Använd en vy för att hantera enhetsegenskaper.
> * Anropa synkrona och asynkrona kommandon för att styra enheten.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av den **anpassade programmallen.** Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En utvecklingsmaskin med [Python](https://www.python.org/) version 3.7 eller senare installerad. Du kan `python3 --version` köra på kommandoraden för att kontrollera din version. Python är tillgängligt för en mängd olika operativsystem. Instruktionerna i den här självstudien förutsätter att du kör **python3-kommandot** i Kommandotolken i Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Skapa ett Python-program

Följande steg visar hur du skapar ett Python-klientprogram som ansluter till den verkliga enheten som du har lagt till i programmet. Detta Python-program simulerar beteendet hos en riktig enhet.

1. I kommandoradsmiljön navigerar `environmental-sensor` du till mappen som du skapade tidigare.

1. Om du vill installera de bibliotek som krävs kör du följande kommandon:

    ```cmd
    pip install azure-iot-device
    ```

1. Skapa en fil som heter **environmental_sensor.py** i `environmental-sensor` mappen.

1. Lägg till `import` följande satser i början av filen **environmental_sensor.py:**

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Lägg till följande asynkrona `main` funktions- och variabeldeklarationer i filen:

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    Uppdatera platshållarna `{your Scope ID}` `{your Device ID}`och `{your Primary Key}` med de värden som du har noterat tidigare. I ett riktigt program, inte hårt koda denna information i programmet.

    Alla följande funktionsdefinitioner och kod `main` är kapslade i funktionen.

1. Lägg till följande två `main` funktioner i funktionen för att registrera enheten och ansluta den till ditt IoT Central-program. Registreringen använder Azure Device Provisioning Service:

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Lägg till följande `main` funktion i funktionen för att skicka telemetri till ditt IoT Central-program:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    Namnen på telemetriobjekten`temp` ( `humid`och ) måste matcha de namn som används i enhetsmallen.

1. Lägg till följande `main` funktioner i funktionen för att hantera kommandon som anropas från ditt IoT Central-program:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    Namnen på kommandona`blink`( `turnon` `turnoff`, `rundiagnostics`, och ) måste matcha de namn som används i enhetsmallen.

    För närvarande använder IoT Central inte det svarsschema som definierats i enhetskapacitetsmodellen. För ett synkront kommando kan svarsnyttolasten vara vilken giltig JSON som helst. För ett asynkront kommando bör enheten returnera ett 202-svar omedelbart, följt av rapporterad egenskapsuppdatering när arbetet är klart. Formatet för den rapporterade egenskapsuppdateringen är:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    En operatör kan visa svarsnyttolasten i kommandohistoriken.

1. Lägg till följande `main` funktioner i funktionen för att hantera egenskapsuppdateringar som skickas från ditt IoT Central-program:

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        settings = {
          'name': name_setting,
          'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
          while True:
            patch = await device_client.receive_twin_desired_properties_patch() # blocking
            to_update = patch.keys() & settings.keys()
            await asyncio.gather(
              *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
            )
    ```

    När operatören anger en skrivbar egenskap i IoT Central-programmet använder programmet en enhetstvilling önskad egenskap för att skicka värdet till enheten. Enheten svarar sedan med hjälp av en enhetstvillingrapporterad egenskap. När IoT Central tar emot det rapporterade egenskapsvärdet uppdateras egenskapsvyn med statusen **synkroniserad**.

    Namnen på egenskaperna`name` ( `brightness`och ) måste matcha de namn som används i enhetsmallen.

1. Lägg till följande `main` funktioner i funktionen för att styra programmet:

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. Spara filen **environmental_sensor.py.**

## <a name="run-your-python-application"></a>Kör ditt Python-program

Om du vill starta enhetsklientprogrammet kör du följande kommando i kommandoradsmiljön:

```cmd
python3 environmental_sensor.py
```

Du kan se enheten ansluter till ditt Azure IoT Central-program och börjar skicka telemetri:

![Kör klientprogrammet](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Du kan se hur enheten svarar på kommandon och egenskapsuppdateringar:

![Observera klientprogrammet](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Nästa steg

Som enhetsutvecklare, nu när du har lärt dig grunderna i hur du skapar en enhet med Node.js, några föreslagna nästa steg är att:

- Lär dig hur du ansluter en riktig enhet till IoT Central i [Connect an MXChip IoT DevKit-enheten till din Azure IoT Central-program](./howto-connect-devkit.md) how-to-artikel.
- Läs [Anslut till Azure IoT Central](./concepts-get-connected.md) om du vill veta mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhetsanslutningar.

Om du föredrar att fortsätta genom uppsättningen IoT Central tutorials och lära sig mer om att bygga en IoT Central lösning, se:

> [!div class="nextstepaction"]
> [Mall för att skapa en gateway-enhet](./tutorial-define-gateway-device-type.md)