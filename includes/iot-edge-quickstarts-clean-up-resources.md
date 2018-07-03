---
title: ta med fil
description: ta med fil
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: c0b9f9e9808de90df84edf2d3c409a921629baee
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055055"
---
Om du ska fortsätta till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du redan har skapat och återanvända dem.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som skapats i den här artikeln om du vill undvika kostnader. 

> [!IMPORTANT]
> Att ta bort Azure-resurser och en resursgrupp kan inte ångras. När resursgruppen och alla resurser som ingår i den tas bort är de borta permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Om du endast vill ta bort IoT Hub kan du köra följande kommando efter att ha ersatt `<YourIoTHub>` med ditt hubbnamn och `<TestResources>` med namnet på din resursgrupp:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Ta bort hela resursgruppen med namnet:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

2. I textrutan **Filtrera efter namn ...** , skriver du namnet på resursgruppen som innehåller din IoT Hub. 

3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

    ![Ta bort](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.






