---
title: Hantering av enheter i fjärranslutna övervakningslösning - Azure | Microsoft Docs
description: Den här kursen visar hur du hanterar enheter som är anslutna till den fjärranslutna övervakningslösning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 0f177c3a8746f801e52cdac6cb2189e9cc28e1e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627287"
---
# <a name="manage-and-configure-your-devices"></a>Hantera och konfigurera dina enheter

Den här kursen visar enheten hanteringsfunktioner för lösning för övervakning av fjärråtkomst. I självstudiekursen används ett scenario för att införa dessa funktioner i Contoso IoT-programmet.

Contoso har beslutat nya maskiner att expandera en av sina resurser för att öka utdata. Medan du väntar nya maskiner som ska levereras som du vill köra en simulering om du vill kontrollera hur din lösning. Som en operator som du vill hantera och konfigurera enheterna i Fjärrövervaknings-lösning.

För att tillhandahålla en utökningsbar sätt att hantera och konfigurera enheter Fjärrövervaknings lösningen använder funktioner för IoT-hubb som [jobb](../iot-hub/iot-hub-devguide-jobs.md) och [direkt metoder](../iot-hub/iot-hub-devguide-direct-methods.md). Information om hur enheten utvecklare implementerar metoderna på en fysisk enhet finns [anpassa Fjärrövervaknings solution accelerator](iot-accelerators-remote-monitoring-customize.md).

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Etablera en simulerad enhet.
> * Testa den simulerade enheten.
> * Anropa enheten från lösningen.
> * Konfigurera om en enhet.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här självstudiekursen, måste en distribuerad instans av Fjärrövervaknings-lösning i din Azure-prenumeration.

Om du inte har distribuerat Fjärrövervaknings lösningen ännu, bör du genomföra den [distribuera Fjärrövervaknings solution accelerator](iot-accelerators-remote-monitoring-deploy.md) kursen.

## <a name="add-a-simulated-device"></a>Lägg till en simulerad enhet

Navigera till den **enheter** i lösningen och väljer sedan **+ ny enhet**. I den **ny enhet** panelen, väljer **simulerad**:

![Etablera en simulerad enhet](./media/iot-accelerators-remote-monitoring-manage/devicesprovision.png)

Lämna antalet enheter för att etablera inställd på **1**. Välj den **felaktig motorn** enheten modell och välj sedan **tillämpa** att skapa den simulerade enheten:

![Etablera en simulerad motorn-enhet](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine.png)

Mer information om hur du etablerar en *fysiska* enhet, finns [ansluta enheten till Fjärrövervaknings solution accelerator](iot-accelerators-connecting-devices-node.md).

## <a name="test-the-simulated-device"></a>Testa den simulerade enheten

Om du vill visa information om den nya simulerade enheten markerar du den i listan över enheter på den **enheter** sidan. Information om enheten visas i den **enheten detalj** panelen:

![Visa den nya simulerade motorn](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew.png)

I **enheten detalj**, kontrollera att den nya enheten skickar telemetri. Om du vill visa annan telemetri strömmar från enheten, Välj ett namn på telemetri som **vibration**:

![Välj en telemetri ström för att visa](./media/iot-accelerators-remote-monitoring-manage/devicesvibration.png)

Den **enheten detalj** panelen visas annan information om enhet, till exempel taggvärden, de metoder som stöds och egenskaper som rapporteras av enheten.

Om du vill visa detaljerad diagnostik rulla visa **diagnostik**.

## <a name="act-on-a-device"></a>Fungerar på en enhet

För att fungera på en eller flera enheter, markerar du dem i listan över enheter och välj sedan **jobb**. Den **motorn** enhetsmodell anger tre metoder måste ha stöd för en enhet:

![Motorn metoder](./media/iot-accelerators-remote-monitoring-manage/devicesmethods.png)

Välj **FillTank**, ange Jobbnamnet på **FillEngineTank**, och välj sedan **Verkställ**:

![Schemalägga restart-metoden](./media/iot-accelerators-remote-monitoring-manage/devicesrestartengine.png)

Spåra status för jobbet på den **Underhåll** väljer **jobb**:

![Övervaka jobbet scheman](./media/iot-accelerators-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>Metoderna i andra enheter

När du utforska de olika enhetstyper simulerade ser du att andra typer av enheter stöder olika metoder. I en distribution med fysiska enheter anger enhetsmodellen metoderna ska ha stöd för enheten. Normalt ansvarar enhet utvecklaren för att utveckla koden som gör att enheten fungerar som svar på ett metodanrop.

Om du vill schemalägga en metod för att köras på flera enheter, kan du välja flera enheter i listan på den **enheter** sidan. Den **jobb** panelen visas typerna av metoden som är gemensamma för alla valda enheter.

## <a name="reconfigure-a-device"></a>Konfigurera om en enhet

Om du vill ändra konfigurationen av en enhet väljer du den i listan över enheter på den **enheter** sidan och väljer sedan **jobb**, och välj sedan **omkonfigurera**. Panelen jobb visar egenskapsvärdena för den valda enheten som du kan ändra:

![Konfigurera om en enhet](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure.png)

Lägga till ett namn för jobbet att ändra, uppdatera egenskapsvärden och välj **Verkställ**:

![Uppdatera ett egenskapsvärde för enhet](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical.png)

Spåra status för jobbet på den **Underhåll** väljer **jobb**.

## <a name="next-steps"></a>Nästa steg

Den här självstudiekursen visades hur du vill:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Etablera en simulerad enhet.
> * Testa den simulerade enheten.
> * Anropa enheten från lösningen.
> * Konfigurera om en enhet.

Nu när du har lärt dig hur du hanterar dina enheter, föreslagna nästa steg är att lära dig hur du:

* [Felsök och åtgärda enhetsproblem](iot-accelerators-remote-monitoring-maintain.md).
* [Testa din lösning med simulerade enheter](iot-accelerators-remote-monitoring-test.md).
* [Ansluta enheten till Fjärrövervaknings solution accelerator](iot-accelerators-connecting-devices-node.md).

<!-- Next tutorials in the sequence -->