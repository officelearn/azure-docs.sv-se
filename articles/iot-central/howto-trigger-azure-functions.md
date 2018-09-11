---
title: Utlösa Azure Functions med webhookar i Azure IoT Central
description: Skapa en funktionsapp som körs varje gång en regel utlöses i Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/06/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b14dc4eeec1ab543c407b1bb1cf8a5ce46f3ecb
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357420"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Utlösa Azure Functions med webhookar i Azure IoT Central

Använd Azure Functions för att köra serverlös kod för webhook-utdata från IoT Central regler. Du behöver inte etablera en virtuell dator eller publicera en webbapp för att använda Azure Functions, men i stället kan du köra den här koden serverlessly. Använd Azure Functions för att omvandla webhook-nyttolasten innan de skickas till slutmålet, till exempel en SQL-databas eller en Event Grid. 

## <a name="prerequisites"></a>Förutsättningar
+ Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="how-to-connect-azure-functions"></a>Så här ansluter du Azure Functions

1. [Skapa en ny funktionsapp i Azure Portal. ](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).
2. Expandera funktionsappen och klicka på + knappen bredvid funktioner. Om den här funktionen är den första funktionen i din funktionsapp väljer du anpassad funktion. Detta visar en fullständig uppsättning med funktionsmallar.
3. Ange allmän i sökfältet och välj sedan önskat språk för utlösarmallen för allmän webhook. I det här avsnittet används en C#-funktion. 
4. Klicka på **</> Hämta funktionswebbadress** och kopiera och spara värdet. Du kan använda det här värdet för att konfigurera webhooken. 
4. IoT Central, hitta den regel som du vill ansluta till din funktionsapp. 
5. Lägg till en webhook-åtgärd. Ange en **visningsnamn** och klistra in funktionens URL som du kopierade tidigare.
6. Spara regeln. Nu när regeln utlöses, anropar webhooken funktionsappen ska köras. Du kan se loggar och se varje gång som funktionen har utlösts i din funktionsapp.

Mer information finns i Azure Functions-artikeln om [skapar en funktion som utlöses av en allmän webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du ställer in och använda webhooks, föreslagna nästa steg är att utforska [att skapa arbetsflöden i Microsoft Flow](howto-add-microsoft-flow.md).
