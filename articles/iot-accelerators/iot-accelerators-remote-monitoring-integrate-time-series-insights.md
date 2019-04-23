---
title: Integrera Time Series Insights med fjärrövervakning – Azure | Microsoft Docs
description: I den här anvisningen du lära dig hur du konfigurerar Time Series Insights för en befintlig fjärrövervakning lösning som inte redan innehåller Time Series Insights.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 4cc9b0051eaa12eee07f067352126ad159107a83
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007482"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrera Azure Time Series Insights med fjärrövervakning

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som du kan använda för att hantera Time Series-data på IoT-skala i molnet. Du kan använda Time Series Insights för att lagra och hantera time series-data, utforska och visualisera händelser samtidigt, utföra Rotorsaksanalys och jämföra flera platser och tillgångar.

Lösningsacceleratorn för fjärrövervakning tillhandahåller nu automatisk distribution och integrering med Time Series Insights. I den här anvisningen att lära dig hur du konfigurerar Time Series Insights för en befintlig fjärrövervakning lösning som inte redan innehåller Time Series Insights.

> [!NOTE]
> Time Series Insights är inte tillgänglig för tillfället i Azure i Kina-molnet. Nya fjärrövervakning solution accelerator distributioner i Azure i Kina-molnet använder Cosmos DB för lagring av alla.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här anvisningen, måste du redan har distribuerat en lösning för fjärrövervakning:

* [Distribuera lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Skapa en dedikerad konsumentgrupp i din IoT-hubb som ska användas för strömmande data på Time Series Insights.

> [!NOTE]
> Konsumentgrupper används av program för att hämta data från Azure IoT Hub. Varje konsumentgrupp kan upp till fem utdata konsumenter. Du bör skapa en ny konsumentgrupp för var femte utdata mottagare och du kan skapa upp till 32 konsumentgrupper.

1. Klicka på knappen Cloud Shell i Azure-portalen.

1. Kör följande kommando för att skapa en ny konsumentgrupp. Använd namnet på IoT-hubben i distributionen fjärrövervakning och namnet på distributionen av fjärrövervakning som resursgruppens namn:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Distribuera Time Series Insights

Sedan distribuerar Time Series Insights som ytterligare en resurs i lösningen för fjärrövervakning och ansluter den till IoT hub.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **skapa en resurs** > **Internet of Things** > **Time Series Insights**.

    ![Ny Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Skapa din Time Series Insights-miljö använda värdena i tabellen nedan:

    | Inställning | Value |
    | ------- | ----- |
    | Miljönamn | Följande skärmbild använder namnet **contorosrmtsi**. Välj ditt eget unika namn när du har slutfört det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **Använd befintlig**. Välj namnet på en befintlig resursgrupp för fjärrövervakning. |
    | Location | Vi använder **USA, östra**. Skapa din miljö i samma region som din lösning för fjärrövervakning om möjligt. |
    | Sku |**S1** |
    | Kapacitet | **1** |

    ![Skapa Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klicka på **Skapa**. Det kan ta en stund medan miljön som ska skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelsekälla att ansluta till din IoT-hubb. Se till att du använder konsumentgruppen skapades i föregående steg. Time Series Insights kräver varje tjänst och har en dedikerad konsumentgrupp inte är i användning av en annan tjänst.

1. Gå till din nya Time Series Insights-miljö.

1. Till vänster, Välj **händelsekällor**.

    ![Visa händelsekällor](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **Lägg till**.

    ![Lägga till händelsekälla](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera din IoT-hubb som en ny händelsekälla, använda värdena i tabellen nedan:

    | Inställning | Value |
    | ------- | ----- |
    | Namn på händelsekälla | Följande skärmbild använder namnet **contosorm-iot-hub**. Använd ditt eget unika namn när du har slutfört det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använd IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID:t | I listrutan väljer du din Azure-prenumeration. |
    | IoT Hub-namn | **contosorma57a6**. Använd namnet på IoT-hubben från lösningen för fjärrövervakning. |
    | IoT Hub-principnamn | **iothubowner** se till att den princip som används är en ägare. |
    | IoT Hub-principnyckel | Det här fältet fylls i automatiskt. |
    | IoT Hub-konsumentgrupp | **timeseriesinsights** |
    | Händelseserialiseringsformat | **JSON**     | 
    | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelsekälla](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

## <a name="configure-the-data-access-policy"></a>Konfigurera principen för åtkomst av data

Om du vill se till att kan alla användare som har åtkomst till lösningen för fjärrövervakning utforska data i Time Series Insights explorer, lägga till ditt program och användare under principerna för dataåtkomst i Azure-portalen. 

1. Visa **resursgrupper** i navigationslistan.

1. Välj den **ContosoRM** resursgrupp.

1. Välj **contosormtsi** i listan över Azure-resurser.

1. Välj **Dataåtkomstprinciper** att se den aktuella listan över rolltilldelningar.

1. Välj **Lägg till** att öppna den **Välj användarroll** fönstret.

   Om du inte har behörighet att tilldela roller kan du inte ser den **Lägg till** alternativet.

1. I den **rollen** listrutan, väljer du en roll som **läsare** och **deltagare**.

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** för att skapa rolltilldelningen. Efter en liten stund tilldelas säkerhetsobjektet rollen i principerna för dataåtkomst.

> [!NOTE]
> Om du vill bevilja ytterligare användare åtkomst till Time Series Insights explorer kan du använda dessa steg för att [bevilja åtkomst till data](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Configure Azure Stream Analytics 

Nästa steg är att konfigurera Azure Stream Analytics Manager mikrotjänster för att sluta skicka meddelanden till Cosmos DB och lagra dem i Time Series Insights. Hoppa över det här steget om du vill duplicera meddelandena i Cosmos DB.

1. Visa **resursgrupper** i navigationslistan.

1. Välj den **ContosoRM** resursgrupp.

1. Hitta den Azure Stream Analytics (ASA) direktuppspelningsjobbet i listan över resurser. Resursnamnet som börjar med **streamingjobs -**.

1. Klicka på knappen för att stoppa ASA direktuppspelningsjobb överst på sidan.

1. Redigera ASA-frågan och ta bort den **Välj**, **INTO**, och **FROM** satser som pekar på meddelandena strömma i Cosmos DB. Dessa satser måste vara längst ned på frågan och se ut som i följande exempel:

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

6. Starta om Azure Stream Analytics direktuppspelningsjobb.

7. Hämta de senaste ändringarna till Azure Stream Analytics manager mikrotjänst genom att skriva följande kommando i Kommandotolken:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurera telemetri mikrotjänst

Hämta den senaste telemetri mikrotjänst genom att skriva följande kommando i Kommandotolken:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Valfritt]*  Konfigurera webbgränssnittet att länka till Time Series Insights explorer

Om du vill visa enkelt dina data i Time Series Insights explorer, rekommenderar vi anpassa Gränssnittet enkelt länka till miljön. Du gör detta genom att hämta de senaste ändringarna till Webbgränssnittet med följande kommando:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurera miljövariabler

För att slutföra Time Series Insights-integrering, behöver du konfigurera miljön för distributionen för den uppdaterade mikrotjänster.

### <a name="basic-deployments"></a>Grundläggande distributioner

Konfigurera miljön för `basic` distributionen för den uppdaterade mikrotjänster.

1. I Azure-portalen klickar du på den **Azure Active Directory** fliken på den vänstra panelen.

1. Klicka på **appregistreringar**.

1. Sök efter och klicka på din **ContosoRM** program.

1. Gå till **inställningar** > **nycklar** och sedan skapa en ny nyckel för ditt program. Se till att kopiera nyckel-värde på säker plats.

1. Hämta den [senaste docker compose yaml-fil](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) från GitHub-lagringsplatsen med senaste taggen. 

1. SSH till den virtuella datorn genom att följa anvisningarna på [hur du skapar och använder SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. När du är ansluten, Skriv `cd /app`.

1. Lägg till följande miljövariabler till varje mikrotjänst i docker compose yaml-fil och `env-setup` skriptet i den virtuella datorn:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navigera till den **telemetritjänsten** och även redigera docker compose-fil genom att lägga till samma miljövariablerna ovan.

1. Navigera till den **ASA manager-tjänsten** och redigera docker compose-fil genom att lägga till `PCS_TELEMETRY_STORAGE_TYPE`.

1. Starta om docker-behållare med hjälp av `sudo ./start.sh` från den virtuella datorn.

> [!NOTE]
> Ovanstående konfiguration av miljövariabler är giltig för fjärrövervakning versioner före 1.0.2

### <a name="standard-deployments"></a>Standard-distributioner

Konfigurera miljön för `standard` distributionen för den uppdaterade mikrotjänster som ovan

1. På kommandoraden, kör `kubectl proxy`. Mer information finns i [åtkomst till Kubernetes API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Öppna hanteringskonsolen för Kubernetes.

1. Hitta configuration kartan för att lägga till följande nya miljövariabler för TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Redigera mall yaml-fil för telemetri service pod:

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

5. Redigera mall yaml-fil för ASA manager service pod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Nästa steg

* Läs om hur du utforska dina data och diagnostisera en avisering i Time Series Insights explorer i våra självstudier på [genomför en rot Rotorsaksanalys](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Om du vill lära dig att utforska och fråga efter data i Time Series Insights explorer, finns i dokumentationen på den [Azure Time Series Insights explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
