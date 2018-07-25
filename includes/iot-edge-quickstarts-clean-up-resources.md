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
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008509"
---
Om du tänker fortsätta till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer du har skapat och använda dem igen.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser du har skapat i den här artikeln för att undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av Azure-resurser och resursgrupper. När dessa objekt tas bort tas resursgruppen och alla resurser som finns i den bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT-hubben i en befintlig resursgrupp som innehåller resurser som du vill behålla tar du bara bort själva IoT-hubbresursen i stället för att ta bort resursgruppen.
>

Om du endast vill ta bort IoT-hubben kör du följande kommando. Ersätt \<YourIoTHub> med namnet på din IoT-hubb och \<TestResources> med namnet på din resursgrupp:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Ta bort hela resursgruppen med namnet:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

2. I textrutan **Filtrera efter namn** anger du namnet på den resursgrupp som innehåller din IoT-hubb. 

3. Till höger om resursgruppen i resultatlistan väljer du ellipsen (**...**) och sedan **Ta bort resursgrupp**.

    ![Ta bort resursgrupp](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på resursgruppen igen för att bekräfta och välj sedan **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.






