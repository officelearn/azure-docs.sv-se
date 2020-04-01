---
title: Skapa en kontinuerlig patientövervakningsapp med Azure IoT Central | Microsoft-dokument
description: Lär dig att skapa ett kontinuerligt patientövervakningsprogram med hjälp av Azure IoT Central-programmallar.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021295"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Självstudiekurs: Distribuera och gå igenom en kontinuerlig appmall för patientövervakning



Den här självstudien visar hur du som lösningsbyggare kommer igång genom att distribuera en IoT Central kontinuerlig patientövervakningsprogrammall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kan göra härnäst.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en programmall
> * Gå igenom programmallen

## <a name="create-an-application-template"></a>Skapa en programmall

Navigera till [Azure IoT Central application manager webbplats](https://apps.azureiotcentral.com/). Välj **Skapa** i det vänstra navigeringsfältet och klicka sedan på fliken **Sjukvård.** 

>[!div class="mx-imgBorder"] 
>![App chef Healthcare](media/app-manager-health.png)

Klicka på knappen **Skapa app** om du vill börja skapa programmet och logga sedan in med ett personligt Microsoft-konto, ett arbets- eller skolkonto. Den tar dig till sidan **Nytt program.**

![Skapa applikation Healthcare](media/app-manager-health-create.png)

![Skapa faktureringsinformation för sjukvårdstjänst](media/app-manager-health-create-billinginfo.png)

Så här skapar du ditt program:

1. Azure IoT Central föreslår automatiskt ett programnamn baserat på den mall du har valt. Du kan acceptera det här namnet eller ange ditt eget programnamn, till exempel **Kontinuerlig patientövervakning**. Azure IoT Central genererar också ett unikt URL-prefix för dig baserat på programnamnet. Du kan ändra det här URL-prefixet till något mer minnesvärt om du vill.

2. Du kan välja om du vill skapa programmet med hjälp av den *kostnadsfria* prisplanen eller någon av standardprisplanerna. *standard* Program som du skapar med den kostnadsfria planen är gratis i sju dagar innan de löper ut och tillåter upp till fem kostnadsfria enheter. Du kan flytta ett program från den kostnadsfria planen till en standardprisplan när som helst innan den löper ut. Om du väljer det kostnadsfria abonnemanget måste du ange dina kontaktuppgifter och välja om du vill få information och tips från Microsoft. Program som du skapar med hjälp av en standardplan stöder upp till två kostnadsfria enheter och kräver att du anger din Azure-prenumerationsinformation för fakturering.

3. Välj **Skapa** längst ned på sidan för att distribuera programmet.

## <a name="walk-through-the-application-template"></a>Gå igenom programmallen

### <a name="dashboards"></a>Instrumentpaneler

När du har distribuerat appmallen landar du först på **Lamnas övervakningsinstrumentpanel för patient.** Lamna Healthcare är ett fiktivt sjukhussystem som innehåller två sjukhus: Woodgrove Hospital och Burkville Hospital. På den här operatörsinstrumentpanelen för Woodgrove Hospital ser du information och telemetri om enheterna i den här mallen tillsammans med en uppsättning kommandon, jobb och åtgärder som du kan vidta. Från instrumentpanelen kan du:

* Se enhetstelemetri och egenskaper som **enhetens batterinivå** eller **dess anslutningsstatus.**

* Visa **planlösningen** och placeringen av Smart Vitals Patch-enheten.

* **Återetablera** Smart Vitals Patch för en ny patient.

* Se ett exempel på en **leverantörsinstrumentpanel** som ett sjukhusvårdsteam kan se för att spåra sina patienter.

* Ändra **enhetens patientstatus** för att ange om enheten används för ett patient- eller fjärrscenario.

>[!div class="mx-imgBorder"] 
>![Lamna patient](media/lamna-in-patient.png)

Du kan också klicka på **Gå till fjärrpatient instrumentpanelen** för att se den andra operatören instrumentpanelen som används för Burkville Hospital. Den här instrumentpanelen innehåller en liknande uppsättning åtgärder, telemetri och information. Dessutom kan du se flera enheter som används och har möjlighet att **uppdatera firmware** på varje.

>[!div class="mx-imgBorder"] 
>![Lamna fjärrkontroll](media/lamna-remote.png)

På båda instrumentpanelerna kan du alltid länka tillbaka till den här dokumentationen.

### <a name="device-templates"></a>Enhetsmallar

Om du klickar på fliken **Enhetsmallar** ser du att det finns två olika enhetstyper som ingår i mallen:

* **Smart Vitals Patch**: Den här enheten representerar en patch som mäter olika typer av vitala tecken. Det kan användas för att övervaka patienter i och utanför sjukhuset. Om du klickar på mallen ser du att förutom att skicka enhetsdata som batterinivå och enhetstemperatur skickar plåstret också patienthälsodata som andningsfrekvens och blodtryck.

* **Smart Knä Stag:** Denna enhet representerar ett knä stag som patienter kan använda när återhämta sig från en knäledsplastik. Om du klickar på den här mallen ser du funktioner som rörelseomfång och acceleration, förutom enhetsdata.

>[!div class="mx-imgBorder"] 
>![Mall för smart vitala korrigeringsenheter](media/smart-vitals-device-template.png)

Om du klickar på fliken **Enhetsgrupper** ser du också att dessa enhetsmallar automatiskt har enhetsgrupper skapade för dem.

### <a name="rules"></a>Regler

När du hoppar till fliken Regler visas tre regler som finns i programmallen:

* **Stag temperatur hög:** Denna regel utlöses när enheten temperaturen på Smart&deg;Knee Brace är större än 95 F under en 5-minuters fönster. Du kan använda den här regeln för att varna patienten och vårdteamet och kyla ner enheten på distans.

* **Fall detekteras**: Denna regel utlöses om en patient faller upptäcks. Du kan använda den här regeln för att konfigurera en åtgärd för att distribuera ett operativt team för att hjälpa patienten som har fallit.

* **Patch batteri låg:** Denna regel utlöses när batterinivån på enheten går under 10%. Du kan använda den här regeln för att utlösa ett meddelande till patienten att ladda deras enhet.

>[!div class="mx-imgBorder"] 
>![Hög regel för stagtemperatur](media/brace-temp-rule.png)

### <a name="devices"></a>Enheter

Klicka på fliken **Enheter** och välj sedan en förekomst av **smart knästöd**. Du kommer att se att det finns tre vyer för att utforska information om den enhet som du har valt. Dessa vyer skapas och publiceras när du skapar enhetsmallen för enheten, vilket innebär att de är konsekventa på alla enheter som du ansluter eller simulerar.

**Instrumentpanelsvyn** ger en översikt över telemetri och egenskaper som kommer från enheten som är operatörsorienterad.

På fliken **Egenskaper** kan du redigera molnegenskaper och läsa/skriva enhetsegenskaper.

På fliken **Kommandon** kan du köra kommandon som har modellerats som en del av enhetsmallen.

>[!div class="mx-imgBorder"] 
>![Knästödsvyer](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort programmet genom att besöka **Administration > Programinställningar och** klicka på Ta **bort**.

>[!div class="mx-imgBorder"] 
>![Ta bort app](media/admin-delete.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar en instrumentpanel för leverantörer som ansluter till ditt IoT Central-program.

> [!div class="nextstepaction"]
> [Skapa en instrumentpanel för en leverantör](howto-health-data-triage.md)