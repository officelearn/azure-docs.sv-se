---
title: Hantera enhetsregistrering med Azure-portalen | Microsoft Docs
description: Så här hanterar du enhetsregistreringar för din DP-tjänst i Azure Portal
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/05/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 55486d9a37968351f5313c708e9ef26e5b89063c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Så här hanterar du enhetsregistrering med Azure-portalen

En *enhetsregistrering* skapas en post för en enstaka enhet eller en grupp av enheter som någon gång registrera med Azure IoT-hubb Device etablering Service. Registreringspost innehåller inledande önskad konfiguration för enheter som en del av att registreringen, inklusive önskade IoT-hubben. Den här artikeln visar hur du hanterar enhetsregistreringar för etablering tjänsten.


## <a name="create-a-device-enrollment"></a>Skapa en registrering av enheter

Det finns två sätt som du kan registrera dina enheter med tjänsten etablering:

* En **registrering grupp** finns en post för en grupp av enheter som delar en gemensam attestering mekanismen för X.509-certifikat som signerats av samma signeringscertifikatet, vilket kan vara den [rotcertifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) eller [mellanliggande certifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), som används för att skapa certifikat för enhet på den fysiska enheten. Vi rekommenderar en grupp för registrering för ett stort antal enheter som delar en önskad inledande konfiguration eller för enheter alla kommer att samma klientorganisation. Observera att du bara kan registrera enheter som använder mekanismen för attestering X.509 som *registrering grupper*. 

    Du kan skapa en grupp för registrering i portalen för en grupp av enheter med hjälp av följande steg:

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.  
    2. Klicka på Enhetsetableringen tjänsten som du vill registrera din enhet till i listan över resurser.  
    3. I din etablering tjänst:  
       a. Klicka på **hantera registreringar**och välj den **registrering grupper** fliken.  
       b. Klicka på knappen **Lägg till** högst upp.  
       c. Ange information för registrering listpost när panelen ”Lägg till registrering grupp” visas.  **Gruppnamn** krävs. Även välja ”Certifikatutfärdare eller mellanliggande” för **certifikattyp**, och ladda upp roten **certifikatet för primär** för grupp av enheter.  
       d. Klicka på **Spara**. På har skapats för din registrering grupp, bör du se gruppnamnet visas under den **registrering grupper** fliken.  

       [![Registrering grupp i portalen](./media/how-to-manage-enrollments/group-enrollment.png)] (. / media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* En **enskilda registrering** finns en post för en enda enhet som kan registrera. Enskilda registreringar kan använda antingen x509 certifikat eller SAS tokens (från en fysisk eller virtuell TPM) attestering funktioner. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som bara kan använda SAS-token via TPM eller virtuella TPM som mekanismen för attestering. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

    Du kan skapa en enskild registrering på portalen med följande steg:

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.
    2. Klicka på Enhetsetableringen tjänsten som du vill registrera din enhet till i listan över resurser.
    3. I din etablering tjänst:  
       a. Klicka på **hantera registreringar**och välj den **enskilda registreringar** fliken.  
       b. Klicka på knappen **Lägg till** högst upp.   
       c. Ange information för registrering listpost när panelen ”Lägg till registrering” visas. Först välja bestyrkande **mekanism** för enheten (X.509 eller TPM). X.509 attestering måste du överföra lägsta **certifikatet för primär** för enheten. TPM måste du ange den **attestering nyckeln** och **registrerings-ID** för enheten.  
       d. Klicka på **Spara**. På har skapats för din registrering grupp, bör du se enheten visas under den **enskilda registreringar** fliken.  

       [![Enskilda registrering i portalen](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Uppdatera en post för registrering
Du kan uppdatera en befintlig post för registrering på portalen med följande steg:

1. Öppna din Enhetsetableringen tjänst i Azure-portalen och klicka på **hantera registreringar**. 
2. Gå till registrering-posten som du vill ändra. Klicka på posten som öppnar en översiktlig information om registrering av enheter. 
3. På den här sidan kan du ändra objekt än säkerhetstypen och autentiseringsuppgifter, till exempel IoT-hubb enheten ska kopplas till, samt enhets-ID. Du kan också ändra inledande dubbla Enhetsstatus. 
4. När du slutfört, klickar du på **spara** att uppdatera din registrering av enheter. 

    ![Uppdatera registreringen i portalen](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Ta bort en registrering av enheter
I fall där dina enheter inte behöver etableras för IoT-hubb kan du ta bort posten relaterade registrering på portalen med följande steg:

1. Öppna din Enhetsetableringen tjänst i Azure-portalen och klicka på **hantera registreringar**. 
2. Navigera till och markera posten registrering som du vill ta bort. 
3. Klicka på den **ta bort** längst upp och välj sedan **Ja** när du uppmanas att bekräfta. 
5. När åtgärden har slutförts visas posten tas bort från listan över enhetsregistrering. 
 
    ![Ta bort registreringen i portalen](./media/how-to-manage-enrollments/remove-enrollment.png)


