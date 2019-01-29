---
title: inkludera fil (förhandsversion av enhetsströmmar)
description: inkludera fil (förhandsversion av enhetsströmmar)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 53d8df7e2366cfbf2f62e79fc99c8ef2f9b48ba1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830818"
---
I det här avsnittet beskrivs hur du använder [Azure-portalen](https://portal.azure.com) för att skapa en IoT-hubb.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj +**Skapa en resurs** och välj sedan **Sakernas Internet**.

3. Klicka på **Iot Hub** i listan till höger. Du ser den första skärmen för att skapa en IoT-hubb.

   ![Skärmbild som visar hur du skapar en hubb i Azure-portalen](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   Fyll i fälten:

   **Prenumeration**: Välj den prenumeration som du vill använda för din IoT-hubb.

   **Resursgrupp**: Du kan skapa en ny resursgrupp eller välja en befintlig. Klicka på **Skapa ny** för att skapa en ny och ange namnet som du vill använda. Om du vill använda en befintlig resursgrupp väljer du **Använd befintlig** och väljer en resursgruppen i listrutan. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](../articles/azure-resource-manager/resource-group-portal.md).

   **Region**: Det här är den region som du vill att din hubb ska finnas i. Se till att du väljer en region som stöds (t.ex. USA, centrala eller USA, centrala EUAP).

   **Namn på IoT-hubb**: Ange ett namn för IoT-hubben. Det här namnet måste vara globalt unikt. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Klicka på **Nästa: Storlek och skalning** för att fortsätta att skapa IoT-hubben.

   ![Skärmbild som visar inställningsstorlek och skala för en ny IoT-hubb med Azure-portalen](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)


   På den här skärmen kan du ta standardvärdena och klicka bara på **Granska + skapa** längst ned på sidan. 

   **Pris- och skalningsnivå**: Se till att du väljer någon av standardnivåerna (S1, S2, S3) eller den kostnadsfria nivån (F1). Det här valet kan även bero på storleken på din flotta och de icke-strömmade arbetsbelastningar som du förväntar dig från din hubb (t.ex. telemetrimeddelanden). Till exempel är den kostnadsfria nivån avsedd för testning och utvärdering. Det gör att 500 enheter kan anslutas till IoT-hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnadsfria nivån. 

   **IoT Hub-enheter**: Det här valet beror på icke-strömmade arbetsbelastningar som du förväntar dig från din hubb – du kan välja 1 för tillfället.

   Mer information om de andra alternativen för nivån finns i avsnittet om att [välja rätt nivå för IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

5. Klicka på **Granska + skapa** och granska dina val. Du ser något som liknar den här skärmen.

   ![Skärmbild – Granska informationen för att skapa den nya IoT-hubben](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. Klicka på **Skapa** för att skapa din nya IoT-hubb. Det tar några minuter att skapa hubben.
