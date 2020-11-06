---
title: 'Snabb start: onboard-sensorer i Azure Defender för IoT'
description: Lär dig att publicera sensorer för Azure Defender för IoT-distribution.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: rkarlin
ms.openlocfilehash: 28d64f5d9d845c2a14f0277799213501e1d68010
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421772"
---
# <a name="quickstart-deploy-and-onboard-a-sensor"></a>Snabb start: Distribuera och publicera en sensor

Den här artikeln innehåller en översikt över sensor distributions processen. Sensorer måste registreras på Azure Defender för IoT-portalen.

Den här processen kräver att du köper en förkonfigurerad sensor eller skaffar en certifierad sensor och installerar själva sensor programmet.

Om du arbetar med en certifierad sensor installation rekommenderar vi att du läser [guiden för maskin varu specifikationer för Azure Defender för IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) innan du börjar.


> [!NOTE]
> Den lokala hanterings konsolen för Azure Defender för IoT behöver inte registreras.


Med onboarding-sensorer kan du:

|||
|------ | ----------- |
| **Definiera ett sensor namn** | Namnge sensorn som du registrerar och koppla den till en IoT Hub eller prenumeration.<br /><br />Mer information finns i **onboard-sensorer** .|
|**Välj en prenumeration och antalet allokerade enheter**|Välj en prenumeration och antalet enheter som omfattas av prenumerationen. Ange talet i steg om 1000.|
| **Definiera ett sensor hanterings läge** | Ange var till gång, avisering och annan information som identifieras av sensorn som visas. Detta bestäms utifrån det **sensor hanterings läge** som du definierar.<br /><br />**Lokalt hanterat läge** : information som identifieras av sensorn visas i sensor konsolen. Identifierings information delas också med den lokala hanterings konsolen om sensorn är ansluten till den.<br /><br />**Moln hanterings läge** : information som identifieras av sensorn visas i sensor konsolen. Dessutom levereras aviserings information via en IoT Hub och kan delas med andra Azure-tjänster, till exempel Azure Sentinel.<br />Mer information finns i den **inbyggda sensorn** . |
| **Hämta en sensor aktiverings fil** | För **lokalt hanterade** sensorer används en aktiverings fil för att hantera auktoriserade sensor aktiverings perioder.<br /><br />För **moln hanterade** sensorer används en aktiverings fil som en anslutning mellan sensorn och en IoT Hub. Mer information finns i **onboard-sensorer**. |
| **Ladda upp en aktiverings fil till sensorn** | Aktiverings filer måste överföras till dina företags sensorer. Nätverks övervakning och åtkomst till sensor konsol funktioner är inte tillgängliga förrän aktiverings filen laddas upp. Mer information finns i **överföra sensor aktiverings filer**. |
| **Uppdatera sensor nätverks parametrar före aktivering** | Uppdatera parametrarna som definieras under sensor installationen. Mer information finns i **fel! Referens källan hittades inte**.|


**Så här distribuerar du en sensor:**

1. Gå till Azure Defender för IoT från Azure Portal.

2. Välj **Kom igång.**

3. I avsnittet **identifiera ditt nätverk** väljer du **Konfigurera**.

   ![Azure Defender för IoT identifiera din nätverks visning](media/updates/image5.png)

4. Välj ett alternativ för att förvärva en sensor.

   ![Vyn Azure Defender för IoT Network sensor](media/updates/image6.png)

  - **Köp en förkonfigurerad sensor** : Microsoft och pilen samarbetar för att tillhandahålla förkonfigurerade sensorer. Om du vill köpa en förkonfigurerad sensor, kontakta pilen vid: <hardware.sales@arrow.com> . Sensorn skickas till din anläggning. Den senaste versionen är installerad.

  - **Ta med din egen installation (ISO-installation)** : lösningen körs på certifierade apparater. Använd [guiden Azure Defender för IoT Hardware Specifications](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) som referens när du köper din certifierade apparat.

    - Välj en version på menyn **Välj version** .

    - Välj **Hämta** och spara filen. I **installations guiden för Azure Defender för IoT** finns mer information om hur du hämtar ISO-avbildningen och installerar sensor program varan.

5. När program varan har installerats på sensorn, eller om du har fått en förkonfigurerad sensor, utför du nätverks konfigurations aktiviteterna. Mer information finns i [konfigurations guiden för nätverket](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="onboard-a-sensor"></a>Publicera en sensor

Sensorer måste registreras på Azure Defender för IoT-portalen. Onboarding sker i två faser:

1. Registrera sensorn med Azure Defender för IoT-portalen.

2. Ladda ned en aktiverings fil för sensorn. Filen kommer senare att överföras till sensorn.

**Så här öppnar du sidan onboarding:**

1. Gå till sidan för att **komma igång** med Microsoft Azure Defender för IoT.

2. Välj **2)** från Azure Defender for IoT.

   ![Skärm bild av överutskrifts visning](media/updates/image7.png)

3. Sidan **onboard sensor** öppnas. Den innehåller alternativ för att registrera sensorn och hämta aktiverings filen.

   ![Skärm bild av överbrädets sid visning](media/quickstart/onboard-sensors.png)

**Registrera sensorn:**

1. Välj ett sensor namn. För moln hanterade sensorer används namnet som anges här namnet som visas i sensor konsolen: det här namnet kan inte ändras från konsolen. För lokalt hanterade sensorer kommer det namn som används här att lagras i Azure, men det kan uppdateras i sensor konsolen. Vi rekommenderar att du inkluderar IP-adressen för sensorn som du installerade som en del av namnet eller använder ett enkelt identifierbart namn. På så sätt kan du lättare spåra och konsekvent namnge mellan registrerings namnet i Azure Defender för IoT-portalen och IP-adressen för den distribuerade sensorn som visas i sensor konsolen.

2. Välj en prenumeration i list rutan prenumeration.
3. I fältet **allokerade enheter** anger du antalet enheter som omfattas av prenumerationen. Du bör ange det här numret igen för varje sensor som du har publicerat som är associerad med samma prenumeration.  Om till exempel prenumerationen "A" är kopplad till 6000 enheter anger du 6000 för varje sensor som är associerad med prenumerationen "A".   
4. Välj ett sensor hanterings läge med hjälp av **anslutning till molnet** . Om växlings listen är på, hanteras sensorn av **molnet**. Om växlingen är avstängd **hanteras sensorn lokalt**.


| Sensor hanterings läge | Description                                                |
| ---------------------- | ---------------------------------------------------------  |
| **Moln hanterat**          | Information som identifieras av sensorn visas i sensor konsolen. Dessutom levereras aviserings information via en IoT Hub och kan delas med andra Azure-tjänster, till exempel Azure Sentinel.<br /><br />Välj en IoT Hub som ska associeras med den här sensorn.<br /><br />Du måste överföra en moln hanterad aktiverings fil till moln hanterade sensorer.<br /><br />Mer information finns i **överföra sensor aktiverings filer** . |
| **Lokalt hanterad**        | Information som identifieras av sensorer som hanteras lokalt visas i sensor konsolen. Om du arbetar i ett Air-gapped nätverk och vill ha en enhetlig vy över all information som identifieras av flera lokalt hanterade sensorer, kan du arbeta med den lokala hanterings konsolen.<br /><br />Sensorer som *hanteras lokalt* är kopplade till en Azure-prenumeration och innehåller instruktioner om sensorn för aktiveringens förfallo period.<br /><br />Välj en prenumeration som du vill koppla till den här sensorn.<br /><br />Du måste ladda upp en lokalt hanterad aktiverings fil till varje sensor. Mer information finns i **överföra sensor aktiverings filer** . |

5. Välj **Register** (Registrera).

6. På sidan Hämta aktiverings fil väljer du **Hämta aktiverings** fil.

   ![Skärm bild av vyn onboard-sensor](media/updates/image9.png)

7. Spara filen. Fil namnet är format: `<hub_name>_<sensor_name>` . Sensor namnet avser det namn som du definierade ovan.

8. Välj **Slutför**.

9. För att slutföra sensor onboarding-processen laddar du upp filen till sensorn från Azure Defender för IoT-konsolen.
 
## <a name="upload-a-sensor-activation-file"></a>Ladda upp en sensor aktiverings fil

Den här artikeln beskriver hur du laddar upp en aktiverings fil till sensorn. Du bör ha fått filen när du onboarding The sensorn.

**Lokalt hanterade aktiverings filer**

Lokalt hanterade sensorer är associerade med en Azure-prenumeration.  Aktiverings filen för dina lokalt hanterade sensorer innehåller ett förfallo datum. En månad före det här datumet visas ett varnings meddelande överst i sensor konsolen. Varningen är kvar tills du har uppdaterat aktiverings filen.

Du kan fortsätta att arbeta med Azure Defender för IoT-funktioner när aktiverings filen upphör att gälla.

**Moln hanterade aktiverings filer**

Sensorer som är molnbaserade är associerade med en Azure-IoT Hub. Dessa sensorer begränsas inte av tids perioder för aktiverings filen. Aktiverings filen för moln hanterade sensorer används för att säkerställa anslutning till IoT Hub.

Mer information om IoT-hubbar finns i [about IoT Hub](../iot-hub/about-iot-hub.md).

**Så här överför du aktiverings filen:**

1. Kontrol lera att du har:

   - IP-adress för sensorn som definierats under installationen

   - Autentiseringsuppgifter för användar inloggning som krävs för sensorn

2. Få åtkomst till Azure Defender för IoT-konsolen från din webbläsare med hjälp av IP-adressen för den här sensorn.

3. Logga in på Azure Defender för IoT sensor-konsolen.

   ![Logga in i vyn Azure Defender för IoT Console](media/updates/image11.png)

4. När inloggningen är klar öppnas aktiverings skärmen. Välj överför och välj den aktiverings fil som du sparade.

   ![Vyn Azure Defender för IoT-aktivering](media/updates/image12.png)

5. Godkänn de allmänna villkoren.

6. Välj **Aktivera**. Parametrarna för nätverks konfiguration för sensorn definierades vid program varu installationen eller när en förkonfigurerad sensor köptes. Följande parametrar har definierats:
   - IP-adress
   - DNS  
   - Standard gateway
   - Nätmask
   - Värdnamn
 
   Du kanske vill uppdatera den här informationen innan du aktiverar sensorn på grund av följande:
   - Du måste ändra de förinställda parametrarna som definierats  
   - Du vill konfigurera om nätverks parametrarna efter installationen kan du också definiera proxyinställningar innan du aktiverar sensorn.

7. Välj länken **sensor för nätverks konfiguration** formuläret i dialog rutan Aktivera.  

   ![Skärm bild av vyn Redigera nätverks konfiguration](media/updates/image13.png)

8. De parametrar som definieras under installationen visas. Ett alternativ är också tillgängligt definiera proxy. Uppdatera efter behov och välj **Spara**.
 
 
## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du distribuerar och onboard-sensorer. Mer information om att komma igång finns i följande artikel:

- [Komma igång](getting-started.md)
