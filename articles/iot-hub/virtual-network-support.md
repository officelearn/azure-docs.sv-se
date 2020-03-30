---
title: Azure IoT Hub-stöd för virtuella nätverk
description: Så här använder du anslutningsmönster för virtuella nätverk med IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501433"
---
# <a name="iot-hub-support-for-virtual-networks"></a>IoT Hub-stöd för virtuella nätverk

Den här artikeln introducerar VNET-anslutningsmönstret och beskriver hur du konfigurerar en privat anslutningsupplevelse till en IoT-hubb via ett kundägt Azure VNET.

> [!NOTE]
> IoT Hub-funktionerna som beskrivs i den här artikeln är för närvarande tillgängliga för IoT-hubbar [som skapats med hanterad tjänstidentitet](#create-an-iot-hub-with-managed-service-identity) i följande regioner: Östra USA, Södra centrala USA och Västra USA 2.


## <a name="introduction"></a>Introduktion

Som standard mappar IoT Hub-värdnamn till en offentlig slutpunkt med en offentligt dirigerbar IP-adress över Internet. Som visas i bilden nedan delas den här offentliga slutpunkten för IoT Hub mellan nav som ägs av olika kunder och kan nås av IoT-enheter via både stora nätverk och lokala nätverk.

Flera IoT Hub-funktioner, inklusive [meddelanderoutning,](./iot-hub-devguide-messages-d2c.md) [filöverföring](./iot-hub-devguide-file-upload.md)och [massimport/export](./iot-hub-bulk-identity-mgmt.md) av enheter kräver på samma sätt anslutning från IoT Hub till en kundägd Azure-resurs över den offentliga slutpunkten. Som visas nedan utgör dessa anslutningsvägar tillsammans utgående trafik från IoT Hub till kundresurser.
![Offentlig slutpunkt för IoT Hub](./media/virtual-network-support/public-endpoint.png)


Av flera skäl kan kunder vilja begränsa anslutningen till sina Azure-resurser (inklusive IoT Hub) via ett virtuella nätverk som de äger och driver. Dessa skäl är:

* Vi presenterar ytterligare säkerhetslager via isolering på nätverksnivå för IoT-hubben genom att förhindra anslutningsexponering för ditt nav via det offentliga Internet.

* Aktivera en privat anslutningsupplevelse från dina lokala nätverkstillgångar som säkerställer att dina data och din trafik överförs direkt till Azure-stamnätsnätverket.

* Förhindra exfiltrationattacker från känsliga lokala nätverk. 

* Efter etablerade Azure-omfattande anslutningsmönster med hjälp av [privata slutpunkter](../private-link/private-endpoint-overview.md).


I den här artikeln beskrivs hur du uppnår dessa mål med hjälp av [privata slutpunkter](../private-link/private-endpoint-overview.md) för inkommande anslutning till IoT Hub, som använder Azure-undantag för betrodda förstapartstjänster för egress-anslutning från IoT Hub till andra Azure-resurser.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Inkommande anslutning till IoT Hub med privata slutpunkter

En privat slutpunkt är en privat IP-adress som allokeras i ett kundägt VNET via vilket en Azure-resurs kan nås. Genom att ha en privat slutpunkt för din IoT-hubb kan du tillåta tjänster som körs i ditt VNET att nå IoT Hub utan att kräva att trafik skickas till IoT Hubs offentliga slutpunkt. På samma sätt kan enheter som fungerar i din lokala kan använda [VPN (Virtual Private Network)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) eller [ExpressRoute](https://azure.microsoft.com/services/expressroute/) Private Peering för att få anslutning till ditt virtuella nätverk i Azure och därefter till din IoT Hub (via dess privata slutpunkt). Som ett resultat kan kunder som vill begränsa anslutningen till sina IoT-hubb offentliga slutpunkter (eller eventuellt helt blockera det) uppnå detta mål genom att använda [IoT Hub-brandväggsregler](./iot-hub-ip-filtering.md) samtidigt som de behåller anslutningen till sin Hub med hjälp av den privata slutpunkten.

> [!NOTE]
> Huvudfokus för den här inställningen är för enheter i ett lokalt nätverk. Den här inställningen rekommenderas inte för enheter som distribueras i ett nätverk i stort område.

![Offentlig slutpunkt för IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Innan du fortsätter, se till att följande förutsättningar är uppfyllda:

* Din IoT-hubb måste etableras med [hanterad tjänstidentitet](#create-an-iot-hub-with-managed-service-identity).

* IoT-hubben måste etableras i en av de [regioner som stöds.](#regional-availability-private-endpoints)

* Du har etablerat ett Azure VNET med ett undernät där den privata slutpunkten skapas. Mer information [finns i Skapa ett virtuellt nätverk med Azure CLI.](../virtual-network/quick-create-cli.md)

* För enheter som fungerar inuti lokala nätverk konfigurerar du [VPN (Virtual Private Network)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) eller [ExpressRoute-privat](https://azure.microsoft.com/services/expressroute/) peering i ditt Azure VNET.


### <a name="regional-availability-private-endpoints"></a>Regional tillgänglighet (privata slutpunkter)

Privata slutpunkter som stöds i IoT Hub har skapats i följande regioner:

* USA, östra

* USA, södra centrala

* USA, västra 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Konfigurera en privat slutpunkt för IoT Hub-inträngning

Så här konfigurerar du en privat slutpunkt:

1. Kör följande Azure CLI-kommando för att registrera om Azure IoT Hub-providern med din prenumeration:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Navigera till fliken **Privata slutpunktsanslutningar** i IoT Hub-portalen (den här fliken är endast tillgänglig **+** för i IoT-hubbar i de regioner som [stöds)](#regional-availability-private-endpoints)och klicka på tecknet för att lägga till en ny privat slutpunkt.

3. Ange prenumeration, resursgrupp, namn och region för att skapa den nya privata slutpunkten i (helst bör privat slutpunkt skapas i samma region som navet; se [avsnittet regional tillgänglighet](#regional-availability-private-endpoints) för mer information).

4. Klicka på **Nästa: Resurs**och ange prenumerationen för IoT Hub-resursen och välj **"Microsoft.Devices/IotHubs"** som resurstyp, IoT Hub-namnet som **resurs**och **iotHub** som målunderresurs.

5. Klicka på **Nästa: Konfiguration** och ange ditt virtuella nätverk och undernät för att skapa den privata slutpunkten i. Välj alternativet att integrera med Azure privat DNS-zon, om så önskas.

6. Klicka på **Nästa: Taggar**och ange eventuella taggar för din resurs.

7. Klicka på **Granska + skapa** om du vill skapa en privat slutpunktsresurs.


### <a name="pricing-private-endpoints"></a>Prissättning (privata slutpunkter)

Information om priser finns i [Azure Private Link-priser](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Egressanslutning från IoT Hub till andra Azure-resurser

IoT Hub behöver åtkomst till din Azure blob-lagring, händelsehubbar, servicebussresurser för [meddelanderoutning,](./iot-hub-devguide-messages-d2c.md) [filöverföring](./iot-hub-devguide-file-upload.md)och [massenhetsimport/export](./iot-hub-bulk-identity-mgmt.md), som vanligtvis sker över resursernas offentliga slutpunkt. I händelse av att du binder ditt lagringskonto, händelsehubbar eller servicebussresurs till ett VNET, blockerar den rekommenderade konfigurationen anslutningen till resursen som standard. Detta kommer därför att hindra IoT Hubs funktionalitet som kräver åtkomst till dessa resurser.

För att minska den här situationen måste du aktivera anslutning från din IoT Hub-resurs till ditt lagringskonto, händelsehubbar eller servicebussresurser via **Azures första part betrodda tjänster.**

Kraven är som följer:

* IoT-hubben måste etableras i en av de [regioner som stöds.](#regional-availability-trusted-microsoft-first-party-services)

* Din IoT Hub måste tilldelas en hanterad tjänstidentitet vid hubbetableringstid. Följ instruktioner om hur du [skapar ett nav med hanterad tjänstidentitet](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regional tillgänglighet (betrodda Microsoft-tjänster för första part)

Azure betrodda första parts tjänster undantag för att kringgå brandväggsbegränsningar till Azure-lagring, händelsehubbar och servicebussresurser stöds endast för IoT-hubbar i följande regioner:

* USA, östra

* USA, södra centrala

* USA, västra 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Prissättning (betrodda Microsoft-tjänster från första part)

Undantagsfunktionen betrodda Microsoft-tjänster för första parts tjänster är kostnadsfri i IoT Hubs i de [regioner som stöds](#regional-availability-trusted-microsoft-first-party-services). Avgifter för de etablerade lagringskontona, händelsehubbar eller servicebussresurserna gäller separat.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Skapa en IoT-hubb med hanterad tjänstidentitet

En hanterad tjänstidentitet kan tilldelas till hubben vid resursetableringstid (den här funktionen stöds för närvarande inte för befintliga hubbar), vilket kräver att IoT-hubben använder TLS 1.2 som minimiversion. För detta ändamål måste du använda ARM-resursmallen nedan:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

När du har ersatt `name`värdena `SKU.tier`för din resurs `location` `SKU.name` kan du använda Azure CLI för att distribuera resursen i en befintlig resursgrupp med hjälp av:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

När resursen har skapats kan du hämta den hanterade tjänstidentitet som tilldelats din hubb med Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

När IoT Hub med en hanterad tjänstidentitet har etablerats följer du motsvarande avsnitt för att ställa in routningsslutpunkter till [lagringskonton,](#egress-connectivity-to-storage-account-endpoints-for-routing) [händelsehubbar](#egress-connectivity-to-event-hubs-endpoints-for-routing)och [servicebussresurser,](#egress-connectivity-to-service-bus-endpoints-for-routing) eller för att konfigurera [filöverföring](#egress-connectivity-to-storage-accounts-for-file-upload) och [massenhetsimport/export](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport).


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Utgående anslutning till slutpunkter för lagringskonto för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägt lagringskonto. Om du vill att routningsfunktionen ska kunna komma åt ett lagringskonto medan brandväggsbegränsningar är på plats måste IoT-hubben ha en hanterad tjänstidentitet (se hur du [skapar en hubb med hanterad tjänstidentitet](#create-an-iot-hub-with-managed-service-identity)). När en hanterad tjänstidentitet har etablerats följer du stegen nedan för att ge RBAC-behörighet till hubbens resursidentitet att komma åt ditt lagringskonto.

1. Navigera till fliken **Åtkomstkontroll (IAM) i** Azure-portalen och klicka på **Lägg till** under avsnittet Lägg till **en rolltilldelning.**

2. Välj **Storage Blob Data Contributor** som **roll**, **Azure AD-användare, grupp eller tjänsthuvudnamn** som **Tilldela åtkomst till** och välj IoT Hub resursnamn i listrutan. Klicka på knappen **Spara**.

3. Navigera till fliken **Brandväggar och virtuella nätverk** i ditt lagringskonto och aktivera Alternativet **Tillåt åtkomst från valda nätverk.** Markera kryssrutan **Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot**under listan **Undantag** . Klicka på knappen **Spara**.

4. På ioT-hubbens resurssida navigerar du till fliken **Meddelanderoutning.**

5. Navigera till avsnittet **Anpassade slutpunkter** och klicka på **Lägg till**. Välj **Lagring** som slutpunktstyp.

6. På sidan som visas anger du ett namn för slutpunkten, väljer den behållare som du tänker använda i blob-lagringen, tillhandahåller kodning och filnamnsformat. Välj **System tilldelad** som **autentiseringstyp** till lagringsslutpunkten. Klicka på knappen **Skapa**.

Nu är din anpassade lagringsslutpunkt inställd på att använda hubbens systemtilldelade identitet och har behörighet att komma åt lagringsresursen trots brandväggsbegränsningar. Du kan nu använda den här slutpunkten för att ställa in en routningsregel.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Utgående anslutning till händelsehubbarslutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägt händelsehubbarnamnområde. Om du vill att routningsfunktionen ska kunna komma åt en händelsehubbarresurs när brandväggsbegränsningar finns på plats måste IoT Hub ha en hanterad tjänstidentitet (se hur du [skapar en hubb med hanterad tjänstidentitet](#create-an-iot-hub-with-managed-service-identity)). När en hanterad tjänstidentitet har etablerats följer du stegen nedan för att ge RBAC-behörighet till hubbens resursidentitet för att komma åt dina händelsehubbar.

1. I Azure-portalen navigerar du till fliken **IAM (Event** hubs Access Control) och klickar på **Lägg till** under avsnittet **Lägg till en rolltilldelning.**

2. Välj **Händelsehubbar dataavsändare** som **roll**, **Azure AD-användare, grupp eller tjänsthuvudnamn** som **Tilldela åtkomst till** och välj IoT Hubs resursnamn i listrutan. Klicka på knappen **Spara**.

3. Navigera till fliken **Brandväggar och virtuella nätverk** i händelsehubbar och aktivera Alternativet **Tillåt åtkomst från valda nätverk.** Markera kryssrutan **Tillåt betrodda Microsoft-tjänster att komma åt händelsehubbar**under listan **Undantag** . Klicka på knappen **Spara**.

4. På ioT-hubbens resurssida navigerar du till fliken **Meddelanderoutning.**

5. Navigera till avsnittet **Anpassade slutpunkter** och klicka på **Lägg till**. Välj **Händelsehubbar** som slutpunktstyp.

6. På sidan som visas anger du ett namn på slutpunkten, väljer namnområde och instans för händelsehubbar och klickar på knappen **Skapa.**

Nu är slutpunkten för dina anpassade händelsehubbar konfigurerad för att använda hubbens systemtilldelade identitet och har behörighet att komma åt din händelsehubbarresurs trots brandväggsbegränsningar. Du kan nu använda den här slutpunkten för att ställa in en routningsregel.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Utgående anslutning till servicebussslutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägt servicebussnamnområde. Om du vill att routningsfunktionen ska kunna komma åt en servicebussresurs när brandväggsbegränsningar är på plats måste IoT Hub ha en hanterad tjänstidentitet (se hur du [skapar ett nav med hanterad tjänstidentitet](#create-an-iot-hub-with-managed-service-identity)). När en hanterad tjänstidentitet har etablerats följer du stegen nedan för att ge RBAC-behörighet till hubbens resursidentitet att komma åt din servicebuss.

1. I Azure-portalen navigerar du till fliken **Åtkomstkontroll (IAM) till** tjänstbussen och klickar på **Lägg till** under avsnittet Lägg till **en rolltilldelning.**

2. Välj **Servicebussdataavsändare** som **roll**, **Azure AD-användare, grupp eller tjänsthuvudnamn** som **Tilldela åtkomst till** och välj IoT Hub resursnamn i listrutan. Klicka på knappen **Spara**.

3. Navigera till fliken **Brandväggar och virtuella nätverk** i servicebussen och aktivera Alternativet **Tillåt åtkomst från valda nätverk.** Markera kryssrutan Tillåt **betrodda Microsoft-tjänster att komma åt den här servicebussen**under listan **Undantag** . Klicka på knappen **Spara**.

4. På ioT-hubbens resurssida navigerar du till fliken **Meddelanderoutning.**

5. Navigera till avsnittet **Anpassade slutpunkter** och klicka på **Lägg till**. Välj **Servicebusskö** eller **Servicebussämne** (beroende på vad som är tillämpligt) som slutpunktstyp.

6. På sidan som visas anger du ett namn för slutpunkten, väljer servicebussens namnområde och kö eller ämne (beroende på vad som är tillämpligt). Klicka på knappen **Skapa**.

Nu är din anpassade servicebussslutpunkt inställd på att använda hubbens systemtilldelade identitet, och den har behörighet att komma åt din servicebussresurs trots brandväggsbegränsningar. Du kan nu använda den här slutpunkten för att ställa in en routningsregel.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Egressanslutning till lagringskonton för filöverföring

IoT Hubs filuppladdningsfunktion gör det möjligt för enheter att ladda upp filer till ett kundägt lagringskonto. För att filöverföringen ska fungera måste både enheter och IoT Hub ha anslutning till lagringskontot. Om brandväggsbegränsningar finns på lagringskontot måste dina enheter använda någon av det lagringskonto som stöds (inklusive [privata slutpunkter,](../private-link/create-private-endpoint-storage-portal.md) [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) eller [direkt brandväggskonfiguration)](../storage/common/storage-network-security.md)för att få anslutning. Om brandväggsbegränsningar finns på lagringskontot måste IoT Hub konfigureras för att komma åt lagringsresursen via undantaget betrodda Microsoft-tjänster. För detta ändamål måste din IoT Hub ha en hanterad tjänstidentitet (se hur du [skapar ett nav med hanterad tjänstidentitet](#create-an-iot-hub-with-managed-service-identity)). När en hanterad tjänstidentitet har etablerats följer du stegen nedan för att ge RBAC-behörighet till hubbens resursidentitet att komma åt ditt lagringskonto.

1. Navigera till fliken **Åtkomstkontroll (IAM) i** Azure-portalen och klicka på **Lägg till** under avsnittet Lägg till **en rolltilldelning.**

2. Välj **Storage Blob Data Contributor** som **roll**, **Azure AD-användare, grupp eller tjänsthuvudnamn** som **Tilldela åtkomst till** och välj IoT Hub resursnamn i listrutan. Klicka på knappen **Spara**.

3. Navigera till fliken **Brandväggar och virtuella nätverk** i ditt lagringskonto och aktivera Alternativet **Tillåt åtkomst från valda nätverk.** Markera kryssrutan **Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot**under listan **Undantag** . Klicka på knappen **Spara**.

4. På ioT-hubbens resurssida navigerar du till fliken **Filuppladdning.**

5. På sidan som visas väljer du den behållare som du tänker använda i blob-lagringen, konfigurerar **inställningarna för filmeddelanden**, **SAS TTL**, **Standard TTL** och **Maximalt leveransantal** som du vill. Välj **System tilldelad** som **autentiseringstyp** till lagringsslutpunkten. Klicka på knappen **Skapa**.

Nu är lagringsslutpunkten för filöverföring inställd på att använda hubbens systemtilldelade identitet, och den har behörighet att komma åt lagringsresursen trots brandväggsbegränsningar.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Egressanslutning till lagringskonton för import/export av bulkenheter

IoT Hub stöder funktionerna för att [importera/exportera](./iot-hub-bulk-identity-mgmt.md) enheters information i bulk från/till en lagringsblob som tillhandahålls av kunden. För att funktionen massimport/export ska fungera måste både enheter och IoT Hub ha anslutning till lagringskontot.

Den här funktionen kräver anslutning från IoT Hub till lagringskontot. För att komma åt en servicebussresurs när brandväggsbegränsningar är på plats måste IoT Hub ha en hanterad tjänstidentitet (se hur du [skapar ett nav med hanterad tjänstidentitet).](#create-an-iot-hub-with-managed-service-identity) När en hanterad tjänstidentitet har etablerats följer du stegen nedan för att ge RBAC-behörighet till hubbens resursidentitet att komma åt din servicebuss.

1. Navigera till fliken **Åtkomstkontroll (IAM) i** Azure-portalen och klicka på **Lägg till** under avsnittet Lägg till **en rolltilldelning.**

2. Välj **Storage Blob Data Contributor** som **roll**, **Azure AD-användare, grupp eller tjänsthuvudnamn** som **Tilldela åtkomst till** och välj IoT Hub resursnamn i listrutan. Klicka på knappen **Spara**.

3. Navigera till fliken **Brandväggar och virtuella nätverk** i ditt lagringskonto och aktivera Alternativet **Tillåt åtkomst från valda nätverk.** Markera kryssrutan **Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot**under listan **Undantag** . Klicka på knappen **Spara**.

Du kan nu använda Azure IoT REST API: s för [att skapa import exportjobb](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) för information om hur du använder massimport/ export funktionalitet. Observera att du måste `storageAuthenticationType="identityBased"` ange i din `inputBlobContainerUri="https://..."` begäran `outputBlobContainerUri="https://..."` kropp och använda och som in- och utdata-URL:er för ditt lagringskonto, respektive.


Azure IoT Hub SDK stöder även den här funktionen i tjänstklientens registerhanterare. Följande kodavsnitt visar hur du initierar ett importjobb eller exporterar jobb med hjälp av C# SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Så här använder du den här regionbegränsade versionen av Azure IoT-SDK:er med stöd för virtuellt nätverk för C#, Java och Node.js:

1. Skapa en miljövariabel med `EnableStorageIdentity` `1`namnet och ange dess värde till .

2. Ladda ner SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
För Python laddar du ned vår begränsade version från GitHub.

1. Navigera till [GitHub-utgivningssidan](https://aka.ms/vnetpythonsdk).

2. Hämta följande fil, som du hittar längst ned på utgivningssidan under rubriken **tillgångar**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Öppna en terminal och navigera till mappen med den nedladdade filen.

4. Kör följande kommando för att installera Python Service SDK med stöd för virtuella nätverk:
    > pip installera ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan om du vill veta mer om IoT Hub-funktioner:

* [Meddelanderoutning](./iot-hub-devguide-messages-d2c.md)
* [Ladda upp filer](./iot-hub-devguide-file-upload.md)
* [Import/export av bulkenhet](./iot-hub-bulk-identity-mgmt.md) 
