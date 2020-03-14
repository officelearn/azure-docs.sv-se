---
title: Azure IoT Hub stöd för virtuella nätverk
description: Så här använder du anslutnings mönster för virtuella nätverk med IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 1b250bee302cb305ee613010238283ceac4014ed
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375088"
---
# <a name="iot-hub-support-for-virtual-networks"></a>IoT Hub stöd för virtuella nätverk

Den här artikeln beskriver det virtuella nätverkets anslutnings mönster och beskriver hur du konfigurerar en privat anslutning till en IoT-hubb via ett kundägda Azure VNET.

> [!NOTE]
> De IoT Hub-funktioner som beskrivs i den här artikeln är för närvarande tillgängliga för IoT Hub som skapats i följande regioner: östra USA, södra centrala USA och västra USA 2.


## <a name="introduction"></a>Introduktion

Som standard mappas IoT Hub-värdnamn till en offentlig slut punkt med en offentligt dirigerad IP-adress via Internet. Som du ser i bilden nedan delas den här IoT Hub offentliga slut punkten mellan nav som ägs av olika kunder och kan nås av IoT-enheter över WAN-nätverk såväl som lokala nätverk.

Flera IoT Hub funktioner, bland annat [meddelanderoutning](./iot-hub-devguide-messages-d2c.md), [fil överföring](./iot-hub-devguide-file-upload.md)och [Mass import/export](./iot-hub-bulk-identity-mgmt.md) kräver anslutning från IoT Hub till en Azure-resurs som ägs av en kund via den offentliga slut punkten. Som illustreras nedan utgör dessa anslutnings vägar kollektivt den utgående trafiken från IoT Hub till kund resurser.
![IoT Hub offentlig slut punkt](./media/virtual-network-support/public-endpoint.png)


Av flera skäl kan kunderna vilja begränsa anslutningen till sina Azure-resurser (inklusive IoT Hub) via ett virtuellt nätverk som de äger och använder. Dessa orsaker är:

* Introducera ytterligare säkerhets lager via isolering på nätverks nivå för din IoT-hubb genom att förhindra anslutningens exponering för navet via det offentliga Internet.

* Att aktivera en privat anslutning från dina lokala nätverks resurser och se till att dina data och trafik skickas direkt till Azure stamnät nätverket.

* Förhindra exfiltrering attacker från känsliga lokala nätverk. 

* Följande etablerade Azure-wide-anslutnings mönster använder [privata slut punkter](../private-link/private-endpoint-overview.md).


I den här artikeln beskrivs hur du uppnår dessa mål med hjälp av [privata slut punkter](../private-link/private-endpoint-overview.md) för ingångs anslutning till IoT Hub, med hjälp av Azure Trusted First part Services-undantag för utgående anslutning från IoT Hub till andra Azure-resurser.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Ingress-anslutning till IoT Hub med hjälp av privata slut punkter

En privat slut punkt är en privat IP-adress som tilldelas i ett kundägda VNET via vilken en Azure-resurs kan kontaktas. Genom att ha en privat slut punkt för din IoT-hubb kommer du att kunna tillåta tjänster som körs i ditt VNET för att uppnå IoT Hub utan att trafik måste skickas till IoT Hub offentliga slut punkter. På samma sätt kan enheter som arbetar i din lokala dator använda [virtuella privata nätverk (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) eller [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privata peering för att få anslutning till ditt VNet i Azure och därefter till din IoT Hub (via dess privata slut punkt). Därför kan kunder som vill begränsa anslutningen till sina offentliga slut punkter för IoT Hub (eller eventuellt helt blockera det) uppnå målet genom att använda [IoT Hub brand Väggs regler](./iot-hub-ip-filtering.md) och samtidigt behålla anslutningen till hubben med hjälp av den privata slut punkten.

> [!NOTE]
> Huvud fokus för den här installationen är för enheter i ett lokalt nätverk. Den här installationen rekommenderas inte för enheter som distribueras i ett WAN-nätverk.

![IoT Hub offentlig slut punkt](./media/virtual-network-support/virtual-network-ingress.png)

Innan du fortsätter kontrollerar du att följande krav uppfylls:

* IoT-hubben måste vara etablerad i någon av de [regioner som stöds](#regional-availability-private-endpoints).

* Du har skapat ett Azure VNET med ett undernät där den privata slut punkten ska skapas. Mer information finns i [skapa ett virtuellt nätverk med Azure CLI](../virtual-network/quick-create-cli.md) .

* För enheter som arbetar i lokala nätverk ställer du in [VPN (virtuellt privat nätverk)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) eller [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privat peering i ditt Azure VNet.


### <a name="regional-availability-private-endpoints"></a>Regional tillgänglighet (privata slut punkter)

Privata slut punkter som stöds i IoT Hub har skapats i följande regioner:

* USA, östra

* USA, södra centrala

* USA, västra 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Konfigurera en privat slut punkt för IoT Hub ingress

Följ dessa steg om du vill konfigurera en privat slut punkt:

1. Kör följande Azure CLI-kommando för att registrera Azure IoT Hub-providern igen med din prenumeration:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Gå till fliken **anslutningar för privata slut punkter** på din IoT Hub portal (den här fliken är bara tillgänglig för i IoT-hubbar i de [regioner som stöds](#regional-availability-private-endpoints)) och klicka på **+** signera för att lägga till en ny privat slut punkt.

3. Ange prenumeration, resurs grupp, namn och region för att skapa den nya privata slut punkten i (vi rekommenderar att privat slut punkt skapas i samma region som hubben. mer information finns i [avsnittet om regional tillgänglighet](#regional-availability-private-endpoints) ).

4. Klicka på **Nästa: resurs**och ange prenumerationen för din IoT Hub-resurs och välj **"Microsoft. Devices/IotHubs"** som resurs typ, ditt IoT Hub namn som **resurs**och **iotHub** som mål under resurs.

5. Klicka på **Nästa: konfiguration** och ange ditt virtuella nätverk och undernät för att skapa den privata slut punkten i. Välj alternativet att integrera med Azures privata DNS-zon, om så önskas.

6. Klicka på **Nästa: Taggar**och om du vill kan du även ange taggar för resursen.

7. Klicka på **Granska + skapa** för att skapa din privata slut punkts resurs.


### <a name="pricing-private-endpoints"></a>Priser (privata slut punkter)

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Utgående anslutning från IoT Hub till andra Azure-resurser

IoT Hub behöver åtkomst till Azure Blob Storage, Event Hub, Service Bus-resurser för [meddelanderoutning](./iot-hub-devguide-messages-d2c.md), [fil uppladdning](./iot-hub-devguide-file-upload.md)och [import/export av Mass enheter](./iot-hub-bulk-identity-mgmt.md), som vanligt vis äger rum över resursens offentliga slut punkt. I händelse av att du binder ditt lagrings konto, händelse hubbar eller Service Bus-resurs till ett virtuellt nätverk blockerar den välinformerade konfigurationen anslutningen till resursen som standard. Därför hindrar detta att IoT Hubs funktioner som kräver åtkomst till dessa resurser.

För att minska den här situationen måste du aktivera anslutning från din IoT Hub-resurs till ditt lagrings konto, händelse hubbar eller Service Bus-resurser via **Azure First part Trust Services** -alternativet.

Kraven är följande:

* IoT-hubben måste vara etablerad i någon av de [regioner som stöds](#regional-availability-trusted-microsoft-first-party-services).

* Din IoT Hub måste tilldelas en hanterad tjänst identitet vid hubbens etablerings tid. Följ anvisningarna om hur du [skapar en hubb med hanterad tjänst identitet](#create-a-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regional tillgänglighet (betrodda Microsoft-tjänster från första part)

Undantag för Azure-betrodda första part tjänster för att kringgå brand Väggs begränsningar för Azure Storage, Event Hub och Service Bus-resurser stöds bara för IoT-hubbar i följande regioner:

* USA, östra

* USA, södra centrala

* USA, västra 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Priser (betrodda Microsoft-tjänster från första part)

Den betrodda Microsoft-tjänsten för första parts tjänster är kostnads fri i IoT Hub i de [regioner som stöds](#regional-availability-trusted-microsoft-first-party-services). Avgifter för etablerade lagrings konton, händelse nav eller Service Bus-resurser gäller separat.


### <a name="create-a-hub-with-managed-service-identity"></a>Skapa en hubb med hanterad tjänst identitet

En hanterad tjänst identitet kan tilldelas till navet vid resurs etablerings tiden (den här funktionen stöds för närvarande inte för befintliga hubbar). För det här ändamålet måste du använda resurs mal len ARM nedan:

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
            "identity": { "type": "SystemAssigned" },
            "properties": { "minTlsVersion": "1.2" },
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
            "dependsOn": [ "<provide-a-valid-resource-name>" ],
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
                            "identity": { "type": "SystemAssigned" },
                            "properties": { "minTlsVersion": "1.2" },
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

När du har angett värdena för din resurs `name``location`, `SKU.name` och `SKU.tier`, kan du använda Azure CLI för att distribuera resursen i en befintlig resurs grupp med hjälp av:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

När resursen har skapats kan du hämta den hanterade tjänst identiteten som tilldelats navet med Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

När IoT Hub med en hanterad tjänst identitet har tillhandahållits följer du motsvarande avsnitt för att konfigurera routnings slut punkter för [lagrings konton](#egress-connectivity-to-storage-account-endpoints-for-routing), [händelse nav](#egress-connectivity-to-event-hubs-endpoints-for-routing)och [Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing) -resurser eller för att konfigurera [import/export](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)av [fil överföring](#egress-connectivity-to-storage-accounts-for-file-upload) och Mass enhet.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Utgående anslutning till lagrings konto slut punkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägda lagrings konto. För att routnings funktionen ska kunna komma åt ett lagrings konto medan brand Väggs begränsningarna är på plats måste IoT Hub ha en hanterad tjänst identitet (se så här [skapar du en hubb med hanterad tjänst identitet](#create-a-hub-with-managed-service-identity)). När en hanterad tjänst identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för åtkomst till ditt lagrings konto.

1. I Azure Portal navigerar du till ditt lagrings kontos **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Storage BLOB data-deltagare** som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja din IoT Hub resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i ditt lagrings konto och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **meddelanderoutning** .

5. Gå till avsnittet **anpassade slut punkter** och klicka på **Lägg till**. Välj **lagring** som typ av slut punkt.

6. På sidan som visas anger du ett namn för din slut punkt, väljer den behållare som du vill använda i blob-lagringen, ange kodning och fil namns format. Välj **system tilldelat** som **Autentiseringstyp** till lagrings slut punkten. Klicka på knappen **Skapa**.

Nu är din anpassade lagrings slut punkt inställd på att använda navets tilldelade identitet och har behörighet att komma åt lagrings resursen trots dess brand Väggs begränsningar. Du kan nu använda den här slut punkten för att ställa in en regel för routning.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Utgående anslutning till Event Hub-slutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till en kundägda Event Hub-namnrymd. Om du vill tillåta att routningsfunktioner får åtkomst till en händelse för händelse nav medan brand Väggs begränsningarna är på plats måste IoT Hub ha en hanterad tjänst identitet (se så här [skapar du en hubb med hanterad tjänst identitet](#create-a-hub-with-managed-service-identity)). När en hanterad tjänst identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att få åtkomst till dina händelse nav.

1. I Azure Portal navigerar du till fliken för Event Hub- **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Event Hubs data avsändare** som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja din IoT Hubs resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i Event Hub och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till Event Hub**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **meddelanderoutning** .

5. Gå till avsnittet **anpassade slut punkter** och klicka på **Lägg till**. Välj **händelse nav** som typ av slut punkt.

6. Ange ett namn för din slut punkt på sidan som visas, Välj namn området för Event Hub och instansen och klicka på knappen **skapa** .

Nu konfigureras slut punkten för anpassade händelse nav till att använda navets tilldelade identitet och har behörighet att komma åt dina Event Hub-resurser trots dess brand Väggs begränsningar. Du kan nu använda den här slut punkten för att ställa in en regel för routning.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Utgående anslutning till Service Bus-slutpunkter för routning

IoT Hub kan konfigureras för att dirigera meddelanden till ett kundägda Service Bus-namnområde. För att routnings funktionen ska kunna komma åt en Service Bus-resurs medan brand Väggs begränsningarna är på plats måste IoT Hub ha en hanterad tjänst identitet (se så här [skapar du en hubb med hanterad tjänst identitet](#create-a-hub-with-managed-service-identity)). När en hanterad tjänst identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att få åtkomst till din Service Bus.

1. I Azure Portal navigerar du till fliken åtkomst kontroll för Service Bus **(IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Service Bus-avsändare** som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja din IoT Hubs resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i Service Bus och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan för **Tillåt att betrodda Microsoft-tjänster har åtkomst till den här Service Bus-tjänsten**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **meddelanderoutning** .

5. Gå till avsnittet **anpassade slut punkter** och klicka på **Lägg till**. Välj **Service Bus-kö** eller **Service Bus-ämne** (i förekommande fall) som typ av slut punkt.

6. På sidan som visas anger du ett namn för din slut punkt, väljer din Service Bus-namnrymd och kö eller ämne (om det är tillämpligt). Klicka på knappen **Skapa**.

Nu är din anpassade Service Bus-slutpunkt konfigurerad att använda navets tilldelade identitet och har behörighet att komma åt din Service Bus-resurs trots dess brand Väggs begränsningar. Du kan nu använda den här slut punkten för att ställa in en regel för routning.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Utgående anslutning till lagrings konton för fil uppladdning

Med IoT Hub fil överförings funktionen kan enheter Ladda upp filer till ett kundägda lagrings konto. För att tillåta fil uppladdning att fungera måste både enheter och IoT Hub ha anslutning till lagrings kontot. Om brand Väggs begränsningar finns på lagrings kontot, måste enheterna använda något av de lagrings konto funktioner som stöds (inklusive [privata slut punkter](../private-link/create-private-endpoint-storage-portal.md), [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) eller [konfiguration av direkt brand vägg](../storage/common/storage-network-security.md)) för att få anslutning. Om brand Väggs begränsningarna finns på lagrings kontot måste IoT Hub konfigureras för åtkomst till lagrings resursen via det betrodda Microsoft Services-undantaget. För det här ändamålet måste IoT Hub ha en hanterad tjänst identitet (se så här [skapar du en hubb med hanterad tjänst identitet](#create-a-hub-with-managed-service-identity)). När en hanterad tjänst identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för åtkomst till ditt lagrings konto.

1. I Azure Portal navigerar du till ditt lagrings kontos **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Storage BLOB data-deltagare** som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja din IoT Hub resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i ditt lagrings konto och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot**. Klicka på knappen **Spara**.

4. På resurs sidan för IoT Hub navigerar du till fliken **fil uppladdning** .

5. På sidan som visas väljer du den behållare som du vill använda i blob-lagringen, konfigurerar **inställningarna för fil meddelanden**, **SAS TTL**, standard- **TTL** och **maximalt antal leveranser** som önskas. Välj **system tilldelat** som **Autentiseringstyp** till lagrings slut punkten. Klicka på knappen **Skapa**.

Nu har din lagrings slut punkt för fil uppladdning kon figurer ATS för att använda navets tilldelade identitet och har behörighet att komma åt lagrings resursen trots dess brand Väggs begränsningar.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Utgående anslutning till lagrings konton för import/export av Mass enhet

IoT Hub stöder funktionerna för att [Importera/exportera](./iot-hub-bulk-identity-mgmt.md) enheters information i bulk från/till en lagrings-BLOB för kunden. För att tillåta Mass import/export-funktionen att fungera måste både enheter och IoT Hub ha anslutning till lagrings kontot.

Den här funktionen kräver anslutning från IoT Hub till lagrings kontot. För att få åtkomst till en Service Bus-resurs medan brand Väggs begränsningarna är på plats måste IoT Hub ha en hanterad tjänst identitet (se så här [skapar du en hubb med hanterad tjänst identitet](#create-a-hub-with-managed-service-identity)). När en hanterad tjänst identitet har tillhandahållits följer du stegen nedan för att ge RBAC-behörighet till navets resurs identitet för att få åtkomst till din Service Bus.

1. I Azure Portal navigerar du till ditt lagrings kontos **åtkomst kontroll (IAM)** och klickar på **Lägg till** under avsnittet **Lägg till en roll tilldelning** .

2. Välj **Storage BLOB data-deltagare** som **roll**, **Azure AD-användare, grupp eller tjänstens huvud** namn som **att tilldela åtkomst till** och välja din IoT Hub resurs namn i list rutan. Klicka på knappen **Spara**.

3. Gå till fliken **brand väggar och virtuella nätverk** i ditt lagrings konto och aktivera alternativet **Tillåt åtkomst från valda nätverk** . Under **undantags** listan markerar du kryss rutan **Tillåt att betrodda Microsoft-tjänster får åtkomst till det här lagrings kontot**. Klicka på knappen **Spara**.

Nu kan du använda Azure IoT-REST API för att [skapa import-och export jobb](https://docs.microsoft.com/rest/api/iothub/jobclient/getimportexportjobs) för information om hur du använder Mass import/export-funktionen. Observera att du måste ange `storageAuthenticationType="identityBased"` i din begär ande text och använda `inputBlobContainerUri="https://..."` och `outputBlobContainerUri="https://..."` som indata-och utdata-URL för ditt lagrings konto.


Azure IoT Hub SDK har också stöd för den här funktionen i tjänst klientens register hanterare. Följande kodfragment visar hur du startar ett import jobb eller ett export jobb i med hjälp av C# SDK.

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


Om du vill använda den här regionen – begränsad version av Azure IoT SDK: er C#med VNet-stöd för, Java och Node. js:

1. Skapa en miljö variabel med namnet `EnableStorageIdentity` och ange dess värde som `1`.

2. Hämta SDK:
    - > [Java](https://aka.ms/vnetjavasdk)
    - > [C#](https://aka.ms/vnetcsharsdk)
    - > [Node.js](https://aka.ms/vnetnodesdk)
 
För python laddar du ned vår begränsade version från GitHub.

1. Gå till [sidan med GitHub-versionen](https://aka.ms/vnetpythonsdk).

2. Ladda ned följande fil, som du hittar längst ned på sidan version under rubriken namngivna **till gångar**.
    > *azure_iot_hub-2.2.0. Limited-PY2. py3-none-any. WHL*

3. Öppna en Terminal och navigera till mappen med den nedladdade filen.

4. Kör följande kommando för att installera python service SDK med stöd för virtuella nätverk:
    > PIP install./azure_iot_hub-2.2.0. Limited-PY2. py3-none-any. WHL


## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan för att lära dig mer om IoT Hub funktioner:

* [Meddelanderoutning](./iot-hub-devguide-messages-d2c.md)
* [Fil uppladdning](./iot-hub-devguide-file-upload.md)
* [Import/export av Mass enhet](./iot-hub-bulk-identity-mgmt.md) 