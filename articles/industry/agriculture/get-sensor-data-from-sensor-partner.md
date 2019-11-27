---
title: Hämta sensor data från partner
description: Beskriver hur du hämtar sensor data från partners
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b985dfc1f16372c3fad1b0a5c0894931b4c15dcc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406494"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Hämta sensor data från sensor partner

Azure-FarmBeats hjälper dig att ta strömma data från dina IoT-enheter och sensorer till datahubben. För närvarande stöds följande sensor enhets partner:

  ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Genom att integrera enhets data med Azure FarmBeats kan du hämta grunddata från IoT-sensorer som distribuerats i Server gruppen till datahubben. Data, när de är tillgängliga, kan visualiseras genom FarmBeats-acceleratorn och kan användas för data fusion och AI/ML modell utveckling med FarmBeats.

För att starta strömning av sensor data kontrollerar du följande:

-  Du har installerat FarmBeats från Azure Marketplace.
-  Du har valt de sensorer och enheter som du vill installera i Server gruppen.
-  Om du planerar att använda jord fuktighets sensorer kan du använda FarmBeats för att få en rekommendation om antalet sensorer och var exakt bör placera sensorerna. Mer information finns i [generera kartor](generate-maps.md).

- Köp och distribuera enheter/sensorer från din enhets partner i Server gruppen. Se till att du har åtkomst till sensor data via din enhets partner lösning.

### <a name="enable-device-integration-with-farmbeats"></a>Aktivera enhets integrering med FarmBeats   

När du har startat strömningen av sensor data kan du starta processen med att hämta data till FarmBeats-systemet. Du måste ange följande information för din enhets leverantör för att kunna integrera med FarmBeats:  

 - API-slutpunkt  
 - Klient-ID:t  
 - Klientorganisations-ID  
 - Klienthemlighet  
 - EventHub-anslutningssträng

Ovanstående information tillhandahålls av system integreraren. Kontakta system integreraren om du har problem med att aktivera enhets integrering.

Du kan också generera autentiseringsuppgifterna genom att köra det här skriptet från Azure Cloud Shell. Följ stegen nedan:

1. Hämta [zip-filen](https://aka.ms/farmbeatspartnerscript) och extrahera den till den lokala enheten. Du hittar två filer i ZIP-filen.
2. Logga in på https://portal.azure.com/ och öppna Cloud Shell (det här alternativet finns i det övre högra fältet i portalen)  

    ![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Se till att miljön är inställd på **PowerShell** – som standard är den inställd på bash.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Överför de två filerna (från steg 1 ovan) i din Cloud Shell.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Gå till den katalog där filerna laddades upp (som standard laddas den upp till arbets katalogen > användar namn).
6. Kör följande skript:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. Följ anvisningarna på skärmen för att avbilda värdena. (API-slutpunkt, klient-ID, klient-ID, klient hemlighet och EventHub-anslutningssträng). EventHub-anslutningssträngen är tillgänglig som en del av API-svaret i Swagger.

**Integrera enhets data med de genererade autentiseringsuppgifterna**

Besök enhets partner portalen för att länka FarmBeats med den uppsättning autentiseringsuppgifter som du skapade i föregående avsnitt.

 - API-slutpunkt  
 - EventHub-anslutningssträng  
 - Klientorganisations-ID  
 - Klienthemlighet  
 - Klient-ID:t  

 Enhets leverantören bekräftar en lyckad integrering. Vid bekräftelse kan du Visa alla enheter och sensorer på Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Visa enheter och sensorer

Använd följande avsnitt om du vill visa enheter och sensorer i Server gruppen.

### <a name="view-devices"></a>Visa enheter

För närvarande stöder FarmBeats följande enheter:

- **Nod**: en enhet som en eller flera sensorer är kopplade till.
- **Gateway**: en enhet som en eller flera noder är kopplade till.

Använd följande steg:

1. På sidan start väljer du **enheter** på menyn.
  Sidan enheter visar enhets typ, modell, status, den server grupp som den placerats i och senaste uppdaterade datum för metadata. Som standard har kolumnen Server grupp värdet NULL. Du kan välja att tilldela en enhet till en Server grupp. Mer information finns i [tilldela enheter](#assign-devices).
2. Välj enhet för att Visa enhets egenskaper, telemetri och underordnade enheter som är anslutna till enheten.  

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Visa sensorer

Använd följande steg:

1. På Start sidan väljer du **sensorer** på menyn.
  På sidan sensorer visas information om typen av sensor, den server grupp som den är ansluten till, den överordnade enheten, Port namnet, Port typen och den senast uppdaterade statusen.
2. Välj sensor för att Visa sensor egenskaper, aktiva aviseringar och telemetri från sensorn.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Tilldela enheter  

När du har sensor data som flödar in kan du tilldela den till Server gruppen där du har distribuerat sensorer.

1. På sidan start väljer du **Server grupper** på menyn, sidan **Server** grupps lista visas.  
2. Välj den server grupp som du vill tilldela enheten och välj **Lägg till enheter**.  
3. Fönstret **Lägg till enheter** visas. Välj den enhet som du vill tilldela till Server gruppen.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Välj **Lägg till enheter**. Du kan också gå till menyn **enheter** , markera de enheter som du vill tilldela till en Server grupp och välja **Associera enheter**.  
5. I fönstret **Associera enheter** väljer du gruppen från List rutan och väljer **Använd för alla** för att koppla Server gruppen till alla valda enheter.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Om du vill koppla varje enhet till en annan server grupp markerar du List rutan i kolumnen **tilldela till Server grupp** och väljer en Server grupp för varje enhets rad.  
7. Välj **tilldela** för att slutföra tilldelning av enheter.

### <a name="visualize-sensor-data"></a>Visualisera sensor data

Använd följande steg:

1. På sidan start väljer du **Server grupper** på menyn för att visa sidan **grupper** .  
2. Välj den **Server grupp** som du vill visa sensor data för.  
3. På instrument panelen för **Server gruppen** kan du Visa telemetridata. Du kan välja att Visa Live-telemetri eller använda det **anpassade intervallet** för att Visa inom ett visst datum intervall.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Ta bort sensor

Följ de här stegen:

1. På Start sidan väljer du **sensorer** på menyn för att visa sidan **sensorer** .  
2. Välj den enhet som du vill ta bort och välj **ta bort** från bekräftelse fönstret.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Ett bekräftelse meddelande visas om att sensorn har tagits bort.  

## <a name="delete-devices"></a>Ta bort enheter

Följ de här stegen:

1. På sidan start väljer du **enheter** på menyn för att visa sidan enheter.  
2. Välj den enhet som du vill ta bort och välj **ta bort** från bekräftelse fönstret.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Nästa steg

Nu har du sensor data som flödar till din Azure FarmBeats-instans. Nu kan du lära dig hur du [genererar kartor](generate-maps.md#generate-maps) för dina grupper.
