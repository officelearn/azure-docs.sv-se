---
title: Hantera enhets registreringar för Azure IoT Hub Device Provisioning Service i Azure Portal
description: Hantera enhets registreringar för enhets etablerings tjänsten (DPS) i Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2fbcacd7f2094f9b0b9dcea3fea4d804fd96923e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165323"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Hantera enhets registreringar med Azure Portal

En *enhets registrering* skapar en post för en enskild enhet eller en grupp av enheter som kan komma att registreras med Azure-IoT Hub Device Provisioning service. Registrerings posten innehåller den initiala önskade konfigurationen för enheten eller enheterna som en del av registreringen, inklusive önskad IoT-hubb. Den här artikeln visar hur du hanterar enhets registreringar för etablerings tjänsten.


## <a name="create-a-device-enrollment"></a>Skapa en enhets registrering

Det finns två sätt att registrera enheter med etablerings tjänsten:

* En **registrerings grupp** är en post för en grupp av enheter som delar en gemensam mekanism för attestering av X. 509-certifikat, signerade av samma signerings certifikat, som kan vara [rot certifikatet](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) eller det [mellanliggande certifikatet](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)som används för att producera enhets certifikatet på den fysiska enheten. Vi rekommenderar att du använder en registrerings grupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som alla kommer till samma klient. 

    Du kan skapa en registrerings grupp i portalen för en grupp av enheter med hjälp av följande steg:

  1. Logga in på Azure Portal och klicka på **alla resurser** på den vänstra menyn.  
  1. Klicka på den enhets etablerings tjänst som du vill registrera enheten på från listan över resurser.  
  1. I etablerings tjänsten:  
     a. Klicka på **Hantera registreringar**och välj sedan fliken **registrerings grupper** .  
     b. Klicka på knappen **Lägg till** längst upp.  
     c. När panelen "Lägg till registrerings grupp" visas anger du informationen för registrerings List posten.  **Grupp namn** måste anges. Välj också "CA eller mellanliggande" för **certifikat typ**och överför **primärt rot certifikat** för enhets gruppen.  
     d. Klicka på **Spara**. När du har skapat din registrerings grupp bör du se att grupp namnet visas under fliken **registrerings grupper** .  

     [![Registrerings grupp i portalen](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* En **enskild registrering** är en post för en enskild enhet som kan registreras. Enskilda registreringar kan använda antingen x509-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som mekanism för attestering. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika inledande konfigurationer eller för enheter som bara kan använda SAS-token via TPM eller virtuell TPM som mekanism för attestering. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

    Du kan skapa en enskild registrering i portalen med hjälp av följande steg:

    1. Logga in på Azure Portal och klicka på **alla resurser** på den vänstra menyn.
    1. Klicka på den enhets etablerings tjänst som du vill registrera enheten på från listan över resurser.
    1. I etablerings tjänsten:  
       a. Klicka på **Hantera registreringar**och välj sedan fliken **enskilda registreringar** .  
       b. Klicka på knappen **Lägg till** längst upp.   
       c. När panelen "Lägg till registrering" visas anger du informationen för registrerings List posten. Välj först **mekanismen** för attestering för enheten (X. 509 eller TPM). X. 509-attestering kräver att du överför det **primära löv certifikatet** för enheten. TPM kräver att du anger **attesterings nyckel** och **registrerings-ID** för enheten.  
       d. Klicka på **Spara**. När du har skapat din registrerings grupp bör du se att enheten visas under fliken **enskilda registreringar** .  

       [![Individuell registrering i portalen](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Uppdatera en registrerings post
Du kan uppdatera en befintlig registrerings post i portalen med hjälp av följande steg:

1. Öppna enhets etablerings tjänsten i Azure Portal och klicka på **Hantera registreringar**. 
1. Navigera till den registrerings post som du vill ändra. Klicka på posten, som öppnar en sammanfattnings information om din enhets registrering. 
1. På den här sidan kan du ändra andra objekt än säkerhets typ och autentiseringsuppgifter, till exempel den IoT-hubb som enheten ska länkas till, samt enhets-ID. Du kan också ändra den inledande enhetens dubbla tillstånd. 
1. När du är klar klickar du på **Spara** för att uppdatera enhets registreringen. 

    ![Uppdatera registreringen i portalen](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Ta bort en enhets registrering
I de fall där enhet (er) inte behöver tillhandahållas till någon IoT-hubb kan du ta bort posten för den relaterade registreringen i portalen med hjälp av följande steg:

1. Öppna enhets etablerings tjänsten i Azure Portal och klicka på **Hantera registreringar**. 
1. Navigera till och välj den registrerings post som du vill ta bort. 
1. Klicka på knappen **ta bort** högst upp och välj sedan **Ja** när du uppmanas att bekräfta. 
1. När åtgärden har slutförts visas din post i listan med enhets registreringar. 
 
    ![Ta bort registreringen i portalen](./media/how-to-manage-enrollments/remove-enrollment.png)


