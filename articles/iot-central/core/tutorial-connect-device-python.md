---
title: Självstudie – ansluta en generisk python-app till Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du, som enhets utvecklare, ansluter en enhet som kör en python-app till ditt Azure IoT Central-program. Du skapar en enhets mall genom att importera en enhets kapacitets modell och lägga till vyer som gör att du kan interagera med en ansluten enhet
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: tracking-python
ms.openlocfilehash: 30ceed388412f08e31b9c9b0c7ea6fdf2fed143e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607218"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Självstudie: skapa och ansluta ett klient program till ditt Azure IoT Central-program (python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Den här självstudien visar hur du, som enhets utvecklare, ansluter ett python-klientprogram till ditt Azure IoT Central-program. Python-programmet simulerar beteendet för en miljö sensor enhet. Du kan använda ett exempel på _enhets kapacitets modell_ för att skapa en _enhets mal len_ i IoT Central. Du lägger till vyer i enhets mal len för att låta en operatör interagera med en enhet.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Importera en enhets kapacitets modell för att skapa en enhets mall.
> * Lägg till standard-och anpassade vyer i en enhets mall.
> * Publicera en enhets mall och Lägg till en riktig enhet i IoT Central programmet.
> * Skapa och kör python-enhets koden och se hur den ansluter till ditt IoT Central-program.
> * Visa den simulerade telemetri som skickas från enheten.
> * Använd en vy för att hantera enhets egenskaper.
> * Anropa synkrona och asynkrona kommandon för att styra enheten.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av den **anpassade program** mal len. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En utvecklings dator med [python](https://www.python.org/) version 3,7 eller senare installerad. Du kan köra `python3 --version` på kommando raden för att kontrol lera din version. Python är tillgängligt för många olika operativ system. Anvisningarna i den här självstudien förutsätter att du kör kommandot **python3** i kommando tolken i Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Skapa ett Python-program

Följande steg visar hur du skapar ett python-klientprogram som ansluter till den riktiga enhet som du har lagt till i programmet. Det här python-programmet simulerar beteendet för en riktig enhet.

1. I din kommando rads miljö navigerar du till `environmental-sensor` mappen som du skapade tidigare.

1. Kör följande kommandon för att installera de bibliotek som krävs:

    ```cmd
    pip install azure-iot-device
    ```

1. Skapa en fil med namnet **environmental_sensor. py** i `environmental-sensor` mappen.

1. Lägg till följande- `import` instruktioner i början av filen **environmental_sensor. py** :

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

1. Lägg till följande asynkrona `main` funktion och variabel deklarationer i filen:

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

    Uppdatera plats hållarna `{your Scope ID}` , `{your Device ID}` och `{your Primary Key}` med de värden som du antecknade tidigare. I ett verkligt program bör du inte hårdkoda den här informationen i programmet.

    Alla följande funktions definitioner och kod kapslas i `main` funktionen.

1. Lägg till följande två funktioner i- `main` funktionen för att registrera enheten och ansluta den till ditt IoT Central-program. Registrering använder Azure Device Provisioning-tjänsten:

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

1. Lägg till följande funktion i `main` funktionen för att skicka telemetri till ditt IoT Central-program:

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

    Namnen på telemetri-objekten ( `temp` och `humid` ) måste matcha namnen som används i enhets mal len.

1. Lägg till följande funktioner i `main` funktionen för att hantera kommandon som anropas från ditt IoT Central-program:

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

    Namnen på kommandona ( `blink` , `turnon` , `turnoff` och `rundiagnostics` ) måste matcha namnen som används i enhets mal len.

    För närvarande använder IoT Central inte det svars schema som definierats i enhetens kapacitets modell. För ett synkront kommando kan svars nytto lasten vara vilken giltig JSON som helst. För ett asynkront kommando ska enheten returnera ett 202-svar omedelbart följt av den rapporterande egenskaps uppdateringen när arbetet är klart. Formatet för den rapporterade egenskaps uppdateringen är:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    En operatör kan visa svars nytto lasten i kommando historiken.

1. Lägg till följande funktioner i `main` funktionen för att hantera egenskaps uppdateringar som skickas från ditt IoT Central-program:

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

    När operatorn ställer in en skrivbar egenskap i IoT Central-programmet använder programmet en enhet med dubbla önskade egenskaper för att skicka värdet till enheten. Enheten svarar sedan med en enhets dubbla rapporterad egenskap. När IoT Central tar emot det rapporterade egenskap svärdet uppdateras egenskaps läget med statusen **synkroniserad**.

    Namnen på egenskaperna ( `name` och `brightness` ) måste matcha namnen som används i enhets mal len.

1. Lägg till följande funktioner i- `main` funktionen för att kontrol lera programmet:

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

1. Spara filen **environmental_sensor. py** .

## <a name="run-your-python-application"></a>Kör din python-app

Starta enhets klient programmet genom att köra följande kommando i din kommando rads miljö:

```cmd
python3 environmental_sensor.py
```

Du kan se att enheten ansluter till ditt Azure IoT Central-program och börjar skicka telemetri:

![Kör klient programmet](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Du kan se hur enheten svarar på kommandon och egenskaps uppdateringar:

![Observera klient programmet](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>Nästa steg

Som en enhets utvecklare nu har du lärt dig grunderna för hur du skapar en enhet med hjälp av Node. js, men vissa föreslagna nästa steg är att:

* Lär dig hur du ansluter en riktig enhet till IoT Central i [ansluta en MXChip IoT DevKit-enhet till Azure IoT Central Application](./howto-connect-devkit.md) instruktion-artikeln.
* Läs [Vad är enhets mallar?](./concepts-device-templates.md) om du vill lära dig mer om rollen hets mallar när du implementerar din enhets kod.
* Läs [bli ansluten till Azure IoT Central](./concepts-get-connected.md) om du vill veta mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhets anslutningar.

Om du föredrar att fortsätta med en uppsättning IoT Central själv studie kurser och lära dig mer om hur du skapar en IoT Central lösning, se:

> [!div class="nextstepaction"]
> [Mall för att skapa en gateway-enhet](./tutorial-define-gateway-device-type.md)
