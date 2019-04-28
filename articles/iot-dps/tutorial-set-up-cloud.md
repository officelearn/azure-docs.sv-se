---
title: Konfigurera moln för Azure IoT Hub Device Provisioning-tjänst i portalen | Microsoft Docs
description: Automatisk enhetsetablering i IoT Hub i Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f06d3f033a2bf5907dc2ee324359bef0eb247d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953538"
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Konfigurera molnresurser för enhetsetablering med IoT Hub Device Provisioning-tjänst

Den här självstudien visar hur du konfigurerar molnet för en automatisk enhetsetablering med IoT Hub Device Provisioning-tjänsten. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT Hub Device Provisioning-tjänst och hämta ID-omfånget
> * Skapa en IoT Hub
> * Länka IoT-hubben till Device Provisioning-tjänsten
> * Ange allokeringsprincip för enhetsetableringstjänsten

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

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

   ![Ange grundläggande information om enhetsetableringstjänsten på portalen](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Klicka på **Skapa**. Efter en liten stund skapas instansen för enhetsetableringstjänsten, och sidan **Översikt** visas.

6. På sidan **Översikt** för den nya tjänstinstansen kopierar du värdet för **ID-omfånget** för senare användning. Det värdet används för att identifiera registrerings-ID och erbjuder en garanti för att registrerings-ID:t är unikt.

7. Kopiera även **Tjänstslutpunkten** värde för senare användning. 

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Länka Device Provisioning-tjänsten till en IoT-hubb

Nästa steg är att länka Device Provisioning-tjänsten och IoT Hub så att IoT Hub Device Provisioning-tjänsten kan registrera enheter till den hubben. Tjänsten kan endast etablera enheter till IoT-hubbar som har länkats till Device Provisioning-tjänsten. Följ de här stegen.

1. På sidan **Alla resurser** klickar du på Device Provisioning-tjänsteinstansen du skapade tidigare.

2. Välj **Linked IoT hubs** (Länkade IoT-hubbar) på Device Provisioning-tjänstens sida.

3. Klicka på **Lägg till**.

4. På sidan **Lägg till länk i IoT Hub** anger du följande information och klickar på **Spara**:

    * **Prenumeration:** Kontrollera att den prenumeration som innehåller IoT-hubben är markerad. Du kan länka till en IoT-hubb som finns i en annan prenumeration.

    * **IoT-hubb:** Välj namnet på IoT-hubben som du vill länka med den här instansen för Device Provisioning-tjänsten.

    * **Åtkomstprincip:** Välj **iothubowner** som autentiseringsuppgifterna som används för att upprätta en länk till IoT hub.

   ![Länka hubbnamnet för att länka till enhetsetableringstjänsten på portalen](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Ange allokeringsprincip för enhetsetableringstjänsten

Allokeringsprincipen är en IoT Hub Device Provisioning Service-inställning som avgör hur enheter tilldelas till en IoT-hubb. Det finns tre allokeringsprinciper som stöds: 

1. **Kortast svarstid**: Enheter etableras till en IoT-hubb baserat på hubben med kortast svarstid till enheten.

2. **Jämnt viktad distribution** (standard): Länkade IoT-hubbar är lika sannolikt att enheter etablerade till sig. Den här inställningen är standardinställningen. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen. 

3. **Statisk konfiguration via registreringslistan**: Specifikation av önskad IoT-hubben på registreringslistan har prioritet framför allokeringsprincip Device Provisioning-tjänstnivå.

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
