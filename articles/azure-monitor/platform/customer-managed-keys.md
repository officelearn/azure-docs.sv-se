---
title: Azure Monitor kundhanterad nyckel
description: Information och steg för att konfigurera kundhanterad nyckel (CMK) för att kryptera data i dina Log Analytics arbets ytor med hjälp av en Azure Key Vault nyckel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/20/2020
ms.openlocfilehash: aa27ba30c7a403cf70396e219b0619e2e84b0d4f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747741"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor kundhanterad nyckel 

Den här artikeln innehåller bakgrunds information och steg för att konfigurera Kundhanterade nycklar (CMK) för dina Log Analytics-arbetsytor. När de har kon figurer ATS krypteras alla data som skickas till dina arbets ytor med din Azure Key Vault nyckel.

Vi rekommenderar att du granskar [begränsningar och](#limitationsandconstraints) begränsningar nedan före konfigurationen.

## <a name="customer-managed-key-cmk-overview"></a>Översikt över kundhanterad nyckel (CMK)

[Kryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)   är ett gemensamt sekretess-och säkerhets krav i organisationer.Du kan låta Azure helt hantera kryptering i vila, medan du har olika alternativ för att hantera krypterings-eller krypterings nycklar.

Azure Monitor säkerställer att alla data krypteras i vila med hjälp av Azure-hanterade nycklar.Azure Monitor innehåller också ett alternativ för data kryptering med hjälp av din egen nyckel som lagras i din [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)   och som används av lagring med systemtilldelad autentisering med [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)   .Den här nyckeln kan vara antingen [program vara eller maskin vara-HSM skyddad](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Azure Monitor krypterings användningen är identisk med hur [Azure Storage kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)   fungerar.

Med CMK kan du kontrol lera åtkomsten till dina data och återkalla den när du vill. Azure Monitor Storage respekterar alltid ändringar i nyckel behörigheter inom en timme. Data som matats in under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett CMK-konfiguration, men kontrollen över SSD-data följer [nyckel återkallning](#cmk-kek-revocation). Vi arbetar med att ha SSD-data krypterade med CMK i den andra halvan av 2020.

Data som matats in under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett CMK-konfiguration, men kontrollen över SSD-data följer [nyckel återkallning](#cmk-kek-revocation). Vi arbetar med att ha SSD-data krypterade med CMK i den andra halvan av 2020.

CMK-funktionen levereras på dedikerade Log Analytics-kluster. För att kontrol lera att vi har den kapacitet som krävs i din region, kräver vi att din prenumeration vit listas i förväg. Använd din Microsoft-kontakt för att hämta din prenumerations vit listas innan du börjar konfigurera CMK.

 [Pris modellen Log Analytics kluster](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)   använder kapacitets reservationer som börjar med en 1000 GB/dag-nivå.

## <a name="how-cmk-works-in-azure-monitor"></a>Så här fungerar CMK i Azure Monitor

Azure Monitor utnyttjar systemtilldelad hanterad identitet för att ge åtkomst till din Azure Key Vault.Systemtilldelad hanterad identitet kan bara kopplas till en enda Azure-resurs medan Log Analytics klustrets identitet stöds på kluster nivå.Detta anger att CMK-funktionen levereras på ett dedikerat Log Analytics-kluster.För att stödja CMK på flera arbets ytor, fungerar en ny Log Analytics *kluster*   resurs som en mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor.Den Log Analytics kluster lagringen använder den hanterade identitet som \' är associerad med *kluster*   resursen för att autentisera till din Azure Key Vault via Azure Active Directory. 
Efter CMK-konfigurationen krypteras alla data som matats in till arbets ytor som är kopplade till *kluster*   resursen med nyckeln i Key Vault. Du kan när som helst koppla från arbets ytor från *kluster*   resursen.Nya data matas in för att Log Analytics lagring och krypteras med Microsoft-nyckeln, medan du kan fråga dina nya och gamla data sömlöst.

![Översikt över CMK](media/customer-managed-keys/cmk-overview-8bit.png)

1. Key Vault
2. Log Analytics *kluster* resurs som har hanterad identitet med behörighet att Key Vault--identiteten sprids till Underlay dedikerade Log Analytics kluster lagring
3. Dedikerat Log Analytics kluster
4. Arbets ytor som är associerade med *kluster* RESURSEN för CMK-kryptering

## <a name="encryption-keys-operation"></a>Åtgärd för krypterings nycklar

Det finns tre typer av nycklar som ingår i kryptering av lagrings data:

- Nyckel krypterings nyckel för **KEK** (CMK)
- Krypterings nyckel för **AEK** -konto
- **DEK** – data krypterings nyckel

Följande regler gäller:

- Log Analytics kluster lagrings konton genererar unik krypterings nyckel för varje lagrings konto, vilket kallas för AEK.

- AEK används för att härleda DEKs, vilket är de nycklar som används för att kryptera varje data block som skrivs till disk.

- När du konfigurerar din nyckel i Key Vault och refererar till den i *kluster* resursen, skickar Azure Storage begär anden till Azure Key Vault för att omsluta och packa upp AEK för att utföra data kryptering och dekryptering.

- Din KEK lämnar aldrig Key Vault och om det finns en HSM-nyckel lämnar den aldrig maskin varan.

- Azure Storage använder den hanterade identitet som är kopplad till *kluster* resursen för att autentisera och komma åt Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Etablerings procedur för CMK

1. Prenumerations-vit listning – CMK-funktionen levereras på dedikerade Log Analytics kluster. För att kontrol lera att vi har den kapacitet som krävs i din region, kräver vi att din prenumeration vit listas i förväg. Använd din Microsoft-kontakt för att få din prenumeration vit listas
2. Skapa Azure Key Vault och lagra nyckel
3. Skapa en *kluster* resurs
4. Bevilja behörighet till din Key Vault
5. Associera Log Analytics-arbetsytor

Proceduren stöds för närvarande inte i användar gränssnittet och etablerings processen utförs via REST API.

> [!IMPORTANT]
> Alla API-förfrågningar måste innehålla en token Authorization-token i begär ande huvudet.

Ett exempel:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Där *eyJ0eXAiO..* . representerar hela autentiseringstoken. 

Du kan hämta token med någon av följande metoder:

1. Använd [Appregistreringar](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) metod.
2. I Azure-portalen
    1. Navigera till Azure Portal medan du är i "utvecklarverktyg" (F12)
    1. Sök efter Authorization-sträng under "begärandehuvuden" i någon av instanserna "batch? API-version". Det ser ut så här: "auktorisering: Bearer-eyJ0eXAiO....". 
    1. Kopiera och Lägg till det i API-anropet enligt exemplen nedan.
3. Gå till webbplatsen för Azure REST-dokumentation. Tryck på "prova" på alla API: er och kopiera Bearer-token.

### <a name="asynchronous-operations-and-status-check"></a>Asynkrona åtgärder och status kontroll

Några av åtgärderna i den här konfigurations proceduren körs asynkront eftersom de inte kan slutföras snabbt. Svaret för asynkron åtgärd returnerar ursprungligen HTTP-statuskod 200 (OK) och rubriken med *Azure-AsyncOperation-* egenskapen när den godkänns:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Du kan kontrol lera statusen för den asynkrona åtgärden genom att skicka en GET-begäran till värdet för *Azure-AsyncOperation-* huvudet:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Svaret innehåller information om åtgärden och dess *status*. Det kan vara något av följande:

Åtgärden pågår
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

Åtgärden har slutförts
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

### <a name="subscription-whitelisting"></a>Vit listning för prenumeration

CMK-funktionen levereras på dedikerade Log Analytics-kluster.För att kontrol lera att vi har den kapacitet som krävs i din region, kräver vi att din prenumeration vit listas i förväg. Använd dina kontakter i Microsoft för att tillhandahålla dina prenumerations-ID: n.

> [!IMPORTANT]
> CMK-funktionen är regional. Din Azure Key Vault, *kluster* resurs och associerade Log Analytics arbets ytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

### <a name="storing-encryption-key-kek"></a>Lagra krypterings nyckel (KEK)

Skapa eller Använd en Azure Key Vault som du redan har för att skapa eller importera en nyckel som ska användas för data kryptering. Azure Key Vault måste konfigureras som rekonstruerbar för att skydda din nyckel och åtkomsten till dina data i Azure Monitor. Du kan kontrol lera den här konfigurationen under egenskaper i Key Vault, både *mjuk borttagnings* -och *rensnings skydd* ska vara aktiverat.

![Mjuk borttagning och rensning av skydds inställningar](media/customer-managed-keys/soft-purge-protection.png)

De här inställningarna är tillgängliga via CLI och PowerShell:
- [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Rensa skydds skydd](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

### <a name="create-cluster-resource"></a>Skapa *kluster* resurs

Den här resursen används som en mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor. När du har fått en bekräftelse på att dina prenumerationer har vit listas skapar du en Log Analytics *kluster* resurs i den region där dina arbets ytor finns.

Du måste ange *kapacitets reservations* nivå (SKU) när du skapar en *kluster* resurs. *Kapacitets reservations* nivån kan ligga inom intervallet 1 000 till 2 000 GB per dag och du kan uppdatera den i steg om 100 senare. Om du behöver kapacitets reservations nivå över 2 000 GB per dag kan du kontakta oss på LAIngestionRate@microsoft.com . [Läs mer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

Egenskapen *billingType* bestämmer fakturerings behörigheten för *kluster* resursen och dess data:
- *kluster* (standard)--faktureringen tillskrivs till den prenumeration som är värd för *kluster* resursen
- *arbets ytor* – faktureringen hänförs till prenumerationerna som är värdar för dina arbets ytor proportionellt

> [!NOTE]
> När du har skapat en *kluster* resurs kan du uppdatera den med *SKU*, *keyVaultProperties* eller *billingType* med hjälp av patch rest-begäran.

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
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

Identiteten tilldelas till *kluster* resursen vid skapande tillfället.

**Svar**

200 OK och rubrik.

När den tar etablering av Log Analytics klustret a när du är klar kan du kontrol lera etablerings statusen på två sätt:

1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *kluster* resursen och titta på *provisioningState* -värdet. Den är *ProvisioningAccount* medan *etableringen har slutförts och* slutförts.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Kopiera och spara svaret eftersom du kommer att behöva informationen i nästa steg.

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
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

GUID för "principalId" genereras av den hanterade identitets tjänsten för *kluster* resursen.

### <a name="grant-key-vault-permissions"></a>Bevilja Key Vault behörigheter

Uppdatera din Key Vault med en ny åtkomst princip som ger behörighet till *kluster* resursen. Dessa behörigheter används av Underlay Azure Monitor Storage för data kryptering. Öppna din Key Vault i Azure Portal och klicka på "åtkomst principer" och sedan "+ Lägg till åtkomst princip" för att skapa en princip med följande inställningar:

- Nyckel behörigheter: Välj get-, wrap-och unwrap Key-behörigheter.
- Välj huvud namn: Ange det huvud-ID-värde som returnerades i svaret i föregående steg.

![bevilja Key Vault behörigheter](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Get-behörighet krävs för att verifiera att Key Vault har kon figurer ATS för att kunna *återskapas* för att skydda din nyckel och till gång till dina Azure Monitor data.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Uppdatera kluster resurs med information om nyckel identifierare

Det här steget utförs under initial och i framtida viktiga versions uppdateringar i din Key Vault. Det informerar Azure Monitor lagring om den nyckel version som ska användas för data kryptering. När den uppdateras används den nya nyckeln för att packa upp och packa upp till lagrings nyckeln (AEK).

Om du vill uppdatera *kluster* resursen med information om Key Vault *nyckel identifierare* väljer du den aktuella versionen av nyckeln i Azure Key Vault för att hämta information om nyckel identifieraren.

![Bevilja Key Vault behörigheter](media/customer-managed-keys/key-identifier-8bit.png)

Uppdatera *kluster* resursens KeyVaultProperties med information om nyckel identifierare.

**Uppdatera**

Den här Resource Manager-begäran är asynkron åtgärd vid uppdatering av nyckel-ID-information, medan den är synkron vid uppdatering av kapacitet svärdet.

> [!NOTE]
> Du kan ange del brödtext i *kluster* resursen för att uppdatera en *SKU*, *keyVaultProperties* eller *billingType*.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" innehåller information om Key Vault-nyckel-ID.

**Svar**

200 OK och rubrik.
Det tar några minuter att slutföra spridningen av nyckel identifieraren. Du kan kontrol lera uppdaterings statusen på två sätt:
1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *kluster* resursen och titta på *KeyVaultProperties* -egenskaperna. Den senast uppdaterade informationen om nyckel identifieraren ska returneras i svaret.

Ett svar på GET-begäran på *kluster* resursen bör se ut så här när nyckel identifierarens uppdatering är slutförd:

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
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Arbets ytans koppling till *kluster* resurs

Du måste ha behörigheten "Skriv" till både din arbets yta och *kluster* resurs för att utföra den här åtgärden, bland annat följande åtgärder:

- På arbets ytan: Microsoft. OperationalInsights/arbets ytor/Write
- I *kluster* resurs: Microsoft. OperationalInsights/kluster/Write

> [!IMPORTANT]
> Det här steget bör endast utföras när Log Analytics kluster etableringen har slutförts. Om du associerar arbets ytor och matar in data före etableringen tas inmatade data bort och går inte att återskapa.

**Koppla en arbets yta**

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

200 OK och rubrik.

Inmatade data lagras krypterade med din hanterade nyckel efter Associations åtgärd, vilket kan ta upp till 90 minuter att slutföra. Du kan kontrol lera associerings tillstånd för arbets ytan på två sätt:

1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en [arbets yta – Hämta](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) begäran och observera svaret, den associerade arbets ytan har en clusterResourceId under "funktioner".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
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
    "retentionInDays": 31,
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

## <a name="cmk-kek-revocation"></a>CMK (KEK) åter kallelse

Du kan återkalla åtkomsten till data genom att inaktivera nyckeln eller ta bort *kluster* resurs åtkomst principen i Key Vault. Kluster lagringen för Log Analytics kommer alltid att respektera ändringar i nyckel behörigheter inom en timme eller tidigare och lagringen kommer att bli otillgänglig. Alla nya data som matas in till arbets ytor som är associerade med *kluster*   resursen tas bort och kan inte återställas, inga data är tillgängliga och frågor till dessa arbets ytor fungerar inte. Tidigare inmatade data finns kvar i lagrings utrymmet så länge som *kluster* resursen och dina arbets ytor inte tas bort. Otillgängliga data regleras av data bevarande principen och kommer att rensas när kvarhållning har nåtts. 

Inmatade data under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Detta tas bort vid nyckel återkallnings åtgärden och blir oåtkomlig även.

Lagringen avsöker regelbundet Key Vault för att försöka att packa upp krypterings nyckeln och en gång till, så fortsätter data inmatningen och frågan att återupptas inom 30 minuter.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotation

Rotationen av CMK kräver explicit uppdatering av *kluster* resursen med den nya nyckel versionen i Azure Key Vault. Följ anvisningarna i steget uppdatera *kluster* resurs med information om nyckel identifierare. Om du inte uppdaterar den nya informationen om nyckel identifierare i *kluster* resursen, fortsätter den Log Analytics kluster lagringen att använda din tidigare nyckel för kryptering. Om du inaktiverar eller tar bort din gamla nyckel innan du uppdaterar den nya nyckeln i *kluster* resursen kommer du till [nyckel återkallnings](#cmk-kek-revocation) tillstånd.

Alla dina data är tillgängliga efter nyckel rotations åtgärden, eftersom data alltid krypteras med konto krypterings nyckeln (AEK) medan AEK nu krypteras med din nya KEK-version (Key Encryption Key) i Key Vault.

## <a name="cmk-manage"></a>Hantera CMK

- **Hämta alla *kluster* resurser för en resurs grupp**

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
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
          "billingType": "cluster",
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

- **Hämta alla *kluster* resurser för en prenumeration**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Svar**
    
  Samma svar som för "*kluster* resurser för en resurs grupp", men i prenumerations omfånget.

- **Uppdatera *kapacitets reservationen* i *kluster* resursen**

  När data volymen till dina associerade arbets ytor ändras med tiden och du vill uppdatera kapacitets reservations nivån korrekt. Följ [uppdaterings *kluster* resursen](#update-cluster-resource-with-key-identifier-details) och ange ditt nya kapacitets värde. Det kan vara mellan 1 000 och 2 000 GB per dag och i steg om 100. För högre nivå än 2 000 GB per dag når du din Microsoft-kontakt för att aktivera den. Observera att du inte behöver ange den fullständiga texten i REST-begäran och ska innehålla SKU: n:

  **brödtext**
  ```json
  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Uppdatera *billingType* i *kluster* resursen**

  Egenskapen *billingType* bestämmer fakturerings behörigheten för *kluster* resursen och dess data:
  - *kluster* (standard)--faktureringen tillskrivs till den prenumeration som är värd för kluster resursen
  - *arbets ytor* – faktureringen hänförs till prenumerationerna som är värdar för dina arbets ytor proportionellt
  
  Följ [uppdaterings *kluster* resursen](#update-cluster-resource-with-key-identifier-details) och ange det nya billingType-värdet. Observera att du inte behöver ange den fullständiga texten i REST-begäran och ska innehålla *billingType*:

  **brödtext**
  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Ta bort koppling till arbets yta**

  Du behöver Skriv behörighet för arbets ytan och *kluster* resursen för att utföra den här åtgärden. Du kan när som helst ta bort en arbets yta från *kluster* resursen. Nya inmatade data efter borttagningen av associationen lagras i Log Analytics lagring och krypteras med Microsoft-nyckel. Du kan fråga dig om data som har matats in till din arbets yta innan och efter associationen sömlöst så länge som *kluster* resursen är etablerad och konfigurerad med giltig Key Vault nyckel.

  Den här Resource Manager-begäran är asynkron åtgärd.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Svar**

  200 OK och rubrik.

  Inmatade data efter att den avassocierings åtgärd har lagrats i Log Analytics lagring, det kan ta 90 minuter att slutföra. Du kan kontrol lera status för arbets ytans associering på två sätt:

  1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
  2. Skicka en [arbets yta – get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) -begäran och Observera att svaret, den Avassocierade arbets ytan inte har *clusterResourceId* under *funktioner*.

- **Ta bort *kluster* resursen**

  Du behöver Skriv behörighet för *kluster* resursen för att utföra den här åtgärden. En mjuk borttagnings åtgärd utförs för att tillåta återställning av *kluster* resursen, inklusive dess data inom 14 dagar, om borttagningen var oavsiktlig eller avsiktlig. *Kluster* resurs namnet är reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Efter den mjuka borttagnings perioden släpps *kluster* resurs namnet, *kluster* resursen och data tas bort permanent och går inte att återställa. En associerad arbets yta kopplas bort från *kluster* resursen vid borttagnings åtgärden. Nya inmatade data lagras i Log Analytics lagring och krypteras med Microsoft-nyckel. Den Avassocierade arbets ytans åtgärd är asynkron och kan ta upp till 90 minuter att slutföra.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Svar**

  200 OK

- **Återställa *kluster* resursen och dina data** 
  
  En *kluster* resurs som togs bort under de senaste 14 dagarna är i läget för tyst Borttagning och kan återställas med sina data. Eftersom alla arbets ytor har kopplats bort från *kluster* resursen med *kluster* resurs borttagning måste du associera dina arbets ytor igen efter ÅTERställningen för CMK-kryptering. Återställnings åtgärden utförs manuellt av produkt gruppen för närvarande. Använd din Microsoft-kanal för återställnings begär Anden.

## <a name="limitationsandconstraints"></a>Begränsningar och begränsningar

-CMK stöds på dedikerade Log Analytics kluster och lämpar sig för kunder som skickar 1 TB per dag eller mer.

-Det maximala antalet *kluster*   resurser per region och prenumeration är 2

-Du kan koppla en arbets yta till *kluster*   resursen och sedan ta bort associationen om CMK inte krävs för arbets ytan.Antalet associationer för arbets ytan på en viss arbets yta under en period på 30 dagar är begränsat till 2

– Associationen för arbets ytan till *kluster*   resursen ska endast utföras när du har kontrollerat att Log Analytics kluster etableringen har slutförts.Data som skickas till din arbets yta innan slut för Ande kommer att tas bort och går inte att återskapa.

-CMK kryptering gäller nyligen inmatade data efter CMK-      konfigurationen.Data som matats in före CMK      -konfigurationen förblir krypterade med Microsoft-nyckeln.Du kan fråga efter data som matats in      före och efter CMK-konfigurationen sömlöst.

-Azure Key Vault måste konfigureras som återställnings Bart.De här egenskaperna är inte aktiverade som standard och ska konfigureras med CLI eller PowerShell:    -  [mjuk](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)borttagning 
      måste aktive ras för    -  [Purge protection](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)   att skydda mot Tvingad borttagning av hemligheten/valvet även efter mjuk borttagning.

- *Kluster*   Det finns      för närvarande inte stöd för att flytta resurser till en annan resurs grupp eller prenumeration.

-Din Azure Key Vault, *kluster*   resurs och associerade arbets ytor måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer.

– Associationen för arbets ytan till *kluster*   resursen kommer inte att fungera om den är      kopplad till en annan *kluster*   resurs

## <a name="troubleshooting"></a>Felsökning

- Beteende med Key Vault tillgänglighet
  - I normal drift – Storage cache-AEK under korta tids perioder och går tillbaka till Key Vault för att packa med jämna mellanrum.
    
  - Tillfälliga anslutnings fel – lagring hanterar tillfälliga fel (timeout, anslutnings fel, DNS-problem) genom att tillåta att nycklar hålls kvar i cacheminnet för kort, samtidigt som den överkommer till alla små signaler i tillgänglighet. Funktionerna för fråga och inmatning fortsätter utan avbrott.
    
  - Live site – otillgänglig cirka 30 minuter kommer lagrings kontot att bli otillgängligt. Fråge funktionen är inte tillgänglig och inmatade data cachelagras i flera timmar med Microsoft-nyckeln för att undvika data förlust. När åtkomsten till Key Vault återställs blir frågan tillgänglig och temporära cachelagrade data matas in i data lagringen och krypteras med CMK.

  - Key Vault åtkomst frekvens – den frekvens som Azure Monitor lagrings åtkomst Key Vault för omslutning och unwrap-åtgärder är mellan 6 och 60 sekunder.

- Om du skapar en *kluster* resurs och anger KeyVaultProperties omedelbart kan åtgärden Miss lyckas eftersom det inte går att definiera åtkomst principen förrän system identiteten har tilldelats till *kluster* resursen.

- Om du uppdaterar en befintlig *kluster* resurs med KeyVaultProperties och get nyckel åtkomst principen saknas i Key Vault, Miss kommer åtgärden.

- Om du försöker ta bort en *kluster* resurs som är kopplad till en arbets yta kommer borttagnings åtgärden att Miss förväntas.

- Om du får ett konflikt fel när du skapar en *kluster* resurs – det kan bero på att du har tagit bort *kluster* resursen under de senaste 14 dagarna och att den är i en mjuk borttagnings period. *Kluster* resurs namnet är reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Namnet släpps efter den mjuka borttagnings perioden när *kluster* resursen tas bort permanent.

- Om du uppdaterar *kluster* resursen medan en åtgärd pågår, fungerar inte åtgärden.

- Om du inte kan distribuera *kluster* resursen kontrollerar du att Azure Key Vault, *kluster*   resursen och tillhör ande Log Analytics arbets ytor finns i samma region. Kan finnas i olika prenumerationer.

- Om du uppdaterar din nyckel version i Key Vault och inte uppdaterar informationen om den nya nyckel identifieraren i *kluster* resursen, fortsätter Log Analytics klustret att använda din tidigare nyckel och dina data blir otillgängliga. Uppdatera informationen om den nya nyckel identifieraren i *kluster* resursen för att återuppta data inmatning och möjlighet att fråga data.

- För support och hjälp som är relaterat till kund Managed Key använder du dina kontakter i Microsoft.
