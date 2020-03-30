---
title: Integrera time series-insikter med fjärrövervakning – Azure | Microsoft-dokument
description: I det här ingången får du lära dig hur du konfigurerar Time Series Insights för en befintlig lösning för fjärrövervakning som inte redan innehåller Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564652"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrera Azure Time Series Insights med fjärrövervakning

Azure Time Series Insights är en fullständigt hanterad analys-, lagrings- och visualiseringstjänst för hantering av tidsseriedata i IoT-skala i molnet. Du kan använda Time Series Insights för att lagra och hantera tidsseriedata, utforska och visualisera händelser samtidigt, utföra rotorsaksanalys och jämföra flera platser och tillgångar.

Lösningsacceleratorn för fjärrövervakning tillhandahåller nu automatisk distribution och integrering med Time Series Insights. I det här ingången får du lära dig hur du konfigurerar Time Series Insights för en befintlig lösning för fjärrövervakning som inte redan innehåller Time Series Insights.

> [!NOTE]
> Time Series Insights är för närvarande inte tillgängligt i Azure China-molnet. Nya snabbacceleratorer för fjärrövervakning i Azure China-molnet använder Cosmos DB för all lagring.

## <a name="prerequisites"></a>Krav

För att slutföra den här inmatningen måste du redan ha distribuerat en lösning för fjärrövervakning:

* [Distribuera lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Skapa en dedikerad konsumentgrupp i din IoT Hub som ska användas för att strömma data till Time Series Insights.

> [!NOTE]
> Konsumentgrupper används av program för att hämta data från Azure IoT Hub. Varje konsumentgrupp tillåter upp till fem output konsumenter. Du bör skapa en ny konsumentgrupp för var femte utdatamottagare och du kan skapa upp till 32 konsumentgrupper.

1. Klicka på knappen Cloud Shell i Azure-portalen.

1. Kör följande kommando för att skapa en ny konsumentgrupp. Använd namnet på IoT-hubben i distributionen för fjärrövervakning och namnet på distributionen fjärrövervakning som resursgruppsnamn:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Distribuera insikter i tidsserier

Distribuera sedan Time Series Insights som ytterligare en resurs i din lösning för fjärrövervakning och anslut den till IoT-hubben.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Skapa en resurs** > **Internet of Things** > **Time Series Insights**.

    ![Nya Time Series Insikter](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Om du vill skapa en time series insights-miljö använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn på miljö | Följande skärmdump använder namnet **contorosrmtsi**. Välj ditt eget unika namn när du slutför det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **Använd befintliga**. Välj namnet på din befintliga resursgrupp för fjärrövervakning. |
    | Location | Vi använder **östra USA**. Skapa din miljö i samma region som din lösning för fjärrövervakning om möjligt. |
    | Sku |**S1** |
    | Kapacitet | **1** |

    ![Skapa insikter i tidsserier](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klicka på **Skapa**. Det kan ta en stund för miljön att skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelsekälla för att ansluta till din IoT-hubb. Se till att du använder den konsumentgrupp som skapats i föregående steg. Time Series Insights kräver att varje tjänst har en dedikerad konsumentgrupp som inte används av en annan tjänst.

1. Navigera till din nya Time Series Insights-miljö.

1. Till vänster väljer du **Händelsekällor**.

    ![Visa händelsekällor](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **Lägg till**.

    ![Lägg till händelsekälla](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera IoT-hubben som en ny händelsekälla använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Händelsekällans namn | Följande skärmbild använder namnet **contosorm-iot-hub**. Använd ditt eget unika namn när du slutför det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använda IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID:t | I listrutan väljer du din Azure-prenumeration. |
    | Iot hub-namn | **contosorma57a6**. Använd namnet på din IoT-hubb från fjärrövervakningslösningen. |
    | Principnamn för IoT Hub | **iothubowner** Kontrollera att principen som används är en ägarprincip. |
    | Principnyckel för Iot-hubb | Det här fältet fylls i automatiskt. |
    | Iot hub konsumentgrupp | **timeseriesinights** |
    | Händelseserialiseringsformat | **Json**     | 
    | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelsekälla](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

## <a name="configure-the-data-access-policy"></a>Konfigurera dataåtkomstprincipen

Om du vill vara säker på att alla användare som har åtkomst till din fjärrövervakningslösning kan utforska data i Utforskaren för Time Series Insights lägger du till ditt program och användare under dataåtkomstprinciper i Azure-portalen. 

1. Visa **resursgrupper** i navigationslistan.

1. Välj resursgruppen **ContosoRM.**

1. Välj **contosormtsi** i listan över Azure-resurser.

1. Välj **Dataåtkomstprinciper** om du vill visa den aktuella listan över rolltilldelningar.

1. Välj **Lägg till** för att öppna fönstret Välj **användarregel.**

   Om du inte har behörighet att tilldela roller visas inte alternativet **Lägg till.**

1. I listrutan **Roll** väljer du en roll som **Läsare** och **Deltagare**.

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** för att skapa rolltilldelningen. Efter en stund tilldelas säkerhetsobjektet rollen i dataåtkomstprinciper.

> [!NOTE]
> Om du behöver ge ytterligare användare åtkomst till Utforskaren för Time Series Insights kan du använda de här stegen för att [bevilja dataåtkomst](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurera Azure Stream Analytics 

Nästa steg är att konfigurera azure stream Analytics Manager-mikrotjänsten så att meddelanden skickas till Cosmos DB och lagra dem endast i Time Series Insights. Hoppa över det här steget om du vill duplicera dina meddelanden i Cosmos DB.

1. Visa **resursgrupper** i navigationslistan.

1. Välj resursgruppen **ContosoRM.**

1. Hitta azure stream analytics (ASA) direktuppspelningsjobbet i listan över resurser. Resursnamnet börjar med **strömmande jobb.**

1. Högst upp klickar du på knappen för att stoppa ASA-streamingjobben.

1. Redigera ASA-frågan och ta bort **satserna SELECT**, **INTO**och **FROM** som pekar på meddelandeströmmen i Cosmos DB. Dessa satser bör finnas längst ned i frågan och se ut som följande exempel:

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

6. Starta om streamingjobben för Azure Stream Analytics.

7. Hämta de senaste ändringarna i azure stream analytics manager-mikrotjänsten genom att skriva följande kommando i kommandotolken:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurera mikrotjänsten Telemetri

Hämta den senaste telemetrimikrotjänsten genom att skriva följande kommando i kommandotolken:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Valfritt]* Konfigurera webbgränssnittet så att det länkar till utforskaren Time Series Insights

Om du enkelt vill visa dina data i Utforskaren för Time Series Insights rekommenderar vi att du anpassar användargränssnittet så att det enkelt kan länka till miljön. Om du vill göra det hämtar du de senaste ändringarna i webbgränssnittet med följande kommando:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurera miljövariabler

För att slutföra Time Series Insights-integreringen måste du konfigurera miljön för distributionen för de uppdaterade mikrotjänsterna.

### <a name="basic-deployments"></a>Grundläggande distributioner

Konfigurera `basic` distributionsmiljön för de uppdaterade mikrotjänsterna.

1. Klicka på fliken Azure **Active Directory** på den vänstra panelen i Azure-portalen.

1. Klicka på **Appregistreringar**.

1. Sök efter och klicka på din **ContosoRM** ansökan.

1. Navigera till **Inställningsnycklar** > **Keys** och skapa sedan en ny nyckel för ditt program. Se till att kopiera nyckelvärdet till säker plats.

1. Hämta den [senaste docker-compose yaml-filen](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) från GitHub-repo med den senaste taggen. 

1. SSH till den virtuella datorn genom att följa stegen som beskrivs om [hur du skapar och använder SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. När du `cd /app`är ansluten skriver du .

1. Lägg till följande miljövariabler till varje mikrotjänst i docker compose yaml-filen och skriptet `env-setup` i den virtuella datorn:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navigera till **telemetritjänsten** och redigera även dockerkompostfilen genom att lägga till samma miljövariabler ovan.

1. Navigera till **ASA-hanteraren** och redigera docker-compose-filen genom att lägga till `PCS_TELEMETRY_STORAGE_TYPE`.

1. Starta om dockerbehållarna med hjälp av `sudo ./start.sh` den virtuella datorn.

> [!NOTE]
> Ovanstående konfiguration av miljövariabler gäller för fjärrövervakningsversioner före 1.0.2

### <a name="standard-deployments"></a>Standarddistributioner

Konfigurera `standard` distributionsmiljön för de uppdaterade mikrotjänsterna ovan

1. Kör på `kubectl proxy`kommandoraden . Mer information finns [i åtkomst till Kubernetes API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Öppna kubernetes hanteringskonsolen.

1. Leta reda på konfigurationskartan för att lägga till följande nya miljövariabler för TSD:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Redigera mallen yaml-fil för telemetritjänstpodden:

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

5. Redigera mallen yaml-fil för ASA manager service pod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du utforskar dina data och diagnostiserar en avisering i Utforskaren för Time Series Insights finns i vår handledning om [hur du utför en grundorsaksanalys](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Mer information om hur du utforskar och frågar data i utforskaren Time Series Insights finns i dokumentationen för [Utforskaren av Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
