---
title: Azure Monitor loggar dedicerade kluster
description: Kunder som inhämtar mer än 1 TB en dag med övervaknings data kan använda dedikerade snarare än delade kluster
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: a68501bd1189993b4dd0c2acdecaa7434fa51dcc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488042"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor loggar dedicerade kluster

Azure Monitor loggar dedikerade kluster är ett distributions alternativ som gör att du kan Azure Monitor logga kunder på en avancerad funktion. Kunder med dedikerade kluster kan välja vilka arbets ytor som ska finnas i dessa kluster.

De funktioner som kräver dedikerade kluster är:

- **[Kundhanterade nycklar](../platform/customer-managed-keys.md)** – kryptera kluster data med hjälp av nycklar som tillhandahålls och styrs av kunden.
- **[Säker databas](../platform/customer-managed-keys.md#customer-lockbox-preview)** kan styra åtkomst begär Anden från Microsofts support tekniker för data.
- **[Dubbel kryptering](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** skyddar mot ett scenario där en av krypteringsalgoritmer eller nycklar kan komprometteras. I det här fallet fortsätter det extra krypterings lagret att skydda dina data.
- **[Flera arbets ytor](../log-query/cross-workspace-query.md)** – om en kund använder mer än en arbets yta för produktion kan det vara bra att använda dedicerat kluster. Frågor över flera arbets ytor kommer att köras snabbare om alla arbets ytor finns i samma kluster. Det kan också vara mer kostnads effektivt att använda dedicerat kluster som tilldelade kapacitets nivåer för kapacitet, ta hänsyn till all kluster inmatning och gäller alla dess arbets ytor, även om några av dem är små och inte berättigade till kapacitets reservations rabatt.

Dedikerade kluster kräver att kunderna genomför med en kapacitet på minst 1 TB data inmatning per dag. Det är enkelt att migrera till ett dedikerat kluster. Ingen data förlust eller tjänst avbrott. 

> [!IMPORTANT]
> Dedikerade kluster godkänns och stöds fullt ut för produktions distributioner. Men på grund av tillfälliga kapacitets begränsningar kräver vi att ditt för registrering ska använda funktionen. Använd dina kontakter i Microsoft för att tillhandahålla dina prenumerations-ID: n.

## <a name="management"></a>Hantering 

Dedikerade kluster hanteras via en Azure-resurs som representerar Azure Monitor logg kluster. Alla åtgärder utförs på den här resursen med PowerShell eller REST API.

När klustret har skapats kan det konfigureras och arbets ytorna är länkade till den. När en arbets yta är länkad till ett kluster finns nya data som skickas till arbets ytan i klustret. Endast arbets ytor i samma region som klustret kan länkas till klustret. Arbets ytor kan vara till skillnad från ett kluster med vissa begränsningar. Mer information om dessa begränsningar finns i den här artikeln. 

Data som matas in på dedikerade kluster krypteras två gånger – en gång på tjänst nivå med hjälp av Microsoft-hanterade nycklar eller [kundhanterad nyckel](../platform/customer-managed-keys.md), och en gång på infrastruktur nivå med två olika krypteringsalgoritmer och två olika nycklar. [Dubbel kryptering](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) skyddar mot ett scenario där en av krypteringsalgoritmer eller nycklar kan komprometteras. I det här fallet fortsätter det extra krypterings lagret att skydda dina data. Med dedikerat kluster kan du också skydda dina data med [Lås](../platform/customer-managed-keys.md#customer-lockbox-preview) kontroll.

Alla åtgärder på kluster nivån kräver `Microsoft.OperationalInsights/clusters/write` behörigheten åtgärd i klustret. Den här behörigheten kan beviljas via den ägare eller deltagare som innehåller `*/write` åtgärden eller via rollen Log Analytics Contributor som innehåller `Microsoft.OperationalInsights/*` åtgärden. Mer information om Log Analytics behörigheter finns [i Hantera åtkomst till logg data och arbets ytor i Azure Monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Kluster pris modell

Log Analytics dedikerade kluster använder en pris modell för kapacitets reservationer som minst 1000 GB/dag. All användning ovanför reservations nivån debiteras enligt priset för betala per användning.  Pris informationen för kapacitets reservationen finns på [sidan Azure Monitor priser]( https://azure.microsoft.com/pricing/details/monitor/).  

Reservations nivån för kluster kapaciteten konfigureras via programmering med Azure Resource Manager med hjälp av `Capacity` parametern under `Sku` . `Capacity`Anges i enheter om GB och kan ha värden på 1000 GB/dag eller mer i steg om 100 GB/dag.

Det finns två fakturerings lägen för användning i ett kluster. Dessa kan anges av- `billingType` parametern när du konfigurerar klustret. 

1. **Kluster**: i det här fallet (som är standard) görs faktureringen för inmatade data på kluster nivå. De inmatade data mängderna från varje arbets yta som är kopplad till ett kluster sammanställs för att beräkna den dagliga fakturan för klustret. 

2. **Arbets ytor**: kostnaderna för kapacitets reservationen för klustret anges i proportion till arbets ytorna i klustret (efter redovisningen av tilldelningar per nod från [Azure Security Center](../../security-center/index.yml) för varje arbets yta.)

Observera att om din arbets yta använder pris nivån bakåtkompatibelt per nod, kommer den att faktureras baserat på data som matas in mot klustrets kapacitets reservation och inte längre per nod. Data tilldelningar per nod från Azure Security Center fortsätter att gälla.

Mer information debiteras för Log Analytics dedikerade kluster finns [här]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).


## <a name="creating-a-cluster"></a>Skapa ett kluster

Först skapar du kluster resurser för att börja skapa ett dedikerat kluster.

Följande egenskaper måste anges:

- **Kluster** namn: används i administrations syfte. Användare visas inte för det här namnet.
- **ResourceGroupName**: för alla Azure-resurser tillhör kluster en resurs grupp. Vi rekommenderar att du använder en central IT-resurs grupp eftersom kluster vanligt vis delas av många team i organisationen. Om du vill ha mer design överväganden kan du läsa om [hur du utformar Azure Monitor loggar distribution](../platform/design-logs-deployment.md)
- **Plats**: ett kluster finns i en angiven Azure-region. Endast arbets ytor i den här regionen kan länkas till det här klustret.
- **SkuCapacity**: du måste ange *kapacitets reservations* nivån (SKU) när du skapar en *kluster* resurs. *Kapacitets reservations* nivån kan vara inom INTERVALLET 1 000 gb till 3 000 GB per dag. Du kan uppdatera den i steg om 100 senare om det behövs. Om du behöver kapacitets reservations nivå över 3 000 GB per dag kan du kontakta oss på LAIngestionRate@microsoft.com . Mer information om kluster kostnader finns i [hantera kostnader för Log Analytics kluster](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

När du har skapat en *kluster* resurs kan du redigera ytterligare egenskaper som *SKU*, * keyVaultProperties eller *billingType*. Se mer information nedan.

> [!WARNING]
> Skapande av kluster utlöser resursallokering och etablering. Den här åtgärden kan ta upp till en timme att slutföra. Vi rekommenderar att du kör det asynkront.

Användar kontot som skapar klustren måste ha standard behörighet för Azure-resurser: `Microsoft.Resources/deployments/*` och kluster Skriv behörighet `(Microsoft.OperationalInsights/clusters/write)` .

### <a name="create"></a>Skapa 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Anropa* 
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

*Response*

Ska vara 200 OK och en rubrik.

### <a name="check-provisioning-status"></a>Kontrollera etableringsstatus

Det tar en stund att slutföra etableringen av det Log Analytics klustret. Du kan kontrol lera etablerings statusen på flera sätt:

- Kör Get-AzOperationalInsightsCluster PowerShell-kommandot med namnet på resurs gruppen och kontrol lera egenskapen ProvisioningState. Värdet är *ProvisioningAccount* medan *etableringen och* slutförts.
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Kopiera Azure-AsyncOperation URL-värdet från svaret och följ status kontrollen asynkrona åtgärder.

- Skicka en GET-begäran på *kluster* resursen och titta på *provisioningState* -värdet. Värdet är *ProvisioningAccount* medan *etableringen och* slutförts.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

   **Response**

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
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*PrincipalId* GUID genereras av den hanterade identitets tjänsten för *kluster* resursen.

## <a name="change-cluster-properties"></a>Ändra kluster egenskaper

När du har skapat *kluster* resursen och den är helt etablerad kan du redigera ytterligare egenskaper på kluster nivå med PowerShell eller REST API. Förutom de egenskaper som är tillgängliga när klustret skapas kan ytterligare egenskaper bara anges efter att klustret har etablerats:

- **keyVaultProperties**: används för att konfigurera Azure Key Vault som används för att etablera en [Azure Monitor kundhanterad nyckel](../platform/customer-managed-keys.md#customer-managed-key-provisioning-procedure). Den innehåller följande parametrar:  *KeyVaultUri*, *attributnamn*, *version*. 
- **billingType** – egenskapen *billingType* bestämmer fakturerings behörigheten för *kluster* resursen och dess data:
  - **Kluster** (standard) – kapacitets reservationens kostnader för klustret är attribut till *kluster* resursen.
  - **Arbets ytor** – kapacitets reservationens kostnader för klustret anges i proportion till arbets ytorna i klustret, där *kluster* resursen faktureras viss användning om den totala inmatade data för dagen är under kapacitets reservationen. Se [Log Analytics dedikerade kluster](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) för att lära dig mer om kluster pris modellen. 

> [!NOTE]
> Egenskapen *billingType* stöds inte i PowerShell.
> Kluster egenskaps uppdateringar kan köras asynkront och kan ta en stund att slutföra.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> Du kan uppdatera *kluster* resursen *SKU*, *keyVaultProperties* eller *billingType* med hjälp av patch.

Exempel: 

*Anropa*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
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

*Response*

200 OK och rubrik

### <a name="check-cluster-update-status"></a>Kontrol lera status för kluster uppdatering

Det tar några minuter att utföra spridningen av nyckel identifieraren. Du kan kontrol lera uppdaterings statusen på två sätt:

- Kopiera Azure-AsyncOperation URL-värdet från svaret och följ status kontrollen asynkrona åtgärder. 

   ELLER

- Skicka en GET-begäran på *kluster* resursen och titta på *KeyVaultProperties* -egenskaperna. Den senast uppdaterade informationen om nyckel identifieraren ska returneras i svaret.

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
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>Länka en arbets yta till klustret

När en arbets yta är länkad till ett dedikerat kluster dirigeras nya data som matas in i arbets ytan till det nya klustret medan befintliga data behålls i det befintliga klustret. Om det dedikerade klustret är krypterat med Kundhanterade nycklar (CMK) krypteras endast nya data med nyckeln. Systemet sammanfattar den här skillnaden från användarna och användarna skickar bara frågor till arbets ytan som vanligt medan systemet utför kors kluster frågor på Server delen.

Ett kluster kan länkas till upp till 100 arbets ytor. Länkade arbets ytor finns i samma region som klustret. För att skydda system Server delen och undvika fragmentering av data kan en arbets yta inte länkas till ett kluster mer än två gånger i månaden.

Om du vill utföra länk åtgärden måste du ha Skriv behörighet till både arbets ytan och *kluster* resursen:

- I arbets ytan: *Microsoft. OperationalInsights/arbets ytor/Write*
- I *kluster* resursen: *Microsoft. OperationalInsights/kluster/Write*

Förutom fakturerings aspekterna behåller den länkade arbets ytan sina egna inställningar, till exempel längden på data kvarhållning.
Arbets ytan och klustret kan finnas i olika prenumerationer. Det är möjligt att arbets ytan och klustret finns i olika klienter om Azure-Lighthouse används för att mappa båda till en enda klient.

Som kluster åtgärd kan länkar av en arbets yta bara utföras när Log Analytics kluster etableringen har slutförts.

> [!WARNING]
> Att länka en arbets yta till ett kluster kräver synkronisering av flera Server dels komponenter och att tillåta cachelagring av hydrering. Den här åtgärden kan ta upp till två timmar att slutföra. Vi rekommenderar att du kör det asynkront.


**PowerShell**

Använd följande PowerShell-kommando för att länka till ett kluster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Använd följande REST-anrop för att länka till ett kluster:

*Skicka*

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

*Response*

200 OK och rubrik.

### <a name="using-customer-managed-keys-with-linking"></a>Använda Kundhanterade nycklar med länkning

Om du använder Kundhanterade nycklar lagras inmatade data krypterade med din hanterade nyckel efter det att associationen har utförts, vilket kan ta upp till 90 minuter att slutföra. 

Du kan kontrol lera associerings tillstånd för arbets ytan på två sätt:

- Kopiera Azure-AsyncOperation URL-värdet från svaret och följ status kontrollen asynkrona åtgärder.

- Skicka en [arbets yta – Hämta](/rest/api/loganalytics/workspaces/get) begäran och observera svaret. Den associerade arbets ytan har en clusterResourceId under "Features".

En sändnings förfrågan ser ut så här:

*Skicka*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Response*

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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Ta bort länken mellan en arbets yta och ett dedikerat kluster

Du kan ta bort länken till en arbets yta från ett kluster. När du har länkat en arbets yta från klustret skickas inte nya data som är kopplade till den här arbets ytan till det dedikerade klustret. Dessutom görs inte längre arbets ytans fakturering via klustret. Gamla data för den olänkade arbets ytan kan vara kvar i klustret. Om dessa data är krypterade med Kundhanterade nycklar (CMK) behålls Key Vault hemligheterna. Systemet är en sammanfattning av den här ändringen från Log Analytics användare. Användare kan bara fråga arbets ytan som vanligt. Systemet utför kors kluster frågor på Server delen om det behövs utan att användaren tillfrågas.  

> [!WARNING] 
> Det finns en gräns på två länknings åtgärder per arbets yta inom en månad. Ta hänsyn till och planera att ta bort länkar från åtgärder enligt detta. 

## <a name="delete-a-dedicated-cluster"></a>Ta bort ett dedikerat kluster

En dedikerad kluster resurs kan tas bort. Du måste ta bort länken mellan alla arbets ytor från klustret innan du tar bort det. Du behöver Skriv behörighet för *kluster* resursen för att utföra den här åtgärden. 

När kluster resursen har tagits bort övergår det fysiska klustret till en rensnings-och borttagnings process. Borttagning av ett kluster tar bort alla data som lagrats i klustret. Data kan vara från arbets ytor som var länkade till klustret tidigare.

En *kluster* resurs som togs bort under de senaste 14 dagarna är i läget för tyst Borttagning och kan återställas med sina data. Eftersom alla arbets ytor har kopplats bort från *kluster* resursen med *kluster* resurs borttagning måste du associera dina arbets ytor igen efter återställningen. Det går inte att utföra återställnings åtgärden av användaren kontakta din Microsoft-kanal eller support för återställnings begär Anden.

Inom 14 dagar efter borttagningen är kluster resurs namnet reserverat och kan inte användas av andra resurser.

**PowerShell**

Använd följande PowerShell-kommando för att ta bort ett kluster:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Använd följande REST-anrop för att ta bort ett kluster:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK



## <a name="next-steps"></a>Nästa steg

- Läs mer om [Log Analytics dedikerad kluster fakturering](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Lär dig om [rätt design av Log Analytics-arbetsytor](../platform/design-logs-deployment.md)
