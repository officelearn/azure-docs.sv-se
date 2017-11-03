---
title: "Konfigurera molnet för Azure IoT Hub-enhet etablering i portal | Microsoft Docs"
description: IoT-hubb automatisk enhetsetableringen i Azure Portal
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
ms.openlocfilehash: 088d127521ce89d3a82e30ad8797fe5746ae7e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Konfigurera molnresurser för enhetsetableringen med IoT-hubb Device etablering Service

Den här kursen visar hur du konfigurerar molnet för automatisk enhet etablering med hjälp av IoT-hubb enheten Etableringstjänsten. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT-hubb enheten etablering Service och få ID-scope
> * Skapa en IoT Hub
> * Länka IoT-hubben till enheten Etableringstjänsten
> * Ange princip för resursallokering för tjänsten etablering enhet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Skapa en enhet etablering tjänstinstans och hämta ID-scope

Följ dessa steg om du vill skapa en ny enhet etablering Service-instans.

1. I det övre vänstra hörnet i Azure-portalen klickar du på **ny**.
2. I rutan Sök skriver **enhetsetableringen**. 
3. Klicka på **IoT Hub-enhet som etablerar tjänsten**.
4. Fyll i den **IoT Hub-enhet Etableringstjänsten** formuläret med följande information:
    
   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Namn** | Ett unikt namn | -- | 
   | **Prenumeration** | Din prenumeration  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |   

   ![Ange grundläggande information om din DP i portalen](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Klicka på **Skapa**.
6. Den *ID scope* används för att identifiera registrering ID: N och ger en garanti för att ett registrerings-ID är unikt. För att få det här värdet kan du klicka på **översikt** att öppna den **Essentials** för etablering av tjänst. Kopiera den **ID Scope** värdet till en tillfällig plats för senare användning.
7. Även anteckna den **tjänstslutpunkten** värdet eller kopiera den till en tillfällig plats för senare användning. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Länka enheten Etableringstjänsten till en IoT-hubb

Nästa steg är att länka tjänst för etablering och IoT-hubb så att Etableringstjänsten IoT-hubb enhet kan registrera enheter till att hubben. Tjänsten kan endast etablera enheter till IoT-hubbar som har länkats till enheten Etableringstjänsten. Följ dessa steg.

1. I den **alla resurser** klickar du på enheten etablering tjänstinstansen du skapade tidigare.
2. På den enheten Etableringstjänsten klickar du på **länkade IoT-hubbar**.
3. Klicka på **Lägg till**.
4. I den **Lägg till länk till IoT-hubb** använder alternativknapparna för att ange om länkade IoT-hubben är placerad i den aktuella prenumerationen eller i en annan prenumeration. Välj namnet på IoT-hubben från den **IoT-hubb** rutan.
5. Klicka på **Spara**.

   ![Länka hubbnamnet att länka till DP i portalen](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Ange princip för resursallokering för tjänsten etablering enhet

Princip för resursallokering är en IoT-hubb enheten etablering tjänstinställning som avgör hur enheter har tilldelats en IoT-hubb. Det finns tre resursallokeringsprinciper som stöds: 

1. **Lägsta fördröjningen**: enheter har etablerats till en IoT-hubb som baseras på hubben med den lägsta fördröjningen till enheten.
2. **Jämnt viktas distribution** (standard): länkade IoT-hubbar sannolikt lika har enheter som har etablerats till dem. Det här är standardinställningen. Om du etablerar enheter till endast en IoT-hubb kan du behålla den här inställningen. 
3. **Statisk konfiguration via listan registrering**: specificering av önskade IoT-hubben i listan över registrering har högre prioritet än Enhetsetableringen servicenivåer allokering av principen.

Ange princip för resursallokering i enheten Etableringstjänsten sidan klickar du på **Hantera princip**. Kontrollera att principen anges till **jämnt viktas distribution** (standard). Om du gör några ändringar klickar du på **spara** när du är klar.

![Hantera principer för tilldelning](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Rensa resurser

Andra kurser i den här samlingen bygger på den här kursen. Om du planerar att fortsätta på att arbeta med efterföljande snabbstarter eller självstudierna inte rensa upp de resurser som skapades i den här självstudiekursen. Om du inte planerar att fortsätta, Använd följande steg för att ta bort alla resurser som skapats av den här kursen i Azure-portalen.

1. I den vänstra menyn i Azure-portalen klickar du på **alla resurser** och välj sedan din IoT-hubb Device etablering Service-instans. Längst upp i den **alla resurser** klickar du på **ta bort**.  
2. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Längst upp i den **alla resurser** klickar du på **ta bort**.
 
## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT-hubb enheten etablering Service och få ID-scope
> * Skapa en IoT Hub
> * Länka IoT-hubben till enheten Etableringstjänsten
> * Ange princip för resursallokering för tjänsten etablering enhet

Gå vidare till nästa kurs att lära dig hur du konfigurerar din enhet för etablering.

> [!div class="nextstepaction"]
> [Konfigurera enhet för etablering](tutorial-set-up-device.md)
