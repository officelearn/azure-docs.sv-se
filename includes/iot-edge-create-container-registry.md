---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 04/25/2019
ms.author: kgremban
ms.openlocfilehash: 485a76cb91e5146f59e6be592ffd9cbba68e585a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576770"
---
## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du Verktyg för Azure IoT-tillägget och skapa en modul som du kan skapa en **behållaravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.

Du kan använda valfritt Docker-kompatibelt register för att lagra dina containeravbildningar. Två populära Docker-registertjänster är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry.

Om du inte redan har ett containerregister följer du dessa steg för att skapa ett nytt i Azure:

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

2. Skapa containerregistret genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Registernamn | Ange ett unikt namn. |
   | Prenumeration | Välj en prenumeration i listrutan. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Plats | Välj en plats i närheten av dig. |
   | Administratörsanvändare | Ändra värdet till **Aktivera**. |
   | SKU | Välj **Grundläggande**. |

5. Välj **Skapa**.

6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**.

7. Kopiera värdena för **inloggningsserver**, **användarnamn**, och **lösenord** och spara dem på en bekväm plats. Du kan använda dessa värden i den här självstudien för att ge åtkomst till behållarregistret.

   ![Kopiera inloggningsserver, användarnamn och lösenord för behållarregister](./media/iot-edge-create-container-registry/registry-access-key.png)