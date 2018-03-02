---
title: "Konfigurera moln för Azure IoT Hub Device Provisioning-tjänst i portalen | Microsoft Docs"
description: Automatisk enhetsetablering i IoT Hub i Azure Portal
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 247c2155943d651c3be7791571522b652cf63483
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Konfigurera molnresurser för enhetsetablering med IoT Hub Device Provisioning-tjänst

Den här självstudien visar hur du konfigurerar molnet för en automatisk enhetsetablering med IoT Hub Device Provisioning-tjänsten. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT Hub Device Provisioning-tjänst och hämta ID-omfånget
> * Skapa en IoT Hub
> * Länka IoT-hubben till Device Provisioning-tjänsten
> * Ange allokeringsprincip för enhetsetableringstjänsten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Skapa en Device Provisioning-tjänstinstans och hämta ID-omfång

Följ de här stegen för att skapa en ny Device Provisioning-tjänstinstans.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. I sökrutan skriver du **enhetsetablering**. 
3. Klicka på **IoT Hub Device Provisioning-tjänst**.
4. Fyll i följande information i formuläret **IoT Hub Device Provisioning-tjänst**:
    
   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Namn** | Ett unikt namn | -- | 
   | **Prenumeration** | Din prenumeration  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |   

   ![Ange grundläggande DPS-information i portalen](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Klicka på **Skapa**.
6. *ID-omfånget* används för att identifiera registrerings-ID och erbjuder en garanti för att registrerings-ID:t är unikt. När du vill hämta värdet klickar du på **Översikt** för att öppna sidan **Essentials** för Device Provisioning-tjänsten. Kopiera **ID-omfångets** värde till en tillfällig plats för användning senare.
7. Anteckna också värdet för **tjänstens slutpunkt** eller kopiera det till en tillfällig plats för användning senare. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Länka Device Provisioning-tjänsten till en IoT-hubb

Nästa steg är att länka Device Provisioning-tjänsten och IoT Hub så att IoT Hub Device Provisioning-tjänsten kan registrera enheter till den hubben. Tjänsten kan endast etablera enheter till IoT-hubbar som har länkats till Device Provisioning-tjänsten. Följ de här stegen.

1. På sidan **Alla resurser** klickar du på Device Provisioning-tjänsteinstansen du skapade tidigare.
2. Välj **Linked IoT hubs** (Länkade IoT-hubbar) på Device Provisioning-tjänstens sida.
3. Klicka på **Lägg till**.
4. På sidan **Lägg till länk i IoT Hub** använder du alternativknapparna för att ange om den länkade IoT-hubben är placerad i den aktuella prenumerationen eller i en annan prenumeration. Välj sedan IoT-hubbens namn från rutan **IoT Hub**.
5. Klicka på **Spara**.

   ![Länka hubbnamnet för att länka till DPS på portalen](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Ange allokeringsprincip för enhetsetableringstjänsten

Allokeringsprincipen är en inställning för IoT Hub Device Provisioning-tjänsten som bestämmer hur enheter tilldelas till en IoT-hubb. Det finns tre allokeringsprinciper som stöds: 

1. **Kortast svarstid**: Enheter etableras till en IoT-hubb baserat på hubben med kortast svarstid till enheten.
2. **Jämnt viktad distribution** (standard): Det är lika sannolikt att länkade IoT-hubbar får enheter etablerade till sig. Det här är standardinställningen. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen. 
3. **Statisk konfiguration via registreringslistan**: Specificering av den önskade IoT-hubben på registreringslistan har högre prioritet än allokeringsprincipen på Device Provisioning-tjänstnivå.

Om du vill ställa in allokeringsprincipen går du till Device Provisioning-tjänstsidan och klickar på **Hantera allokeringsprincip**. Kontrollera att allokeringsprincipen är inställd på **Jämnt viktad distribution** (standardinställningen). Om du gör några ändringar ska du klicka på **Spara** när du är klar.

![Hantera allokeringsprincip](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Rensa resurser

Andra självstudier i den här samlingen bygger på den här självstudien. Om du tänker fortsätta med efterföljande snabbstarter eller självstudier ska du inte rensa resurserna du har skapat i den här självstudien. Om du inte planerar att fortsätta följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudien på Azure-portalen.

1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT Hub Device Provisioning-tjänstinstans. Klicka på **Ta bort** överst på sidan **Alla resurser**.  
2. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Klicka på **Ta bort** överst på sidan **Alla resurser**.
 
## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT Hub Device Provisioning-tjänst och hämta ID-omfånget
> * Skapa en IoT Hub
> * Länka IoT-hubben till Device Provisioning-tjänsten
> * Ange allokeringsprincip för enhetsetableringstjänsten

Gå vidare till nästa självstudie för att lära dig att konfigurera din enhet för etablering.

> [!div class="nextstepaction"]
> [Konfigurera enhet för etablering](tutorial-set-up-device.md)
