---
title: inkludera fil (enhetsströmmar)
description: ta med fil
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46ab75f161692dd048c19698af7027d0e0622b37
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67445977"
---
Om du planerar att fortsätta till nästa rekommenderade artikel kan du behålla och återanvända de resurser som du redan har skapat.

Annars kan du ta bort Azure-resurser som du skapade i den här artikeln för att undvika avgifter.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat IoT-hubben i en befintlig resursgrupp som innehåller resurser som du vill behålla tar du bara bort själva IoT-hubben, inte resursgruppen.
>

Ta bort en resursgrupp med namnet:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

1. Ange namnet på resursgruppen som innehåller IoT-hubben i rutan **Filter efter namn.**

1. I resultatlistan väljer du ellipsen (**...**) till höger om resursgruppen och väljer sedan **Ta bort resursgrupp**.

    ![Knappen "Ta bort resursgrupp"](./media/iot-hub-quickstarts-clean-up-resources-device-streams/iot-hub-delete-resource-group.png)

1. Om du vill bekräfta borttagningen av resursgruppen går du in i resursgruppens namn igen och väljer sedan **Ta bort**. Efter en stund tas resursgruppen och alla dess inneslutna resurser bort.
