---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e3110e4018e214e7e7aa591b811246369c029ecd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38945954"
---
Använda Azure-portalen för att skapa en IoT-hubb:

1. Logga in på [Azure-portalen](http://portal.azure.com).

1. Välj **Skapa en resurs** > **Sakernas internet** > **IoT Hub**.

    ![Välj för att installera IoT Hub](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. När du skapar din IoT-hubb på fri nivå använder du värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | Skapa ny. I den här självstudiekursen används namnet **tutorial-iot-hub-rg**. |
    | Region | I den här självstudien används **USA, västra**. Du väljer normalt den region som är närmast dig. |
    | Namn | Följande skärmbild använder namnet **tutorials-iot-hub**. Du måste välja ett eget unikt namn när du skapar din hubb. |

    ![Inställningar för hubb 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Inställning | Värde |
    | ------- | ----- |
    | Pris- och skalnivå | F1 Kostnadsfri. Du kan bara ha en hubb på den kostnadsfria nivån för en prenumeration. |
    | IoT Hub-enheter | 1 |

    ![Inställningar för hubb 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Klicka på **Skapa**. Det kan ta några minuter för hubben att skapas.

    ![Inställningar för hubb 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Anteckna IoT-hubbnamnet som du har valt. Du använder det här värdet senare i kursen.