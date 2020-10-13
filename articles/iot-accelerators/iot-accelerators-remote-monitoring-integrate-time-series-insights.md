---
title: Integrera Time Series Insights med fjärrövervakning – Azure | Microsoft Docs
description: I den här instruktionen får du lära dig hur du konfigurerar Time Series Insights för en befintlig fjärr styrnings lösning som inte redan innehåller Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: b0ab1e2e43210ecd7a61b7dffcb12d2c7c5c1c46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86536614"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrera Azure Time Series Insights med fjärrövervakning

Azure Time Series Insights är en helt hanterad analys-, lagrings-och visualiserings tjänst för hantering av data i IoT-Scale Time-serien i molnet. Du kan använda Time Series Insights för att lagra och hantera Time Series-data, utforska och visualisera händelser samtidigt, utföra rotor Saks analys och jämföra flera webbplatser och till gångar.

Lösnings acceleratorn för fjärrövervakning tillhandahåller nu automatisk distribution och integrering med Time Series Insights. I den här instruktionen får du lära dig hur du konfigurerar Time Series Insights för en befintlig fjärr styrnings lösning som inte redan innehåller Time Series Insights.

> [!NOTE]
> Time Series Insights är för närvarande inte tillgängligt i Azure Kina-molnet. Nya distributioner av Solution Accelerator för fjärrövervakning i molnet i Azure Kina använder Cosmos DB för all lagring.

## <a name="prerequisites"></a>Krav

För att slutföra den här instruktionen måste du redan ha distribuerat en lösning för fjärrövervakning:

* [Distribuera lösnings acceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Skapa en konsument grupp

Skapa en dedikerad konsument grupp i IoT Hub som ska användas för strömning av data till Time Series Insights.

> [!NOTE]
> Konsument grupper används av program för att hämta data från Azure IoT Hub. Varje konsument grupp tillåter upp till fem utmatnings konsumenter. Du bör skapa en ny konsument grupp för var femte kanal mottagare och du kan skapa upp till 32 konsument grupper.

1. Klicka på knappen Cloud Shell i Azure Portal.

1. Kör följande kommando för att skapa en ny konsument grupp. Använd namnet på IoT Hub i distributionen av fjärrövervakningen och namnet på din distribution av fjärrövervakning som resurs gruppens namn:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Distribuera Time Series Insights

Sedan distribuerar du Time Series Insights som en ytterligare resurs i din lösning för fjärrövervakning och ansluter den till IoT Hub.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **skapa en resurs**  >  **Sakernas Internet**  >  **Time Series Insights**.

    ![Ny Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Använd värdena i följande tabell för att skapa en Time Series Insightss miljö:

    | Inställningen | Värde |
    | ------- | ----- |
    | Namn på miljö | Följande skärm bild använder namnet **contorosrmtsi**. Välj ditt eget unika namn när du är klar med det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **Använd befintlig**. Välj namnet på din befintliga resurs grupp för fjärr övervakning. |
    | Location | Vi använder **USA, östra**. Skapa din miljö i samma region som din lösning för fjärrövervakning om möjligt. |
    | Sku |**S1** |
    | Kapacitet | **1** |

    ![Skapa Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klicka på **Skapa**. Det kan ta en stund innan miljön har skapats.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelse källa för att ansluta till din IoT Hub. Se till att du använder den konsument grupp som skapades i föregående steg. Time Series Insights kräver att varje tjänst har en dedikerad konsument grupp som inte används av någon annan tjänst.

1. Navigera till din nya Time Series Insightss miljö.

1. Välj **händelse källor**till vänster.

    ![Visa händelse källor](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **Lägg till**.

    ![Lägg till händelse källa](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera IoT-hubben som en ny händelse källa använder du värdena i följande tabell:

    | Inställningen | Värde |
    | ------- | ----- |
    | Händelsekällans namn | Följande skärm bild använder namnet **contosorm-IoT-Hub**. Använd ett eget unikt namn när du har slutfört det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använd IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID:t | I listrutan väljer du din Azure-prenumeration. |
    | Namn på IoT Hub | **contosorma57a6**. Använd namnet på din IoT-hubb från din lösning för fjärr styrning. |
    | Principnamn för IoT Hub | **iothubowner** Se till att principen som används är en ägar princip. |
    | Princip nyckel för IoT Hub | Det här fältet fylls i automatiskt. |
    | Konsument grupp för IoT Hub | **timeseriesinsights** |
    | Händelseserialiseringsformat | **JSON**     | 
    | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelse källa](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

## <a name="configure-the-data-access-policy"></a>Konfigurera data åtkomst principen

För att se till att alla användare som har åtkomst till din lösning för fjärrövervakning kan utforska data i Time Series Insights Explorer lägger du till ditt program och användare under principer för data åtkomst i Azure Portal. 

1. Visa **resursgrupper** i navigationslistan.

1. Välj resurs gruppen **ContosoRM** .

1. Välj **contosormtsi** i listan över Azure-resurser.

1. Välj **data åtkomst principer** för att se den aktuella listan över roll tilldelningar.

1. Välj **Lägg till** för att öppna fönstret **Välj användar regel** .

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till** .

1. Välj en roll som **läsare** och **deltagare**i list rutan **roll** .

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** för att skapa rolltilldelningen. Efter en liten stund tilldelas säkerhets objekt rollen i data åtkomst principer.

> [!NOTE]
> Om du behöver ge fler användare åtkomst till Time Series Insights Explorer kan du använda de här stegen för att [bevilja åtkomst till data](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurera Azure Stream Analytics 

Nästa steg är att konfigurera mikrotjänsten i Azure Stream Analytics Manager att avbryta sändningen av meddelanden till Cosmos DB och lagra dem i Time Series Insights. Hoppa över det här steget om du vill duplicera dina meddelanden i Cosmos DB.

1. Visa **resursgrupper** i navigationslistan.

1. Välj resurs gruppen **ContosoRM** .

1. Hitta strömnings jobbet Azure Stream Analytics (ASA) i listan över resurser. Resurs namnet börjar med **streamingjobs-**.

1. Klicka på knappen längst upp för att stoppa strömmande jobb i ASA.

1. Redigera ASA-frågan och ta bort **Select**-, **into**-och **from** -satserna som pekar på meddelande strömmen i Cosmos dB. Dessa satser ska finnas längst ned i frågan och ser ut som i följande exempel:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Starta om Azure Stream Analytics strömmande jobb.

7. Hämta de senaste ändringarna i mikrotjänsten i Azure Stream Analytics Manager genom att skriva följande kommando i kommando tolken:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurera telemetri-mikrotjänsten

Hämta den senaste telemetri-mikrotjänsten genom att skriva följande kommando i kommando tolken:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Valfritt]* Konfigurera webb gränssnittet för att länka till Time Series Insights Explorer

För att enkelt visa dina data i Time Series Insights Explorer rekommenderar vi att du anpassar användar gränssnittet så att det blir enkelt att länka till miljön. Det gör du genom att hämta de senaste ändringarna i webb gränssnittet med hjälp av följande kommando:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurera miljövariabler

För att slutföra Time Series Insights-integrationen måste du konfigurera miljön för distributionen för de uppdaterade mikrotjänsterna.

### <a name="basic-deployments"></a>Grundläggande distributioner

Konfigurera `basic` distributions miljön för uppdaterade mikrotjänster.

1. Klicka på fliken **Azure Active Directory** i den vänstra panelen i Azure Portal.

1. Klicka på **Appregistreringar**.

1. Sök efter och klicka på ditt **ContosoRM** -program.

1. Navigera till **inställnings**  >  **nycklar** och skapa sedan en ny nyckel för ditt program. Se till att kopiera nyckelvärdet till en säker plats.

1. Hämta den [senaste docker-yaml-filen](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) från GitHub lagrings platsen med den senaste taggen. 

1. Använda SSH i den virtuella datorn genom att följa stegen som beskrivs i [skapa och använda SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. När du är ansluten skriver du `cd /app` .

1. Lägg till följande miljövariabler i varje mikrotjänst i Docker-yaml-filen och `env-setup` skriptet i den virtuella datorn:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navigera till **telemetri-tjänsten** och redigera Docker-filen genom att lägga till samma miljövariabler ovan.

1. Navigera till **tjänsten ASA Manager** och redigera Docker Compose-filen genom att lägga till `PCS_TELEMETRY_STORAGE_TYPE` .

1. Starta om Docker-behållare med hjälp av `sudo ./start.sh` från den virtuella datorn.

> [!NOTE]
> Ovanstående konfiguration av miljövariabler är giltig för fjärrövervakning av versioner innan 1.0.2

### <a name="standard-deployments"></a>Standard distributioner

Konfigurera `standard` distributions miljön för de uppdaterade Micro-tjänsterna ovan

1. Kör på kommando raden `kubectl proxy` . Mer information finns i [åtkomst till Kubernetes-API: et](https://kubernetes.io/docs/reference/access-authn-authz/#using-kubectl-to-start-a-proxy-server).

1. Öppna hanterings konsolen för Kubernetes.

1. Hitta konfigurations kartan för att lägga till följande nya miljövariabler för TSD:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Redigera mallen yaml File for telemetri service pod:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Redigera mallen yaml File for ASA Manager Service pod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Nästa steg

* Information om hur du utforskar dina data och diagnostiserar en avisering i Time Series Insights Explorer finns i vår självstudie om hur du [utför en rotor Saks analys](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Information om hur du utforskar och frågar efter data i Time Series Insights Explorer finns i dokumentationen i [Azure Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
