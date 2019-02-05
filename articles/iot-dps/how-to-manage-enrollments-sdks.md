---
title: 'Hantera enhetsregistreringar med Azure Device Provisioning Service-SDK: er | Microsoft Docs'
description: 'Hantera enhetsregistreringar i IoT Hub Device Provisioning-tjänsten med SDK: er för tjänsten'
author: yzhong94
ms.author: yizhon
ms.date: 04/04/18
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: 1c7fa798c2e767aa6a21b3c56da6f69b4d3a1406
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732363"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Hantera enhetsregistreringar med Azure Device Provisioning Service-SDK: er
En *enhetsregistrering* skapar en post för en enstaka enhet eller en grupp av enheter som kan någon gång registreras med Device Provisioning-tjänsten. Registreringsposten innehåller inledande önskad konfiguration för enheter som en del av att registreringen, inklusive önskad IoT hub. Den här artikeln visar hur du hanterar enhetsregistreringar för etableringstjänsten genom programmering med Azure IoT Provisioning Service SDK.  SDK: erna finns på GitHub i samma lagringsplats som Azure IoT SDK: er.

## <a name="prerequisites"></a>Förutsättningar
* Hämta anslutningssträngen från din instans av Device Provisioning-tjänsten.
* Hämta enheten säkerhetsartefakterna för den [attesteringsmetod](concepts-security.md#attestation-mechanism) används:
    * [**Trusted Platform Module (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Enskild registrering: Registrerings-ID och TPM-bekräftelsenyckeln från en fysisk enhet eller från TPM-simulatorn.
        * Grupp för registrering gäller inte för TPM-attestering.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Enskild registrering: Den [lövcertifikat](/azure/iot-dps/concepts-security) från fysisk enhet eller från SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulatorn.
        * Grupp för registrering: Den [CA/rotcertifikat](/azure/iot-dps/concepts-security#root-certificate) eller [mellanliggande certifikat](/azure/iot-dps/concepts-security#intermediate-certificate)används för att skapa certifikat på en fysisk enhet.  Det kan också genereras från SDK DICE-emulator.
* Exakta API-anrop kan skilja sig på grund av språkskillnader. Exemplen på GitHub mer i:
   * [Java Provisioning Service Client-exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js Provisioning Service Client-exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET provisioning Service Client-exempel](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Skapa en registrering av enheter
Det finns två sätt som du kan registrera dina enheter med etableringstjänsten:

* En **registreringsgruppen** finns en post för en grupp av enheter som delar en gemensam attesteringsmetod av X.509-certifikat som signerats av den [rotcertifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) eller [mellanliggande certifikat ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Vi rekommenderar en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration eller för enheter ska till samma klient. Observera att du endast registrera enheter som använder X.509-attesteringsmetod som *registreringsgrupper*. 

    Du kan skapa en grupp för registrering med SDK: erna följa det här arbetsflödet:

    1. För registreringsgruppen som använder attesteringsmetod X.509-rotcertifikat.  Anropa API för tjänst-SDK ```X509Attestation.createFromRootCertificate``` med rotcertifikatet för att skapa attestering för registrering.  X.509-rotcertifikat finns i antingen en PEM-fil eller som en sträng.
    1. Skapa en ny ```EnrollmentGroup``` variabeln med den ```attestation``` skapade och en unik ```enrollmentGroupId```.  Du kan ange parametrar som ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Anropa API för tjänst-SDK ```createOrUpdateEnrollmentGroup``` i backend-program med ```EnrollmentGroup``` att skapa en grupp för registrering.

* En **enskild registrering** finns en post för en enskild enhet som kan registreras. Enskilda registreringar kan använda X.509-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesteringsmekanismer. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som endast kan använda SAS-token via TPM eller virtuell TPM som attesteringsmetod. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

    Du kan skapa en enskild registrering med SDK: erna följa det här arbetsflödet:
    
    1. Välj din ```attestation``` mekanism som kan vara TPM eller X.509.
        1. **TPM**: Använda bekräftelsenyckeln från en fysisk enhet eller från TPM-simulatorn som indata, kan du anropa API för tjänst-SDK ```TpmAttestation``` skapa attestering för registrering. 
        2. **X.509**: Använder klientcertifikat som indata, kan du anropa API för tjänst-SDK ```X509Attestation.createFromClientCertificate``` skapa attestering för registrering.
    2. Skapa en ny ```IndividualEnrollment``` variabeln med hjälp av den ```attestation``` skapade och en unik ```registrationId``` som indata, som på din enhet eller genereras från TPM-simulatorn.  Du kan ange parametrar som ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Anropa API för tjänst-SDK ```createOrUpdateIndividualEnrollment``` i backend-program med ```IndividualEnrollment``` att skapa en enskild registrering.

När du har skapat en registrering, returnerar ett resultat för registrering i Device Provisioning-tjänsten. Det här arbetsflödet visas i exemplen [tidigare nämnts](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Uppdatera en registreringspost

När du har skapat en registreringspost kan du uppdatera registreringen.  Möjliga scenarier är uppdaterar önskad egenskap, uppdatera metoden attestering eller återkalla Enhetsåtkomst.  Det finns olika API: er för enskild registrering och gruppregistrering, men ingen åtskillnad för attesteringsmetod.

Du kan uppdatera en registreringspost följa det här arbetsflödet:
* **Enskild registrering**:
    1. Hämta den senaste registreringen från etableringstjänsten första med API för tjänst-SDK ```getIndividualEnrollment```.
    2. Ändra parametern till den senaste registreringen vid behov. 
    3. Med den senaste registreringen anropa API för tjänst-SDK ```createOrUpdateIndividualEnrollment``` att uppdatera din post för registrering.
* **Gruppregistrering**:
    1. Hämta den senaste registreringen från etableringstjänsten första med API för tjänst-SDK ```getEnrollmentGroup```.
    2. Ändra parametern till den senaste registreringen vid behov.
    3. Med den senaste registreringen anropa API för tjänst-SDK ```createOrUpdateEnrollmentGroup``` att uppdatera din post för registrering.

Det här arbetsflödet visas i exemplen [tidigare nämnts](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Ta bort en registreringspost

* **Enskild registrering** kan tas bort genom att anropa API för tjänst-SDK ```deleteIndividualEnrollment``` med ```registrationId```.
* **Gruppregistrering** kan tas bort genom att anropa API för tjänst-SDK ```deleteEnrollmentGroup``` med ```enrollmentGroupId```.

Det här arbetsflödet visas i exemplen [tidigare nämnts](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Bulkåtgärd på enskilda registreringar

Du kan utföra bulkåtgärd för att skapa, uppdatera eller ta bort flera enskilda registreringar följa det här arbetsflödet:

1. Skapa en variabel som innehåller flera ```IndividualEnrollment```.  Implementering av den här variabeln är olika för varje språk.  Granska bulk åtgärden exemplet på GitHub för information.
2. Anropa API för tjänst-SDK ```runBulkOperation``` med en ```BulkOperationMode``` för önskad åtgärd och variabeln för enskilda registreringar. Finns stöd för tre lägen: skapa, uppdatera, updateIfMatchEtag, och ta bort.

När du har har utfört en åtgärd, returneras Device Provisioning-tjänsten ett resultat för bulk-åtgärden.

Det här arbetsflödet visas i exemplen [tidigare nämnts](#prerequisites).
