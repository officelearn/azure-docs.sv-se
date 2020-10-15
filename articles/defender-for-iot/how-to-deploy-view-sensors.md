---
title: Visa och hantera inbyggda sensorer i Azure Defender för IoT
description: Den här artikeln beskriver hur du visar och tar bort inbyggda sensorer, samt hur du hämtar nya aktiverings filer för inbyggda sensorer i Azure Defender för IoT.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094527"
---
# <a name="view-and-manage-onboarded-sensors"></a>Visa och hantera inbyggda sensorer

Den här artikeln beskriver hur du visar och tar bort inbyggda sensorer, samt hur du hämtar nya aktiverings filer för onboarded-sensorer.

## <a name="update-sensor-management-mode"></a>Uppdatera sensor hanterings läge

Du kanske vill uppdatera det läge som sensorn hanterar. När du gör detta måste du ta bort den aktuella sensorn från sidan sensor hantering och ladda upp en ny aktiverings fil.

- **Arbeta i moln hanterings läge i stället för lokalt hanterat läge**: uppdatera aktiverings filen för din lokalt hanterade sensor med en aktiverings fil för en molnbaserad hanterad sensor. Efter återaktivering visas sensor identifieringar i både sensorn och Azure Defender för IoT-portalen. När återaktivering av filen har överförts skickas nyligen upptäckt aviserings information till Azure.

- **Arbeta i lokalt hanterat läge i stället för moln hanterings läge**: uppdatera aktiverings filen för en moln hanterad sensor med en aktiverings fil för en lokalt hanterad sensor. Efter återaktivering visas sensor identifierings information endast i sensorn.

- **Koppla sensorn till en ny IoT Hub**: du kanske vill associera sensorn med en ny IoT Hub. Det gör du genom att registrera igen och sedan överföra en ny aktiverings fil.

**Så här återaktiverar du sensorn:**

1. Gå till sidan Azure Defender för IoT, **sensor hantering** .

2. Välj den sensor som du vill ladda upp en ny aktiverings fil för.

3. Ta bort den.

4. Publicera sensorn igen från **onboarding** -sidan i det nya läget eller med en ny IoT Hub.

5. Hämta aktiverings filen från sidan **Hämta aktiverings fil** .

6. Logga in på Azure Defender för IoT sensor-konsolen.

7. I sensor konsolen väljer du **Systeminställningar** och väljer sedan **Återaktivering**.

   ![Skärm bild av vyn återaktivera](media/updates/image14.png)

8. Välj **överför** och välj den fil som du sparade.

9. Välj **Aktivera**.
 
## <a name="delete-sensors"></a>Ta bort sensorer

Om du tar bort en moln hanterad sensor skickas information inte till IoT Hub.

Ta bort lokalt hanterade sensorer när du inte längre arbetar med dem.

**Ta bort sensorer:**

1. Gå till sidan för hantering av Azure Defender för IoT- **sensorn** .

2. Välj de sensorer som du vill ta bort.

3. Välj **ta bort sensor**.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konfigurations alternativ fortsätter du till instruktions guiden för konfiguration av modulen.
> [!div class="nextstepaction"]
> [Guide för konfiguration av moduler](./how-to-agent-configuration.md)
