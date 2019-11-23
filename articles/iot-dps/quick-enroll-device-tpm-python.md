---
title: Enroll TPM device to Azure Device Provisioning Service using Python
description: Quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service using Python provisioning service SDK. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: ac65758f69fc827e36f734f9dee3117d519dca6a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423110"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Quickstart: Enroll TPM device to IoT Hub Device Provisioning Service using Python provisioning service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

De här stegen visar hur du programmässigt skapar en enskild registrering för en TPM-enhet i Azure IoT Hub Device Provisioning-tjänsten med [SDK för Python-etableringtjänst](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) med hjälp av ett Python-exempelprogram. Python-tjänst-SDK fungerar på både Windows- och Linux-datorer men i den här artikeln används en Windows-utvecklingsdator för att gå igenom registreringsprocessen.

Se till att [konfigurera IoT Hub Device Provisioning-tjänsten med Azure-portalen](./quick-setup-auto-provision.md) innan du fortsätter.


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Ladda ned och installera [Python 2.x eller 3.x](https://www.python.org/downloads/). Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. 

1. Välj ett av följande alternativ:

    - Bygg och kompilera **Azure IoT Python SDK**. Skapa Python-paketen med hjälp av [de här instruktionerna](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md). Om du använder Windows OS installerar du även [Visual C++ redistributable package](https://www.microsoft.com/download/confirmation.aspx?id=48145) så att du kan använda interna DLL:er från Python.

    - [Installera eller uppgradera *pip*, pakethanteringssystemet för Python](https://pip.pypa.io/en/stable/installing/) och installera paketet med följande kommando:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Du behöver bekräftelsenyckeln för din enhet. Om du har följt snabbstarten om att [skapa och etablera en simulerad enhet](quick-create-simulated-device.md) för att skapa en simulerad TPM-enhet använder du nyckeln som skapats för den enheten. Du kan också använda följande bekräftelsenyckel som medföljer SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Ändra Python-exempelkoden

I det här avsnittet visas hur du lägger till etableringsinformationen för TPM-enheten i exempelkoden. 

1. Skapa en ny **TpmEnrollment.py**-fil med en texteditor.

1. Lägg till följande `import`-instruktioner och variabler längst upp i **TpmEnrollment.py**-filen. Byt sedan `dpsConnectionString` mot din anslutningssträng som finns under **Policyer för delad åtkomst** i **Device Provisioning-tjänsten** på **Azure-portalen**. Byt ut `endorsementKey` med värdet du tidigare antecknade i [Förbereda miljön](quick-enroll-device-tpm-python.md#prepareenvironment). Skapa slutligen ett unikt `registrationid` och se till att det endast innehåller små bokstäver, siffror och bindestreck.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Lägg till följande funktion och funktionsanrop för att implementera skapandet av registreringsgruppen:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Spara och stäng **TpmEnrollment.py**-filen.
 

## <a name="run-the-sample-tpm-enrollment"></a>Kör exemplet på TPM-registrering

1. Öppna en kommandotolk och kör skriptet.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Se utdata för att se om registreringen har lyckats.

1. Navigera till etableringstjänsten i Azure Portal. Välj **Hantera registreringar**. Observera att TPM-enheten visas under fliken **Enskilda registreringar**, med namnet som `registrationid` skapade tidigare. 

    ![Verifiera lyckad TPM-registrering i portalen](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Rensa resurser
If you plan to explore the Java service sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Stäng utdatafönstret för Python-exemplet på datorn.
1. Om du har skapat en simulerad TPM-enhet stänger du TPM-simulatorfönstret.
1. Navigate to your Device Provisioning service in the Azure portal, select **Manage enrollments**, and then select the **Individual Enrollments** tab. Select the check box next to the *Registration ID* for the enrollment entry you created using this quickstart, and press the **Delete** button at the top of the pane.


## <a name="next-steps"></a>Nästa steg
In this quickstart, you’ve programmatically created an individual enrollment entry for a TPM device, and, optionally, created a TPM simulated device on your machine and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen.

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)
