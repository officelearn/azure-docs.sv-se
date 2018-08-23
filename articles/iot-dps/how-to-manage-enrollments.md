---
title: Hantera enhetsregistreringar med Azure-portalen | Microsoft Docs
description: Hantera enhetsregistreringar för Device Provisioning-tjänsten i Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 0970131c4b674fd864ad574b0f749cd6d7f6f2a2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054606"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Hantera enhetsregistreringar med Azure Portal

En *enhetsregistrering* skapar en post för en enstaka enhet eller en grupp av enheter som kan någon gång registreras med Azure IoT Hub Device Provisioning-tjänsten. Registreringsposten innehåller inledande önskad konfiguration för enheter som en del av att registreringen, inklusive önskad IoT hub. Den här artikeln visar hur du hanterar enhetsregistreringar för etableringstjänsten.


## <a name="create-a-device-enrollment"></a>Skapa en registrering av enheter

Det finns två sätt som du kan registrera dina enheter med etableringstjänsten:

* En **registreringsgruppen** finns en post för en grupp av enheter som delar en gemensam attesteringsmetod av X.509-certifikat som signerats av samma signeringscertifikatet, vilket kan vara den [rotcertifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) eller [mellanliggande certifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)används för att skapa certifikat på fysiska enheten. Vi rekommenderar en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration eller för enheter ska till samma klient. Observera att du endast registrera enheter som använder X.509-attesteringsmetod som *registreringsgrupper*. 

    Du kan skapa en grupp för registrering i portalen för en grupp av enheter med hjälp av följande steg:

    1. Logga in på Azure-portalen och klicka på **alla resurser** på den vänstra menyn.  
    1. Klicka på Device Provisioning-tjänsten som du vill registrera din enhet på listan över resurser.  
    1. I etableringstjänsten:  
       a. Klicka på **hantera registreringar**och välj sedan den **Registreringsgrupper** fliken.  
       b. Klicka på knappen **Lägg till** högst upp.  
       c. Ange information för registreringslistan när panelen ”Lägg till grupp för registrering” visas.  **Gruppnamn** krävs. Även välja ”CA: N eller mellanliggande” för **certifikattyp**, och ladda upp roten **primära** för gruppen med enheter.  
       d. Klicka på **Spara**. Du bör se gruppnamnet visas under gruppen har skapats av din grupp för registrering i **Registreringsgrupper** fliken.  

       [![Grupp för registrering i portalen](./media/how-to-manage-enrollments/group-enrollment.png)] (. / media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* En **enskild registrering** finns en post för en enskild enhet som kan registreras. Enskilda registreringar kan använda antingen x509 certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesteringsmekanismer. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som endast kan använda SAS-token via TPM eller virtuell TPM som attesteringsmetod. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

    Du kan skapa en enskild registrering i portalen med följande steg:

    1. Logga in på Azure-portalen och klicka på **alla resurser** på den vänstra menyn.
    1. Klicka på Device Provisioning-tjänsten som du vill registrera din enhet på listan över resurser.
    1. I etableringstjänsten:  
       a. Klicka på **hantera registreringar**och välj sedan den **enskilda registreringar** fliken.  
       b. Klicka på knappen **Lägg till** högst upp.   
       c. Ange information för registreringslistan när panelen ”Lägg till registrering” visas. Välj först styrkande av **mekanism** för enheten (X.509 eller TPM). X.509-attestering måste du överföra lägsta **primära** för enheten. TPM måste du ange den **attestering nyckeln** och **registrerings-ID** för enheten.  
       d. Klicka på **Spara**. Gruppen har skapats av din grupp för registrering bör du se enheten visas under den **enskilda registreringar** fliken.  

       [![Enskild registrering i portalen](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Uppdatera en registreringspost
Du kan uppdatera en befintlig registreringspost i portalen med följande steg:

1. Öppna Device Provisioning-tjänsten i Azure-portalen och klicka på **hantera registreringar**. 
1. Gå till registreringsposten som du vill ändra. Klicka på posten som öppnar en översiktlig information om registreringen av din enhet. 
1. På den här sidan kan du ändra andra objekt än säkerhetstypen och autentiseringsuppgifter, till exempel IoT-hubben som enheten ska kopplas till, samt enhets-ID. Du kan också ändra den inledande enhetstvillingstatus. 
1. När du har slutförts, klickar du på **spara** att uppdatera registreringen av din enhet. 

    ![Uppdatera registrering i portalen](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Ta bort en registrering av enheter
I fall där dina enheter inte behöver etableras till alla IoT-hubb kan du ta bort registreringsposten relaterade i portalen med följande steg:

1. Öppna Device Provisioning-tjänsten i Azure-portalen och klicka på **hantera registreringar**. 
1. Navigera till och markera den registreringsposten som du vill ta bort. 
1. Klicka på den **ta bort** högst upp och välj sedan **Ja** när du uppmanas att bekräfta. 
1. När åtgärden har slutförts, visas ditt bidrag som tagits bort från listan över enhetsregistreringar. 
 
    ![Ta bort registrering i portalen](./media/how-to-manage-enrollments/remove-enrollment.png)


