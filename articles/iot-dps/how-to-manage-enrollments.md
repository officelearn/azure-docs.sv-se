---
title: Hantera enhetsregistreringar för Azure IoT Hub Device Provisioning Service i Azure-portalen
description: Hantera enhetsregistreringar för din DPS (Device Provisioning Service) i Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974946"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Hantera enhetsregistreringar med Azure Portal

En *enhetsregistrering* skapar en post för en enskild enhet eller en grupp enheter som någon gång kan registrera sig hos Azure IoT Hub Device Provisioning Service. Registreringsposten innehåller den första önskade konfigurationen för enheten/enheten som en del av registreringen, inklusive den önskade IoT-hubben. I den här artikeln visas hur du hanterar enhetsregistreringar för etableringstjänsten.


## <a name="create-a-device-enrollment"></a>Skapa en enhetsregistrering

Det finns två sätt att registrera dina enheter med etableringstjänsten:

* En **registreringsgrupp** är en post för en grupp enheter som delar en gemensam attestationmekanism för X.509-certifikat, signerade av samma signeringscertifikat, som kan vara [rotcertifikatet](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) eller [det mellanliggande certifikatet](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), som används för att producera enhetscertifikat på fysisk enhet. Vi rekommenderar att du använder en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som alla går till samma klient. Observera att du bara kan registrera enheter som använder X.509 attestation mekanism som *registreringsgrupper*. 

    Du kan skapa en registreringsgrupp i portalen för en grupp enheter med hjälp av följande steg:

  1. Logga in på Azure-portalen och klicka på **Alla resurser** från menyn till vänster.  
  1. Klicka på den enhetsetableringstjänst som du vill registrera enheten till från listan över resurser.  
  1. I din etableringstjänst:  
     a. Klicka på **Hantera registreringar**och välj sedan fliken **Registreringsgrupper.**  
     b. Klicka på knappen **Lägg till** högst upp.  
     c. När panelen Lägg till registreringsgrupp visas anger du informationen för registreringslisteposten.  **Gruppnamn** krävs. Välj också "CERTIFIKATUTfärdare eller Mellanliggande" för **certifikattyp**och ladda upp roten **Primärt certifikat** för gruppen av enheter.  
     d. Klicka på **Spara**. När registreringsgruppen har skapats bör gruppnamnet visas under fliken **Registreringsgrupper.**  

     [![Registreringsgrupp i portalen](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* En **enskild registrering** är en post för en enda enhet som kan registrera. Enskilda registreringar kan använda antingen x509-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesteringsmekanismer. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika inledande konfigurationer, eller för enheter som bara kan använda SAS-token via TPM eller virtuell TPM som attestation mekanism. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

    Du kan skapa en enskild registrering i portalen med hjälp av följande steg:

    1. Logga in på Azure-portalen och klicka på **Alla resurser** från menyn till vänster.
    1. Klicka på den enhetsetableringstjänst som du vill registrera enheten till från listan över resurser.
    1. I din etableringstjänst:  
       a. Klicka på **Hantera registreringar**och välj sedan fliken **Enskilda registreringar.**  
       b. Klicka på knappen **Lägg till** högst upp.   
       c. När panelen Lägg till registrering visas anger du informationen för registreringslisteposten. Välj först **attesteringsmekanismen** för enheten (X.509 eller TPM). X.509 attestation kräver att du laddar upp leaf **Primär certifikat** för enheten. TPM kräver att du anger **attestationsnyckeln** och **registrerings-ID:t** för enheten.  
       d. Klicka på **Spara**. När registreringsgruppen har skapats bör du se din enhet visas under fliken **Enskilda registreringar.**  

       [![Individuell registrering i portalen](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Uppdatera en registreringspost
Du kan uppdatera en befintlig registreringspost i portalen med hjälp av följande steg:

1. Öppna tjänsten Enhetsetablering i Azure-portalen och klicka på **Hantera registreringar**. 
1. Navigera till den registreringspost som du vill ändra. Klicka på posten som öppnar en sammanfattande information om enhetsregistreringen. 
1. På den här sidan kan du ändra andra objekt än säkerhetstyp och autentiseringsuppgifter, till exempel IoT-hubben som enheten ska länkas till, samt enhets-ID. Du kan också ändra det ursprungliga enhetstvillingtillståndet. 
1. När du är klar klickar du på **Spara** för att uppdatera enhetsregistreringen. 

    ![Uppdatera registreringen i portalen](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Ta bort en enhetsregistrering
Om enheten/enheten inte behöver etableras till någon IoT-hubb kan du ta bort den relaterade registreringsposten i portalen med hjälp av följande steg:

1. Öppna tjänsten Enhetsetablering i Azure-portalen och klicka på **Hantera registreringar**. 
1. Navigera till och välj den registreringspost som du vill ta bort. 
1. Klicka på knappen **Ta bort** högst upp och välj sedan **Ja** när du uppmanas att bekräfta. 
1. När åtgärden är klar ser du din post tas bort från listan över enhetsregistreringar. 
 
    ![Ta bort registrering i portalen](./media/how-to-manage-enrollments/remove-enrollment.png)


