---
title: "Etablera en enhet med Azure IoT Hub etablering av tjänst | Microsoft Docs"
description: "Etablera enheten till en enda IoT-hubb med hjälp av Azure IoT-hubb enheten Etableringstjänsten"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Etablerar du enheten till en IoT-hubb med hjälp av Azure IoT-hubb enheten Etableringstjänsten

Du lärt dig hur du ställer in en enhet för att ansluta till tjänsten Enhetsetableringen i föregående självstudierna. I kursen får du lära dig hur du använder den här tjänsten för att etablera enheten till en enda IoT-hubb med  **_registrering listor_**. I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

## <a name="prerequisites"></a>Krav

Innan du fortsätter måste du konfigurera din enhet och dess *maskinvarusäkerhetsmodul* enligt beskrivningen i självstudierna [konfigurera en enhet för att etablera med hjälp av Azure IoT-hubb Device etablering Service](./tutorial-set-up-device.md).


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrera enheten

Det här steget måste du lägga till enhetens unik säkerhet artefakter till enheten Etableringstjänsten. Dessa artefakter säkerhet är följande:

- För TPM-baserade enheter:
    - Den *bekräftelsenyckel* som är unik för varje TPM-chip eller simuleringen. Läs den [förstå bekräftelsenyckel för TPM](https://technet.microsoft.com/library/cc770443.aspx) för mer information.
    - Den *registrerings-ID* som används för att unikt identifiera en enhet i namnområdet/omfattning. Detta kan eller kan inte vara samma som enhets-ID. ID: T är obligatorisk för varje enhet. Registrerings-ID kan härledas från TPM, till exempel en SHA-256-hash för TPM-bekräftelsenyckel för TPM-baserade enheter.

    ![Registreringsinformation för TPM i portalen](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- För X.509-baserade enheter:
    - Den [certifikat utfärdat till X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) chip eller simulering i form av antingen en *.pem* eller en *.cer* fil. För enskilda registrering måste du använda den *signerat certifikat* för X.509-systemet, medan för registrering av grupper du behöver använda den *rotcertifikat*.

    ![Registreringsinformation för X.509 i portalen](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


Det finns två sätt att registrera enheten till tjänsten etablering enhet:

- **Registrering grupper** representerar en grupp av enheter som delar en mekanism för specifika attestering. Vi rekommenderar en grupp för registrering för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter alla kommer att samma klientorganisation.

    ![Registrering av grupper för X.509 i portalen](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Enskilda registreringar** representerar en post för en enda enhet som kan registrera med enheten Etableringstjänsten. Enskilda registreringar kan använda antingen x509 certifikat eller SAS token (i TPM verkliga eller virtuella) attestering funktioner. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som bara kan använda SAS-token via TPM eller virtuella TPM som mekanismen för attestering. Enskilda registreringar kanske önskade IoT-hubb enhets-ID angetts.

Här följer stegen för att registrera enheten i portalen:

1. Observera säkerhet artefakter för HSM på enheten. Du kan behöva använda API: er som anges i avsnittet [extrahera säkerhet artefakter](./tutorial-set-up-device.md#extractsecurity) av föregående kursen finns i en utvecklingsmiljö.

1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten.

1. På sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Manage enrollments** (Hantera registreringar). Välj antingen **enskilda registreringar** fliken eller **registrering grupper** fliken enligt din enhetskonfiguration. Klicka på den **Lägg till** längst upp. Välj **TPM** eller **X.509** som identitet för bestyrkande *mekanism*, och ange rätt säkerhet artefakter som beskrivits tidigare. Du kan ange en ny **IoT-hubb enhets-ID**. Klicka på knappen **Spara** när det är klart. 

1. När enheten har registrerats, bör du se den visas i portalen enligt följande:

    ![Lyckad TPM-registrering i portalen](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>Starta enheten

Följande inställningar är nu klar för registrering av enheten:

1. Din enhet eller grupp av enheter som har registrerats till tjänsten Enhetsetableringen och 
2. Enheten är klar med HSM-chip konfigurerats och är tillgänglig via det program som använder enheten Etableringstjänsten klient-SDK.

Starta enhet så att ditt klientprogram att starta registreringen med tjänsten Enhetsetableringen.  


## <a name="verify-the-device-is-registered"></a>Kontrollera att enheten är registrerad

När din enhet Starter följande åtgärder ska utföras. Se exempelprogrammet TPM simulator [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) för mer information. 

1. Enheten skickar en begäran om registrering till din enhet etableringstjänsten.
2. Etablering av tjänst skickar tillbaka en registrering utmaning som enheten svarar för TPM-enheter. 
3. På lyckad registrering skickar enheten Etableringstjänsten IoT-hubb URI, enhets-ID och den krypterade nyckeln tillbaka till enheten. 
4. IoT-hubb klientprogrammet på enheten sedan ansluter till din hubb. 
5. Lyckad anslutning till hubben, bör du se enheten visas i IoT-hubben **enheten Explorer**. 

    ![Anslutning till hubb i portalen](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

Gå vidare till nästa kurs att lära dig att etablera flera enheter över Utjämning av nätverksbelastning hubs. 

> [!div class="nextstepaction"]
> [Etablera enheter över Utjämning av nätverksbelastning IoT-hubbar](./tutorial-provision-multiple-hubs.md)
