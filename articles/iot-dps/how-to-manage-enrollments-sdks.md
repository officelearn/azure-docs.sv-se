---
title: Hantera enhetsregistreringar med Azure DPS SDK:er
description: Hantera enhetsregistreringar i IoT Hub Device Provisioning Service (DPS) med hjälp av Service SDK:er
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975082"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Hantera enhetsregistreringar med Azure Device Provisioning Service SDK:er
En *enhetsregistrering* skapar en post för en enskild enhet eller en grupp enheter som någon gång kan registrera sig hos enhetsetableringstjänsten. Registreringsposten innehåller den första önskade konfigurationen för enheten/enheten som en del av registreringen, inklusive den önskade IoT-hubben. Den här artikeln visar hur du hanterar enhetsregistreringar för din etableringstjänst programmässigt med hjälp av Azure IoT Provisioning Service SDK:er.  SDK:erna är tillgängliga på GitHub i samma databas som Azure IoT SDK:er.

## <a name="prerequisites"></a>Krav
* Hämta anslutningssträngen från instansen för enhetsetableringstjänst.
* Hämta enhetens säkerhetsartefakter för den [attesteringsmekanism](concepts-security.md#attestation-mechanism) som används:
    * [**Trusted Platform Module (TPM):**](/azure/iot-dps/concepts-security#trusted-platform-module)
        * Individuell registrering: Registrerings-ID och TPM-bekräftelsenyckel från en fysisk enhet eller från TPM Simulator.
        * Registreringsgruppen gäller inte TPM-intyget.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Individuell registrering: [Leaf-certifikatet](/azure/iot-dps/concepts-security) från den fysiska enheten eller från SDK [DICE Emulator.](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)
        * Registreringsgrupp: [Certifikatutfärdar-/rotcertifikatet](/azure/iot-dps/concepts-security#root-certificate) eller [det mellanliggande certifikatet](/azure/iot-dps/concepts-security#intermediate-certificate)som används för att producera enhetscertifikat på en fysisk enhet.  Det kan också genereras från SDK DICE emulator.
* Exakta API-anrop kan vara olika på grund av språkskillnader. Läs exemplen på GitHub för mer information:
   * [Exempel på Java-etableringstjänstklient](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Nod.js etableringstjänstklientexempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Exempel på .NET-etableringstjänstklient](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Skapa en enhetsregistrering
Det finns två sätt att registrera dina enheter med etableringstjänsten:

* En **registreringsgrupp** är en post för en grupp enheter som delar en gemensam attestationmekanism för X.509-certifikat, signerad av [rotcertifikatet](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) eller det [mellanliggande certifikatet](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Vi rekommenderar att du använder en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som alla går till samma klient. Observera att du bara kan registrera enheter som använder X.509 attestation mekanism som *registreringsgrupper*. 

    Du kan skapa en registreringsgrupp med SDK:erna efter det här arbetsflödet:

    1. För registreringsgrupp använder attestation-mekanismen X.509-rotcertifikatet.  Anropa Service SDK API ```X509Attestation.createFromRootCertificate``` med rotcertifikat för att skapa intyg för registrering.  X.509-rotcertifikat finns antingen i en PEM-fil eller som en sträng.
    1. Skapa en ```EnrollmentGroup``` ny ```attestation``` variabel med ```enrollmentGroupId```den skapade och en unik .  Du kan också ange ```Device ID```parametrar ```IoTHubHostName``` ```ProvisioningStatus```som , , .
    2. Anropa Service SDK API ```createOrUpdateEnrollmentGroup``` i serveringsprogrammet med ```EnrollmentGroup``` för att skapa en registreringsgrupp.

* En **enskild registrering** är en post för en enda enhet som kan registrera. Enskilda registreringar kan använda antingen X.509-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesteringsmekanismer. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika inledande konfigurationer, eller för enheter som bara kan använda SAS-token via TPM eller virtuell TPM som attestation mekanism. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

    Du kan skapa en enskild registrering med SDK:erna efter det här arbetsflödet:
    
    1. Välj ```attestation``` din mekanism, som kan vara TPM eller X.509.
        1. **TPM**: Med hjälp av bekräftelsenyckeln från en fysisk enhet eller från TPM Simulator som indata kan du anropa Service SDK API ```TpmAttestation``` för att skapa intyg för registrering. 
        2. **X.509**: Med klientcertifikatet som indata kan du ```X509Attestation.createFromClientCertificate``` anropa Service SDK API för att skapa intyg för registrering.
    2. Skapa en ```IndividualEnrollment``` ny variabel ```attestation``` med hjälp ```registrationId``` av den skapade och en unik som indata, som finns på din enhet eller genereras från TPM Simulator.  Du kan också ange ```Device ID```parametrar ```IoTHubHostName``` ```ProvisioningStatus```som , , .
    3. Anropa Service SDK API ```createOrUpdateIndividualEnrollment``` i serveringsprogrammet med ```IndividualEnrollment``` för att skapa en enskild registrering.

När du har skapat en registrering returnerar enhetsetableringstjänsten ett registreringsresultat. Det här arbetsflödet visas i de exempel [som tidigare nämnts](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Uppdatera en registreringspost

När du har skapat en registreringspost kanske du vill uppdatera registreringen.  Potentiella scenarier är att uppdatera önskad egenskap, uppdatera attestation-metoden eller återkalla enhetsåtkomst.  Det finns olika API:er för individuell registrering och gruppregistrering, men ingen skillnad för attesteringsmekanism.

Du kan uppdatera en registreringspost efter det här arbetsflödet:
* **Enskild registrering**:
    1. Hämta den senaste registreringen från etableringstjänsten ```getIndividualEnrollment```först med Service SDK API .
    2. Ändra parametern för den senaste registreringen efter behov. 
    3. Med den senaste registreringen anropar du Service SDK API ```createOrUpdateIndividualEnrollment``` för att uppdatera registreringsposten.
* **Gruppregistrering:**
    1. Hämta den senaste registreringen från etableringstjänsten ```getEnrollmentGroup```först med Service SDK API .
    2. Ändra parametern för den senaste registreringen efter behov.
    3. Med den senaste registreringen anropar du Service SDK API ```createOrUpdateEnrollmentGroup``` för att uppdatera registreringsposten.

Det här arbetsflödet visas i de exempel [som tidigare nämnts](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Ta bort en registreringspost

* **Individuell registrering** kan tas bort genom ```deleteIndividualEnrollment``` att ```registrationId```anropa Service SDK API med .
* **Gruppregistrering** kan tas bort genom att ```deleteEnrollmentGroup``` ```enrollmentGroupId```anropa Service SDK API med .

Det här arbetsflödet visas i de exempel [som tidigare nämnts](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Massåtgärd på enskilda registreringar

Du kan utföra massåtgärder för att skapa, uppdatera eller ta bort flera enskilda registreringar efter det här arbetsflödet:

1. Skapa en variabel som ```IndividualEnrollment```innehåller flera .  Genomförandet av denna variabel är olika för varje språk.  Mer information finns i massåtgärdsexemplet på GitHub.
2. Anropa Service SDK ```runBulkOperation``` ```BulkOperationMode``` API med en för önskad åtgärd och din variabel för enskilda registreringar. Fyra lägen stöds: skapa, uppdatera, updateIfMatchEtag och ta bort.

När du har utfört en åtgärd returnerar enhetsetableringstjänsten ett massåtgärdsresultat.

Det här arbetsflödet visas i de exempel [som tidigare nämnts](#prerequisites).
