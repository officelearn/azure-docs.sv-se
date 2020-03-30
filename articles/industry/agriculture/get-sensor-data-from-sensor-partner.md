---
title: Hämta sensordata från partnerna
description: I den här artikeln beskrivs hur du hämtar sensordata från partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 916c828365c8f9f50f408bd6c51182bb6e89605f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384202"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Hämta sensordata från sensorpartners

Azure FarmBeats hjälper dig att föra strömmande data från dina IoT-enheter och sensorer till Datahub. För närvarande stöds följande partner för sensorenheter.

  ![FarmBeats partners](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Genom att integrera enhetsdata med Azure FarmBeats kan du hämta markdata från IoT-sensorerna som distribueras i din servergrupp till datahubben. Data, när de är tillgängliga, kan visualiseras via FarmBeats-acceleratorn. Data kan användas för datafusion och maskininlärning /artificiell intelligens (ML/AI) modellbyggnad med hjälp av FarmBeats.

Så här startar du strömmande sensordata:

-  Du har installerat FarmBeats på Azure Marketplace.
-  Du har bestämt dig för vilka sensorer och enheter som du vill installera på din gård.
-  Om du planerar att använda markfuktsensorer, använd FarmBeats Soil Moisture Sensor Placement karta för att få en rekommendation om antalet sensorer och var exakt du ska placera dem. Mer information finns i [Generera kartor](generate-maps-in-azure-farmbeats.md).
- Du köper och distribuerar enheter eller sensorer från din enhetspartner på din servergrupp. Se till att du kan komma åt sensordata via enhetspartners lösning.

## <a name="enable-device-integration-with-farmbeats"></a>Aktivera enhetsintegrering med FarmBeats

När du har påbörjat strömningen av sensordata kan du påbörja processen med att hämta data till ditt FarmBeats-system. Ange följande information till din enhetsleverantör för att aktivera integreringen i FarmBeats:

 - API-slutpunkt
 - Klient-ID:t
 - Klientorganisations-ID
 - Klienthemlighet
 - Anslutningssträng för EventHub

Du kan generera ovanstående information genom att följa dessa steg: (Observera att dessa steg måste göras på Azure så att du behöver åtkomst till Azure-prenumerationen där FarmBeats distribueras)

1. Logga in på https://portal.azure.com/.

2. **Om du är på FarmBeats version 1.2.7 eller senare, hoppa över steg 2a, 2b och 2c, och gå till steg 3.**. Du kan kontrollera FarmBeats version genom att klicka på ikonen Inställningar längst upp till höger i FarmBeats UI.

2a. Gå till Azure Active Directory -> Appregistreringar

2b. Klicka på appregistreringen som skapades som en del av din FarmBeats-distribution. Det kommer att ha samma namn som din FarmBeats datahubb.

2c. Klicka på "Exponera ett API" -> Klicka på "Lägg till ett klientprogram" och skriv **in 04b07795-8ddb-461a-bbee-02f9e1bf7b46** och kontrollera "Auktorisera omfattning". Detta ger åtkomst till Azure CLI (Cloud Shell) för att utföra stegen nedan.

3. Öppna Cloud Shell. Det här alternativet är tillgängligt i verktygsfältet i det övre högra hörnet av Azure-portalen.

    ![Verktygsfältet Azure Portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

5. Kontrollera att miljön är inställd på **PowerShell**. Som standard är den inställd på Bash.

    ![Verktygsfältsinställningen i PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

6. Gå till din hemkatalog.

   ```azurepowershell-interactive 

    cd  

    ```

7. Kör följande kommando. Detta kommer att ladda ner ett skript till din hemkatalog.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1 

    ```

8. Kör följande skript. Skriptet frågar efter klient-ID som kan erhållas från Azure Active Directory -> översiktssida.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

9. Följ instruktionerna på skärmen för att samla in värdena för **API-slutpunkt,** **klient-ID,** **klient-ID,** **Klienthemlighet**och **EventHub-anslutningssträng**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Integrera enhetsdata med hjälp av genererade autentiseringsuppgifter

Nu har du följande information som genereras från föregående avsnitt.
 - API-slutpunkt
 - Anslutningssträng för EventHub
 - Klientorganisations-ID
 - Klienthemlighet
 - Klient-ID:t
 
Du måste ge detta till din enhetspartner för att länka FarmBeats. Gå till enhetspartnerportalen för att göra samma sak. Om du till exempel använder enheter från Davis Instruments, Teralytic eller Pessl Instruments (Metos.at) gå till motsvarande sidor som nämns nedan:

[Davis instrument](https://weatherlink.github.io/azure-farmbeats/setup)

[Teralytisk](https://app.teralytic.com/)

[Pessl Instrument](https://ng.fieldclimate.com/user-api-services)

 Enhetsprovidern bekräftar en lyckad integrering. Vid bekräftelse kan du visa alla enheter och sensorer på Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Visa enheter och sensorer

Använd följande avsnitt för att visa enheter och sensorer för din servergrupp.

### <a name="view-devices"></a>Visa enheter

FarmBeats stöder för närvarande följande enheter:

- **Nod:** En enhet som en eller flera sensorer är anslutna till.
- **Gateway**: En enhet som en eller flera noder är anslutna till.

Följ de här stegen.

1. Välj **Enheter** på startsidan på menyn.
  Sidan **Enheter** visar enhetstyp, modell, status, den servergrupp den placeras i och det senast uppdaterade datumet för metadata. Som standard är servergruppskolumnen inställd på *NULL*. Du kan välja att tilldela en enhet till en servergrupp. Mer information finns i [Tilldela enheter](#assign-devices).
2. Välj den enhet som ska visa enhetsegenskaper, telemetri och underordnade enheter som är anslutna till enheten.

    ![Sidan Enheter](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Visa sensorer

Följ de här stegen.

1. Välj **Sensorer** på startsidan på menyn.
  På sidan **Sensorer** visas information om vilken typ av sensor, vilken servergrupp den är ansluten till, överordnad enhet, portnamn, porttyp och den senast uppdaterade statusen.
2. Välj sensorn för att visa sensoregenskaper, aktiva varningar och telemetri från sensorn.

    ![Sidan Sensorer](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Tilldela enheter  

När sensordata flödar in kan du tilldela den till den servergrupp där du distribuerade sensorerna.

1. Välj **Gårdar** på startsidan på menyn. Listsidan **Gårdar** visas.
2. Markera den servergrupp som du vill tilldela enheten till och välj **Lägg till enheter**.
3. Fönstret **Lägg till enheter** visas. Markera den enhet som du vill tilldela servergruppen.

    ![Fönstret Lägg till enheter](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Välj **Lägg till enheter**. Du kan också gå till menyn **Enheter,** välja de enheter som du vill tilldela en servergrupp och välja **Associera enheter**.
5. I fönstret **Associera enheter** väljer du servergruppen i listrutan och väljer Använd **för alla** om du vill associera servergruppen till alla valda enheter.

    ![Fönstret Associera enheter](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Om du vill associera varje enhet till en annan servergrupp markerar du nedpilen i kolumnen **Tilldela till servergrupp** och väljer en servergrupp för varje enhetsrad.
7. Välj **Tilldela** om du vill slutföra enhetstilldelningen.

### <a name="visualize-sensor-data"></a>Visualisera sensordata

Följ de här stegen.

1. På startsidan väljer du **Gårdar** på menyn för att visa sidan **Grupper.**
2. Välj den **servergrupp** som du vill visa sensordata för.
3. På instrumentpanelen **i servergruppen** kan du visa telemetridata. Du kan visa live telemetri eller använda **anpassat område** för att se ett visst datumintervall.

    ![Instrumentpanel för servergrupp](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Ta bort en sensor

Följ de här stegen.

1. På startsidan väljer du **Sensorer** på menyn för att visa **sidan Sensorer.**
2. Markera den enhet som du vill ta bort och välj **Ta bort** i bekräftelsefönstret.

    ![Knappen Ta bort](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Ett bekräftelsemeddelande visar att sensorn har tagits bort.

## <a name="delete-devices"></a>Ta bort enheter

Följ de här stegen.

1. På startsidan väljer du **Enheter** på menyn för att visa sidan **Enheter.**
2. Markera den enhet som du vill ta bort och välj **Ta bort** i bekräftelsefönstret.

    ![Knappen Ta bort](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Nästa steg

Nu har du sensordata som flödar in i din Azure FarmBeats-instans. Nu lär du dig att [generera kartor](generate-maps-in-azure-farmbeats.md#generate-maps) för dina gårdar.
