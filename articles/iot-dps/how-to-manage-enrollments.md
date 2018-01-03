---
title: Hantera enhetsregistrering med Azure-portalen | Microsoft Docs
description: "Så här hanterar du enhetsregistreringar för din DP-tjänst i Azure Portal"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b1d4e1e54d945c6edb0054da7b465b31de8c82a1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Så här hanterar du enhetsregistrering med Azure-portalen

En *enhetsregistrering* skapas en post för en enstaka enhet eller en grupp av enheter som någon gång registrera med Azure IoT-hubb Device etablering Service. Registreringspost innehåller inledande önskad konfiguration för enheter som en del av att registreringen, inklusive önskade IoT-hubben. Den här artikeln visar hur du hanterar enhetsregistreringar för etablering tjänsten.


## <a name="create-a-device-enrollment"></a>Skapa en registrering av enheter

Det finns två sätt som du kan registrera dina enheter med tjänsten etablering:

* En **registrering grupp** finns en post för en grupp av enheter som delar en gemensam attestering mekanismen för X.509-certifikat som signerats av samma signeringscertifikatet, vilket kan vara den [rotcertifikat](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#root-certificate) eller [mellanliggande certifikat](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#intermediate-certificate), som används för att skapa certifikat för enhet på den fysiska enheten. Vi rekommenderar en grupp för registrering för ett stort antal enheter som delar en önskad inledande konfiguration eller för enheter alla kommer att samma klientorganisation. Observera att du bara kan registrera enheter som använder mekanismen för attestering X.509 som *registrering grupper*. 

    Du kan skapa en grupp för registrering i portalen för en grupp av enheter med hjälp av följande steg:

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.
    2. Klicka på Enhetsetableringen tjänsten som du vill registrera din enhet till i listan över resurser.
    3. I din etablering tjänst klickar du på **hantera registreringar**och välj **registrering grupper** fliken.
    4. Klicka på den **Lägg till** längst upp och ange den information som krävs för registrering listpost. Överför rotcertifikatet för gruppen av enheter. 
    5. Klicka på **Spara**. På har skapats för din registrering grupp, bör du se gruppnamnet visas under den **registrering grupper** fliken. 

        ![Registrering grupp i portalen](./media/how-to-manage-enrollments/group-enrollment.png)

    
* En **enskilda registrering** finns en post för en enda enhet som kan registrera. Enskilda registreringar kan använda antingen x509 certifikat eller SAS token (i TPM verkliga eller virtuella) attestering funktioner. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som bara kan använda SAS-token via TPM eller virtuella TPM som mekanismen för attestering. Enskilda registreringar kanske önskade IoT-hubb enhets-ID angetts.

    Du kan skapa en enskild registrering på portalen med följande steg:

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.
    2. Klicka på Enhetsetableringen tjänsten som du vill registrera din enhet till i listan över resurser.
    3. I din etablering tjänst klickar du på **hantera registreringar**och välj **enskilda registreringar** fliken.
    4. Klicka på den **Lägg till** längst upp. 
    5. Välj säkerhetsmekanism för enheten och ange den information som krävs för registrering listpost. Överföra ett signerat certifikat om enheten implementerar X.509. 
    6. Klicka på **Spara**. På har skapats för din registrering grupp, bör du se enheten visas under den **enskilda registreringar** fliken. 

        ![Enskilda registrering i portalen](./media/how-to-manage-enrollments/individual-enrollment.png)


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



