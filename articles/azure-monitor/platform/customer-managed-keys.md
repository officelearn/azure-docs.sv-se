---
title: Azure Monitor kundhanterad nyckelkonfiguration
description: Information och steg för att konfigurera CUSTOMER-Managed Key (CMK) för att kryptera data i dina Log Analytics-arbetsytor med hjälp av en Azure Key Vault-nyckel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: dbd217c7135172c52a5ec7459930977960c452aa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260877"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor kundhanterad nyckelkonfiguration 

Den här artikeln innehåller bakgrundsinformation och steg för att konfigurera CMK (Customer-Managed Keys) för komponenterna Log Analytics och Application Insights. När alla data som skickas till dina arbetsytor eller komponenter har konfigurerats krypteras de med nyckeln Azure Key Vault.

Vi rekommenderar att du granskar [begränsningar och begränsningar](#limitations-and-constraints) nedan innan konfigurationen.

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- Azure Monitor CMK är en tidig åtkomstfunktion och aktiverad för registrerade prenumerationer.

- CMK-distributionen som beskrivs i den här artikeln levereras i produktionskvalitet och stöds som sådan även om det är en tidig åtkomstfunktion.

- CMK-funktionen levereras på ett dedikerat datalagringskluster, som är ett ADX-kluster (Azure Data Explorer) och som är lämpligt för kunder som skickar 1 TB per dag eller mer. 

- CMK-prismodellen är inte tillgänglig för tillfället och den beskrivs inte i den här artikeln. En prismodell för dedikerat ADX-kluster förväntas under det andra kvartalet av kalenderåret (CY) 2020 och kommer att gälla för alla befintliga CMK-distributioner.

- I den här artikeln beskrivs CMK-konfigurationen för Log Analytics-arbetsytor. CMK för Application Insights-komponenter stöds också med den här artikeln medan skillnader visas i tillägget.

> [!NOTE]
> Log Analytics och Application Insights använder samma datalagringsplattform och frågemotor.
> Vi sammanför dessa två butiker via integrering av Application Insights i Log Analytics för att skapa ett enda enhetligt loggar-arkiv under Azure Monitor. Ändringen är planerad till det andra kvartalet kalenderåret 2020. Om du inte behöver distribuera CMK för dina Application Insights-data då rekommenderar vi att du väntar på att konsolideringen ska slutföras eftersom sådana distributioner kommer att störas av konsolideringen och du måste konfigurera om CMK efter migreringen till Log Analytics-arbetsytan. Minimumet på 1 TB per dag gäller på klusternivå och tills konsolideringen är klar under andra kvartalet kräver Application Insights och Log Analytics separata kluster.

## <a name="customer-managed-key-cmk-overview"></a>Översikt över kundhanterad nyckel (CMK)

[Kryptering på vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) är ett vanligt integritets- och säkerhetskrav i organisationer. Du kan låta Azure hantera kryptering helt i vila, medan du har olika alternativ för att noggrant hantera krypterings- eller krypteringsnycklar.

Azure Monitor-datalagringen säkerställer att alla data krypteras i vila med Hjälp av Azure-hanterade nycklar medan de lagras i Azure Storage. Azure Monitor tillhandahåller också ett alternativ för datakryptering med din egen nyckel som lagras i ditt [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), som används med hjälp av systemtilldelad [autentisering av hanterade identiteter.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Den här nyckeln kan vara antingen [programvara eller hårdvara-HSM skyddad](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor-användningen av kryptering är identisk med hur [Azure Storage-kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) fungerar.

Frekvensen som Azure Monitor Storage har åtkomst till Key Vault för inrbryt- och uppbrytaråtgärder är mellan 6 och 60 sekunder.Azure Monitor Storage respekterar alltid ändringar i nyckelbehörigheter inom en timme.

Intas data under de senaste 14 dagarna hålls också i hot-cache (SSD-backed) för effektiv frågemotordrift. Dessa data förblir krypterade med Microsoft-nycklar oavsett CMK-konfiguration, men vi arbetar för att få SSD-kodat med CMK under första halvåret 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Så här fungerar CMK i Azure Monitor

Azure Monitor utnyttjar systemtilldelade hanterade identitet för att bevilja åtkomst till ditt Azure Key Vault.Systemtilldelade hanterade identitet kan bara associeras med en enda Azure-resurs. Identiteten för Azure Monitor data-store (ADX-kluster) stöds på klusternivå och detta dikterar att CMK-funktionen levereras på ett dedikerat ADX-kluster. För att stödja CMK på flera arbetsytor fungerar en ny Log Analytics-resurs (*Cluster*) som en mellanliggande identitetsanslutning mellan Key Vault och dina Log Analytics-arbetsytor. Det här konceptet överensstämmer med den systemtilldelade identitetsbegränsningen och identiteten upprätthålls mellan ADX-klustret och *log analytics-klusterresursen,* medan data för alla associerade arbetsytor skyddas med nyckeln Key Vault. Adx-klusterlagringen underlag använder\'den hanterade identitet som är associerad med *klusterresursen* för att autentisera och komma åt ditt Azure Key Vault via Azure Active Directory.

![CMK Översikt](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Kundens nyckelvalv.
2.    Kundens Log Analytics *Cluster-resurs* som har hanterad identitet med behörighet till Key Vault – Identiteten stöds på ADX-klusternivå (Data Store).
3.    Azure Monitor-dedikerat ADX-kluster.
4.    Kundens arbetsytor som är associerade till *klusterresurs* för CMK-kryptering.

## <a name="encryption-keys-management"></a>Hantering av krypteringsnycklar

Det finns 3 typer av nycklar som är involverade i lagringsdatakryptering:

- **KEK** - Nyckelkrypteringsnyckel (CMK)
- **AEK** - Krypteringsnyckel för konton
- **DEK** - Data krypteringsnyckel

Följande regler gäller:

- ADX-lagringskontona genererar unik krypteringsnyckel för varje lagringskonto, som kallas AEK.

- AEK används för att härleda DEK:er, som är de nycklar som används för att kryptera varje datablock som skrivs till disk.

- När du konfigurerar nyckeln i Key Vault och refererar till den i *klusterresursen* skickar Azure Storage begäranden till ditt Azure Key Vault för att radbryta och packa upp AEK för att utföra datakrypterings- och dekrypteringsåtgärder.

- Kek lämnar aldrig key vault och när det gäller en HSM-nyckel lämnar den aldrig maskinvaran.

- Azure Storage använder den hanterade identitet som är associerad med *klusterresursen* för att autentisera och komma åt Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>CMK-etableringsförfarande

För CMK-konfiguration för Application Insights följer du tilläggsinnehållet för steg 3 och 6.

1. Prenumeration vitlista - detta krävs för denna tidig tillgång funktionen
2. Skapa Azure Key Vault och lagra nyckel
3. Skapa en *klusterresurs*
4. Adx-kluster (Azure Monitor data-store)
5. Bevilja behörigheter till key vault
6. Associera log analytics-arbetsytor

Proceduren stöds inte i användargränssnittet för närvarande och etableringsprocessen utförs via REST API.

> [!IMPORTANT]
> Alla API-begäranden måste innehålla en innehavarauktoriseringstoken i begäranhuvudet.

Ett exempel:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Där *eyJ0eXAiO....* representerar den fullständiga auktoriseringstoken. 

Du kan hämta token med någon av dessa metoder:

1. Använd metoden [Appregistreringar.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
2. I Azure-portalen
    1. Navigera till Azure-portalen i "utvecklarverktyg" (F12)
    1. Leta efter auktoriseringssträng under "Begär rubriker" i en av instanserna "batch?api-version". Det ser ut som: "tillstånd: Bearer eyJ0eXAiO ....". 
    1. Kopiera och lägg till det i ditt API-anrop enligt exemplen nedan.
3. Navigera till Azure REST-dokumentationswebbplatsen. Tryck på "Prova" på alla API och kopiera Innehavartoken.

### <a name="asynchronous-operations-and-status-check"></a>Asynkrona åtgärder och statuskontroll

Vissa av åtgärderna i den här konfigurationsproceduren körs asynkront eftersom de inte kan slutföras snabbt. Svaret för asynkron åtgärd returnerar inledningsvis en HTTP-statuskod 200 (OK) och ett huvud med egenskapen *Azure-AsyncOperation* när den godkänns:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Du kan kontrollera status för den asynkrona åtgärden genom att skicka en *GET-begäran till azure-AsyncOperation-huvudvärdet:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Svaret innehåller information om åtgärden och dess *status*. Det kan vara något av följande:

Operationen pågår
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

Åtgärden är slutförd
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Åtgärden misslyckades
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="subscription-whitelisting"></a>Vitlistning av prenumeration

CMK-funktionen är en funktion för tidig åtkomst. Prenumerationerna där du planerar att skapa *klusterresurser* måste vitlistas i förväg av Azure-produktgruppen. Använd dina kontakter till Microsoft för att ange dina prenumerations-ID:er.

> [!IMPORTANT]
> CMK-kapaciteten är regional. Dina Azure Key *Vault,Klusterresurs* och associerade Log Analytics-arbetsytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

### <a name="storing-encryption-key-kek"></a>Lagra krypteringsnyckel (KEK)

Skapa eller använd ett Azure Key Vault som du redan måste generera eller importera en nyckel som ska användas för datakryptering. Azure Key Vault måste konfigureras som återställningsbara för att skydda din nyckel och åtkomsten till dina data i Azure Monitor. Du kan verifiera den här konfigurationen under egenskaper i Key Vault, både *Mjukt borttagnings-* och *rensningsskydd* ska aktiveras.

![Inställningar för mjuk borttagning och rensning av skydd](media/customer-managed-keys/soft-purge-protection.png)

Dessa inställningar är tillgängliga via CLI och PowerShell:
- [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Rensa skydd](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) vakter mot kraft radering av den hemliga / valv även efter mjuk bort

### <a name="create-cluster-resource"></a>Skapa *klusterresurs*

Den här resursen används som en mellanliggande identitetsanslutning mellan Key Vault och dina Log Analytics-arbetsytor. När du har fått en bekräftelse på att dina prenumerationer har vitlistats skapar du en Log Analytics *Cluster-resurs* i den region där dina arbetsytor finns. Application Insights och Log *Cluster* Analytics kräver separata klusterresursertyper. Typ av *klusterresurs* definieras vid skapandetid genom att ange *egenskapen clusterType* till antingen *LogAnalytics*eller *ApplicationInsights*. Klusterresurstypen kan inte ändras efter.

För CMK-konfiguration för Application Insights följer du tilläggsinnehållet.

Du måste ange kapacitetsreservationsnivån (sku) när du skapar en *klusterresurs.* Kapacitetsreserveringsnivån kan ligga i intervallet 1 000 till 2 000 GB per dag och du kan uppdatera den i steg om 100 senare. Om du behöver kapacitetsreserveringsnivå som är högre än 2 000 GB per dag kontaktar du din Microsoft-kontakt för att aktivera den. Den här egenskapen påverkar inte faktureringen för tillfället – när prismodellen för dedikerat kluster har introducerats gäller fakturering för alla befintliga CMK-distributioner.

**Skapa**

Den här Resource Manager-begäran är asynkron åtgärd.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
Identiteten tilldelas *klusterresursen* när den skapas.

**Svar**

200 OK och header.
Under funktionens tidiga åtkomstperiod etableras ADX-klustret manuellt. Det tar visserligen att etablera det underliggande ADX-klustret ett tag att slutföra, men du kan kontrollera etableringstillståndet på två sätt:
1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [statuskontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *klusterresursen* och titta på *etableringstatsvärdet.* Det är *etableringKonto vid* etablering och lyckades när den har *slutförts.*

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Adx-kluster (Azure Monitor data-store)

Under funktionens tidiga åtkomstperiod etableras ADX-klustret manuellt av produktteamet när de föregående stegen har slutförts. Använd Microsoft-kanalen för det *Cluster* här steget och ange klusterresurssvaret. 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Kopiera och spara svaret eftersom du behöver informationen i nästa steg.

**Svar**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

GUID för "principalId" genereras av den *Cluster* hanterade identitetstjänsten för klusterresursen.

### <a name="grant-key-vault-permissions"></a>Bevilja behörigheter för nyckelvalv

Uppdatera key vault med en ny åtkomstprincip som ger behörighet till *klusterresursen.* Dessa behörigheter används av underlag Azure Monitor Storage för datakryptering. Öppna key vault i Azure-portalen och klicka på "Åtkomstprinciper" och sedan "+ Lägg till åtkomstprincip" för att skapa en princip med följande inställningar:

- Viktiga behörigheter: välj behörigheterna Hämta, Radbryt nyckel och Ta bort nyckel.
- Välj huvudnamn: Ange huvud-ID-värdet som returnerades i svaret i föregående steg.

![bevilja behörigheter för Nyckelvalv](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

*Behörigheten Hämta* krävs för att verifiera att ditt Key Vault har konfigurerats som återställningsbart för att skydda din nyckel och åtkomsten till dina Azure Monitor-data.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Uppdatera klusterresurs med information om nyckelidentifierare

Det här steget utförs under de första och framtida nyckelversionsuppdateringarna i key vault.This step performed during initial and in future key version updates in your Key Vault. Den informerar Azure Monitor Storage om nyckelversionen som ska användas för datakryptering. När den uppdateras används den nya nyckeln för att figursluta och packa upp till lagringsnyckeln (AEK).

Om du vill uppdatera *klusterresursen* med information om key *vault-nyckelinformation* väljer du den aktuella versionen av din nyckel i Azure Key Vault för att få information om nyckelidentifierare.

![Bevilja behörigheter för nyckelvalv](media/customer-managed-keys/key-identifier-8bit.png)

Uppdatera *klusterresursen* KeyVaultProperties med information om nyckelidentifierare.

**Uppdatera**

Den här Resource Manager-begäran är asynkron åtgärd.

> [!Warning]
> Du måste ange en hel *brödtext* i klusterresursuppdatering som innehåller *identitet*, *sku*, *KeyVaultProperties* och *plats*. Om du saknar *keyVaultProperties-informationen* tas nyckelidentifieraren bort från *klusterresursen* och [orsaka nyckelåterkallelse](#cmk-kek-revocation).

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" innehåller nyckelidentifieraren För nyckelvalvet.

**Svar**

200 OK och header.
Det tar att sprida nyckelidentifieraren några minuter att slutföra. Du kan kontrollera etableringstillståndet på två sätt:
1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [statuskontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *klusterresursen* och titta på egenskaperna *KeyVaultProperties.* Din nyligen uppdaterade nyckelidentifierare bör returneras i svaret.

Ett svar på GET-begäran på *klusterresursen* ska se ut så här när uppdateringen av nyckelidentifieraren är klar:

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Arbetsyteasasociation till *klusterresurs*
För CMK-konfiguration för Application Insights följer du tilläggsinnehållet för det här steget.

Du måste ha "skriv"-behörigheter till både arbetsytan och *klusterresursen* för att utföra den här åtgärden, vilket inkluderar följande åtgärder:

- På arbetsytan: Microsoft.OperationalInsights/workspaces/write
- I *klusterresurs:* Microsoft.OperationalInsights/kluster/skrivning

> [!IMPORTANT]
> Det här steget bör endast utföras efter ADX-klusteretablering. Om du associerar arbetsytor och intar data före etableringen tas intjesterade data bort och kan inte återställas.

**Associera en arbetsyta**

Den här Resource Manager-begäran är asynkron åtgärd.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Svar**

200 OK och header.
Intövda data lagras krypterade med din hanterade nyckel efter associationsåtgärden, vilket kan ta upp till 90 minuter att slutföra. Du kan kontrollera arbetsytssocieringstillståndet på två sätt:
1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [statuskontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en [arbetsyta – Hämta](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) begäran och observera svaret, associerad arbetsyta kommer att ha en clusterResourceId under "funktioner".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Svar**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": days,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>CMK (KEK) återkallelse

Du kan återkalla din åtkomst till dina data genom att inaktivera nyckeln eller ta bort åtkomstprincipen *för klusterresurser* i key vault.You can revoke your access to your data by inaktivera din key or borttagning the Cluster resource access policy in your Key Vault. Azure Monitor Storage respekterar alltid ändringar i nyckelbehörigheter inom en timme, normalt tidigare, och Lagring blir otillgänglig. Alla data som förtärs till arbetsytor som är associerade med *klusterresursen* tas bort och frågor misslyckas. Tidigare inmatade data förblir otillgängliga i Azure Monitor Storage så länge du är din *klusterresurs* och dina arbetsytor inte tas bort. Otillgängliga data styrs av datalagringsprincipen och rensas när lagringen nås.

Lagring kommer regelbundet att avsöka key vault för att försöka packa upp krypteringsnyckeln och när åtkomst, datainmatning och fråga återupptas inom 30 minuter.

## <a name="cmk-kek-rotation"></a>CMK -rotation (KEK)

Rotation av CMK kräver explicit uppdatering av *klusterresursen* med den nya nyckelversionen i Azure Key Vault. Om du vill uppdatera Azure Monitor med den nya nyckelversionen följer du anvisningarna i steget Uppdatera *klusterresurs* med information om nyckelidentifierare. Om du uppdaterar nyckelversionen i Key Vault och inte uppdaterar den nya nyckelidentifieraren i *klusterresursen* fortsätter Azure Monitor Storage att använda din tidigare nyckel.
Alla dina data är tillgängliga efter nyckelrotationen, inklusive data som intas före rotationen och efter den, eftersom alla data förblir krypterade av Account Encryption Key (AEK) medan AEK nu krypteras av din nya KEK-version (Key Encryption Key.

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

- CMK-funktionen stöds på ADX-klusternivå och kräver ett dedikerat AZURE Monitor ADX-kluster med krav på att skicka 1 TB per dag eller mer.

- Maxantal *klusterresurser* per prenumeration är begränsat till 2

- *Cluster* Klusterresursasociation till arbetsyta bör endast utföras efter att du har verifierat att ADX-klusteretableringen har uppfyllts. Data som skickas före den här etableringen kommer att tas bort och kan inte återställas.

- CMK-kryptering gäller för nyligen intjesterade data efter CMK-konfigurationen. Data som intas före CMK-konfigurationen förblir krypterade med Microsoft-nyckeln. Du kan fråga data som förtärs före och efter CMK-konfigurationen sömlöst.

- När arbetsytan är associerad med en *klusterresurs* kan den inte avkoma från *klusterresursen,* eftersom data krypteras med din nyckel och inte är tillgängliga utan din KEK i Azure Key Vault.

- Azure Key Vault måste konfigureras som återställningsbart. Dessa egenskaper är inte aktiverade som standard och bör konfigureras med CLI och PowerShell:

  - [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) måste vara aktiverat
  - [Rensa skydd](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) bör slås på för att skydda mot kraft radering av den hemliga / valv även efter mjuk bort

- Application Insights och Log Analytics kräver separata *klusterresurser.* Klusterresursens *Cluster* typ definieras vid skapandetillfället genom att ange egenskapen "clusterType" på antingen LogAnalytics eller ApplicationInsights. Det *går* inte att ändra klusterresurstypen.

- *Klusterresursflytta* till en annan resursgrupp eller prenumeration stöds inte för närvarande.

- Din Azure Key Vault, *klusterresurs* och associerade arbetsytor måste finnas i samma region och i samma Azure Active Directory (Azure AD) klient, men de kan finnas i olika prenumerationer.

- Arbetsyteasasociationen till *klusterresursen* misslyckas om den är associerad med en annan *klusterresurs*

## <a name="troubleshooting-and-management"></a>Felsökning och hantering

- Tillgänglighet för Nyckelvalv
    - Vid normal drift – Lagring cachelagrar AEK under korta tidsperioder och går tillbaka till Key Vault för att packa upp med jämna mellanrum.
    
    - Tillfälliga anslutningsfel – Lagring hanterar tillfälliga fel (timeout, anslutningsfel, DNS-problem) genom att låta nycklarna stanna kvar i cacheminnet en kort stund längre och detta övervinner alla små blips i tillgänglighet. Fråge- och inmatningsfunktionerna fortsätter utan avbrott.
    
    - Live site – otillgänglighet på cirka 30 minuter gör att lagringskontot blir otillgängligt. Frågefunktionen är inte tillgänglig och intövda data cachelagras i flera timmar med Microsoft-tangenten för att undvika dataförlust. När åtkomsten till Key Vault återställs blir frågan tillgänglig och de tillfälliga cachelagrade data förtärs till datalagret och krypteras med CMK.

- Om du skapar en *klusterresurs* och anger KeyVaultProperties omedelbart kan åtgärden misslyckas eftersom åtkomstprincipen inte kan definieras förrän systemidentiteten har tilldelats *klusterresursen.*

- Om du uppdaterar befintlig *klusterresurs* med KeyVaultProperties och "Get" nyckelåtkomstprincip saknas i Key Vault, misslyckas åtgärden.

- Om du försöker ta bort en *klusterresurs* som är associerad med en arbetsyta misslyckas borttagningen.

- Hämta alla *klusterresurser* för en resursgrupp:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Svar**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- Hämta alla *klusterresurser* för en prenumeration:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Svar**
    
  Samma svar som för*klusterresurser* för en resursgrupp, men i prenumerationsomfång.
    
- Ta bort *klusterresursen* – en mjuk borttagningsåtgärd utförs för att möjliggöra återställning av klusterresursen, dina data och associerade arbetsytor inom 14 dagar, oavsett om borttagningen var oavsiktlig eller avsiktlig. *Klusterresursnamnet* förblir reserverat under den mjuka borttagningsperioden och du kan inte skapa ett nytt kluster med det namnet. Efter mjukborttagningsperioden kan *klusterresursen* och dina data inte återställas. Associerade arbetsytor är avabestärade från *klusterresursen* och nya data intas i delad lagring och krypteras med Microsoft-nyckel.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Svar**

  200 OK

- Återställ *klusterresursen* och dina data – under den mjuka borttagningsperioden skapar du en *klusterresurs* med samma namn och i samma prenumeration, resursgrupp och region. Följ steget **Skapa *klusterresurs* ** för att återställa *klusterresursen.*


## <a name="appendix"></a>Bilaga

Programstatistik customer managed key (CMK) stöds också, men du bör överväga följande ändring för att hjälpa dig att planera distributionen av CMK för dina Application Insight-komponenter.

Log Analytics och Application Insights använder samma datalagringsplattform och frågemotor. Vi sammanför dessa två butiker via integrering av Application Insights i Log Analytics för att tillhandahålla ett enda enhetligt loggar-butik under Azure Monitor under andra kvartalet
2020. Den här ändringen kommer att föra in dina Application Insight-data i Log Analytics-arbetsytor och göra frågor, insikter och andra förbättringar möjliga medan konfigurationen av CMK på arbetsytan, gäller även för dina Application Insights-data.

> [!NOTE]
> Om du inte behöver distribuera CMK för dina Application Insight-data före integreringen rekommenderar vi att du väntar med Application Insights CMK eftersom sådana distributioner kommer att störas av integrationen och du måste konfigurera om CMK efter migreringen till Log Analytics-arbetsytan. Minimumet på 1 TB per dag gäller på klusternivå och tills konsolideringen är klar under andra kvartalet kräver Application Insights och Log Analytics separata kluster.

## <a name="application-insights-cmk-configuration"></a>CMK-konfiguration för programinsikter

Konfigurationen av Application Insights CMK är identisk med den process som illustreras i den här artikeln, inklusive begränsningar och felsökning förutom följande steg:

- Skapa en *klusterresurs*
- Associera en komponent till en *klusterresurs*

När du konfigurerar CMK för application insights följer du dessa steg i stället för de som anges ovan.

### <a name="create-a-cluster-resource"></a>Skapa en *klusterresurs*

Den här resursen används som mellanliggande identitetsanslutning mellan Key Vault och dina komponenter. När du har fått en bekräftelse på att dina prenumerationer har vitlistats skapar du en Log Analytics *Cluster-resurs* i den region där komponenterna finns. Typ av *klusterresurs* definieras vid skapandetid genom att ange *egenskapen clusterType* till antingen *LogAnalytics*eller *ApplicationInsights*. Det bör vara *ApplicationInsights* för Application Insights CMK. *Klastypinställningen* kan inte ändras efter konfigurationen.

**Skapa**

Den här Resource Manager-begäran är asynkron åtgärd.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Svar**

200 OK och header.
Under funktionens tidiga åtkomstperiod etableras ADX-klustret manuellt. Det tar visserligen att etablera det underliggande ADX-klustret ett tag att slutföra, men du kan kontrollera etableringstillståndet på två sätt:
1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [statuskontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *klusterresursen* och titta på *etableringstatsvärdet.* Det är *etableringKonto vid* etablering och lyckades när den har *slutförts.*

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Associera en komponent till en *klusterresurs* med [komponenter – Skapa eller uppdatera](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

Du måste ha skrivbehörighet för både komponenten och *klusterresursen* för att utföra den här åtgärden, vilket inkluderar följande åtgärder:

- Komponent: Microsoft.Insights/component/write
- I *klusterresurs:* Microsoft.OperationalInsights/kluster/skrivning

> [!IMPORTANT]
> Det här steget bör endast utföras efter ADX-klusteretablering. Om du associerar komponenter och hämtar data före etableringen tas intjesterade data bort och kan inte återställas.
> Om du vill kontrollera att ADX-klustret är etablerat kör du *klusterresursen* Hämta REST API och kontrollera att *etableringstate-värdet* *har lyckats*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Svar**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Kopiera och behåll svaret eftersom du behöver det i nästa steg.

**Associera en komponent**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" är värdet "clusterId" som anges i svaret från föregående steg.
"typ" exempel är "web".

**Svar**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" är *Cluster* klusterresurs-ID som är associerat med den här komponenten.

Efter associationen lagras data som skickas till dina komponenter krypterade med den hanterade nyckeln.
