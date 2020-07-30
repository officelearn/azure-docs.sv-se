---
title: Azure Monitor kundhanterad nyckel
description: Information och steg för att konfigurera kundhanterad nyckel (CMK) för att kryptera data i dina Log Analytics arbets ytor med hjälp av en Azure Key Vault nyckel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/05/2020
ms.openlocfilehash: eec056cbe246f129fb78e15faa0027846c271181
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382958"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor kundhanterad nyckel 

Den här artikeln innehåller bakgrunds information och steg för att konfigurera Kundhanterade nycklar (CMK) för dina Log Analytics-arbetsytor. När de har kon figurer ATS krypteras alla data som skickas till dina arbets ytor med din Azure Key Vault nyckel.

Vi rekommenderar att du granskar [begränsningar och](#limitationsandconstraints) begränsningar nedan före konfigurationen.

## <a name="customer-managed-key-cmk-overview"></a>Översikt över kundhanterad nyckel (CMK)

[Kryptering i vila](../../security/fundamentals/encryption-atrest.md)   är ett gemensamt sekretess-och säkerhets krav i organisationer.Du kan låta Azure helt hantera kryptering i vila, medan du har olika alternativ för att hantera krypterings-eller krypterings nycklar.

Azure Monitor säkerställer att alla data och sparade frågor krypteras i vila med hjälp av Microsoft-hanterade nycklar (MMK). Azure Monitor innehåller också ett alternativ för kryptering med hjälp av din egen nyckel som lagras i din [Azure Key Vault](../../key-vault/general/overview.md) och som används av lagring med systemtilldelad autentisering med [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) . Den här nyckeln (CMK) kan vara antingen [program vara eller maskin vara-HSM skyddad](../../key-vault/general/overview.md).

Azure Monitor krypterings användningen är identisk med hur [Azure Storage kryptering](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)   fungerar.

Med CMK kan du kontrol lera åtkomsten till dina data och återkalla den när du vill. Azure Monitor Storage respekterar alltid ändringar i nyckel behörigheter inom en timme. Data som matats in under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett CMK-konfiguration, men kontrollen över SSD-data följer [nyckel återkallning](#cmk-kek-revocation). Vi arbetar med att ha SSD-data krypterade med CMK i den andra halvan av 2020.

CMK-funktionen levereras på dedikerade Log Analytics-kluster. För att verifiera att vi har den kapacitet som krävs i din region, kräver vi att din prenumeration tillåts i förväg. Använd din Microsoft-kontakt för att få din prenumeration tillåten innan du börjar konfigurera CMK.

 [Pris modellen Log Analytics kluster](./manage-cost-storage.md#log-analytics-dedicated-clusters)   använder kapacitets reservationer som börjar med en 1000 GB/dag-nivå.

## <a name="how-cmk-works-in-azure-monitor"></a>Så här fungerar CMK i Azure Monitor

Azure Monitor utnyttjar systemtilldelad hanterad identitet för att ge åtkomst till din Azure Key Vault. Systemtilldelad hanterad identitet kan bara kopplas till en enda Azure-resurs medan identiteten för Log Analytics-klustret stöds på kluster nivå – detta anger att CMK-funktionen levereras på ett dedikerat Log Analytics-kluster. För att stödja CMK på flera arbets ytor, fungerar en ny Log Analytics *kluster* resurs som en mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor. Den Log Analytics kluster lagringen använder den hanterade identitet som \' är associerad med *kluster* resursen för att autentisera till din Azure Key Vault via Azure Active Directory. 

Efter CMK-konfigurationen krypteras alla data som matats in till arbets ytor som är kopplade till *kluster* resursen med nyckeln i Key Vault. Du kan när som helst koppla från arbets ytor från *kluster* resursen. Nya data matas in för att Log Analytics lagring och krypteras med Microsoft-nyckeln, medan du kan fråga dina nya och gamla data sömlöst.


![Översikt över CMK](media/customer-managed-keys/cmk-overview.png)

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

1. Tillåter prenumeration – CMK-funktionen levereras på dedikerade Log Analytics-kluster. För att verifiera att vi har den kapacitet som krävs i din region, kräver vi att din prenumeration tillåts i förväg. Använd din Microsoft-kontakt för att få din prenumeration tillåten.
2. Skapa Azure Key Vault och lagra nyckel
3. Skapa en *kluster* resurs
4. Bevilja behörighet till din Key Vault
5. Associera Log Analytics-arbetsytor

Proceduren stöds inte i Azure Portal och etableringen utförs via PowerShell eller REST-begäranden.

> [!IMPORTANT]
> Alla REST-begäranden måste innehålla en token Authorization-token i begär ande huvudet.

Ett exempel:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Där *eyJ0eXAiO..* . representerar hela autentiseringstoken. 

Du kan hämta token med någon av följande metoder:

1. Använd [Appregistreringar](/graph/auth/auth-concepts#access-tokens) metod.
2. I Azure-portalen
    1. Navigera till Azure Portal medan du är i "utvecklarverktyg" (F12)
    1. Sök efter Authorization-sträng under "begärandehuvuden" i någon av instanserna "batch? API-version". Det ser ut så här: "auktorisering: Bearer-eyJ0eXAiO....". 
    1. Kopiera och Lägg till det i API-anropet enligt exemplen nedan.
3. Gå till webbplatsen för Azure REST-dokumentation. Tryck på "prova" på alla API: er och kopiera Bearer-token.

### <a name="asynchronous-operations-and-status-check"></a>Asynkrona åtgärder och status kontroll

Några av åtgärderna i den här konfigurations proceduren körs asynkront eftersom de inte kan slutföras snabbt. När du använder REST-begäranden i konfigurationen returnerar svaret från början en HTTP-statuskod 200 (OK) och rubriken med *Azure-AsyncOperation* -egenskapen när den godkänns:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Sedan kan du kontrol lera statusen för den asynkrona åtgärden genom att skicka en GET-begäran till värdet för *Azure-AsyncOperation-* huvudet:
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

Uppdaterings åtgärden för nyckel identifieraren pågår
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

*Kluster* resurs borttagning pågår--när du tar bort en *kluster* resurs som har associerade arbets ytor, utförs en disassociation för varje arbets yta i asynkrona åtgärder som kan ta en stund.
Detta är inte relevant när du tar bort ett *kluster* utan någon kopplad arbets yta – i det här fallet tas *kluster* resursen bort omedelbart.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
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

### <a name="allowing-subscription-for-cmk-deployment"></a>Tillåter prenumeration för CMK-distribution

CMK-funktionen levereras på dedikerade Log Analytics-kluster.För att verifiera att vi har den kapacitet som krävs i din region, kräver vi att din prenumeration tillåts i förväg. Använd dina kontakter i Microsoft för att tillhandahålla dina prenumerations-ID: n.

> [!IMPORTANT]
> CMK-funktionen är regional. Din Azure Key Vault, *kluster* resurs och associerade Log Analytics arbets ytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

### <a name="storing-encryption-key-kek"></a>Lagra krypterings nyckel (KEK)

Skapa eller Använd en Azure Key Vault som du redan har för att skapa eller importera en nyckel som ska användas för data kryptering. Azure Key Vault måste konfigureras som rekonstruerbar för att skydda din nyckel och åtkomsten till dina data i Azure Monitor. Du kan kontrol lera den här konfigurationen under egenskaper i Key Vault, både *mjuk borttagnings* -och *rensnings skydd* ska vara aktiverat.

![Mjuk borttagning och rensning av skydds inställningar](media/customer-managed-keys/soft-purge-protection.png)

De här inställningarna kan uppdateras via CLI och PowerShell:

- [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
- [Rensa skydds skydd](../../key-vault/general/soft-delete-overview.md#purge-protection) mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

### <a name="create-cluster-resource"></a>Skapa *kluster* resurs

Den här resursen används som en mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor. När du har fått en bekräftelse på att dina prenumerationer har tillåtits, skapar du en Log Analytics *kluster* resurs i den region där dina arbets ytor finns.

Du måste ange *kapacitets reservations* nivå (SKU) när du skapar en *kluster* resurs. *Kapacitets reservations* nivån kan ligga inom intervallet 1000 till 3000 GB per dag och du kan uppdatera den i steg om 100. Om du behöver kapacitets reservations nivå över 3000 GB per dag kan du kontakta oss på LAIngestionRate@microsoft.com . [Läs mer](./manage-cost-storage.md#log-analytics-dedicated-clusters)

Egenskapen *billingType* bestämmer fakturerings behörigheten för *kluster* resursen och dess data:
- *Kluster* (standard)--kapacitets reservationens kostnader för klustret är attribut till *kluster* resursen.
- *Arbets ytor* – kapacitets reservationens kostnader för klustret anges i proportion till arbets ytorna i klustret, där *kluster* resursen faktureras viss användning om den totala inmatade data för dagen är under kapacitets reservationen. Se [Log Analytics dedikerade kluster](manage-cost-storage.md#log-analytics-dedicated-clusters) för att lära dig mer om kluster pris modellen. 

> [!NOTE]
> * När du har skapat en *kluster* resurs kan du uppdatera den med *SKU*, *keyVaultProperties* eller *billingType* med hjälp av patch rest-begäran.
> * Du kan uppdatera *billingType* med rest-begäran för närvarande, detta stöds inte i PowerShell

Den här åtgärden är asynkron och kan vara en stund att slutföra.

> [!IMPORTANT]
> Kopiera och spara svaret eftersom du kommer att behöva informationen i nästa steg.
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity daily-ingestion-gigabyte 
```

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

**Response**

200 OK och rubrik.

När den tar etablering av Log Analytics klustret a när du är klar kan du kontrol lera etablerings statusen på två sätt:

1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *kluster* resursen och titta på *provisioningState* -värdet. Den är *ProvisioningAccount* medan *etableringen har slutförts och* slutförts.

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

GUID för "principalId" genereras av den hanterade identitets tjänsten för *kluster* resursen.

### <a name="grant-key-vault-permissions"></a>Bevilja Key Vault behörigheter

Uppdatera din Key Vault med en ny åtkomst princip som beviljar behörigheter till *kluster* resursen. Dessa behörigheter används av Underlay Azure Monitor Storage för data kryptering. Öppna din Key Vault i Azure Portal och klicka på "åtkomst principer" och sedan "+ Lägg till åtkomst princip" för att skapa en princip med följande inställningar:

- Nyckel behörigheter: Välj get-, wrap-och unwrap Key-behörigheter.
- Välj huvud namn: Ange det *kluster* resurs namn eller huvud-ID-värde som returnerades i svaret i föregående steg.

![bevilja Key Vault behörigheter](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Get-behörighet krävs för att verifiera att Key Vault har kon figurer ATS för att kunna *återskapas* för att skydda din nyckel och till gång till dina Azure Monitor data.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Uppdatera kluster resurs med information om nyckel identifierare

Det här steget utförs under initial och i framtida viktiga versions uppdateringar i din Key Vault. Det informerar Azure Monitor lagring om den nyckel version som ska användas för data kryptering. När den uppdateras används den nya nyckeln för att packa upp och packa upp till lagrings nyckeln (AEK).

Om du vill uppdatera *kluster* resursen med information om Key Vault *nyckel identifierare* väljer du den aktuella versionen av nyckeln i Azure Key Vault för att hämta information om nyckel identifieraren.

![Bevilja Key Vault behörigheter](media/customer-managed-keys/key-identifier-8bit.png)

Uppdatera *kluster* resursens KeyVaultProperties med information om nyckel identifierare.

Den här åtgärden är asynkron vid uppdatering av nyckel-ID-information och kan ta en stund att slutföra. Den är synkron vid uppdatering av kapacitet svärdet.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> Du kan uppdatera *kluster* resursen *SKU*, *keyVaultProperties* eller *billingType* med hjälp av patch.

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

**Response**

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

Den här åtgärden är asynkron och kan vara en stund att slutföra.

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

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

**Response**

200 OK och rubrik.

Inmatade data lagras krypterade med din hanterade nyckel efter Associations åtgärd, vilket kan ta upp till 90 minuter att slutföra. Du kan kontrol lera associerings tillstånd för arbets ytan på två sätt:

1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en [arbets yta – Hämta](/rest/api/loganalytics/workspaces/get) begäran och observera svaret, den associerade arbets ytan har en clusterResourceId under "funktioner".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Response**

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

## <a name="cmk-for-queries"></a>CMK för frågor

Frågespråket som används i Log Analytics är lättfattliga programspecifika och kan innehålla känslig information i kommentarer som du lägger till i frågor eller i frågesyntaxen. Vissa organisationer kräver att sådan information hålls skyddad som en del av CMK-principen och du måste spara dina frågor krypterade med din nyckel. Med Azure Monitor kan du lagra *sparade sökningar* och *Logga aviserings* frågor som är krypterade med din nyckel i ditt eget lagrings konto när du är ansluten till din arbets yta. 

> [!NOTE]
> Log Analytics frågor kan sparas i olika butiker beroende på vilket scenario som används. Frågorna förblir krypterade med Microsoft Key (MMK) i följande scenarier, oavsett CMK-konfiguration: arbets böcker i Azure Monitor, Azure-instrumentpaneler, Azure Logic app, Azure Notebooks och Automation-runbooks.

När du tar med ditt eget lagrings utrymme (BYOS) och associerar det till din arbets yta överförs frågor till ditt lagrings konto via tjänsten för *sparade sökningar* och *logg aviseringar* . Det innebär att du styr lagrings kontot och [principen för kryptering vid vila](../../storage/common/encryption-customer-managed-keys.md) antingen med samma nyckel som du använder för att kryptera data i Log Analytics kluster eller en annan nyckel. Du kommer dock att vara ansvarig för kostnaderna som är kopplade till det lagrings kontot. 

**Att tänka på innan du ställer in CMK för frågor**
* Du måste ha Skriv behörighet till både din arbets yta och ditt lagrings konto
* Se till att skapa ditt lagrings konto i samma region som din Log Analytics arbets yta finns
* *Spara sökningar* i lagring anses som tjänst artefakter och deras format kan ändras
* Befintliga *sparade sökningar* tas bort från din arbets yta. Kopiera och *Spara sökningar* som du behöver före konfigurationen. Du kan visa dina *sparade sökningar* med [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* Frågans historik stöds inte och du kommer inte att kunna se frågor som du körde
* Du kan associera ett enda lagrings konto till en arbets yta för att kunna spara frågor, men kan användas från frågor med både *sparade sökningar* och *logg aviseringar*
* Fäst på instrument panelen stöds inte

**Konfigurera BYOS för sparade söknings frågor**

Koppla lagrings konto för *fråga* till din arbets yta – *sparade – sökningar* frågor sparas i ditt lagrings konto. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

Efter konfigurationen sparas alla nya *sparade Sök* frågor i ditt lagrings utrymme.

**Konfigurera BYOS för logg aviserings frågor**

Koppla lagrings konto för *aviseringar* till din arbets yta – *logg aviserings* frågor sparas i ditt lagrings konto. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-03-01-preview
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

Efter konfigurationen sparas alla nya aviserings frågor i din lagrings plats.

## <a name="cmk-management"></a>Hantering av CMK

- **Hämta alla *kluster* resurser för en resurs grupp**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Response**
  
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
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  Samma svar som för "*kluster* resurser för en resurs grupp", men i prenumerations omfånget.

- **Uppdatera *kapacitets reservationen* i *kluster* resursen**

  När data volymen till dina associerade arbets ytor ändras med tiden och du vill uppdatera kapacitets reservations nivån korrekt. Följ [uppdaterings *kluster* resursen](#update-cluster-resource-with-key-identifier-details) och ange ditt nya kapacitets värde. Det kan vara mellan 1000 och 3000 GB per dag och i steg om 100. För högre nivå än 3000 GB per dag når du din Microsoft-kontakt för att aktivera den. Observera att du inte behöver ange en fullständig REST-begäran, men bör inkludera SKU: n:

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

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

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Ta bort koppling till arbets yta**

  Du behöver Skriv behörighet för arbets ytan och *kluster* resursen för att utföra den här åtgärden. Du kan när som helst ta bort en arbets yta från *kluster* resursen. Nya inmatade data efter borttagningen av associationen lagras i Log Analytics lagring och krypteras med Microsoft-nyckel. Du kan fråga dig om data som har matats in till din arbets yta innan och efter associationen sömlöst så länge som *kluster* resursen är etablerad och konfigurerad med giltig Key Vault nyckel.

  Den här åtgärden är asynkron och kan vara en stund att slutföra.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK och rubrik.

  Inmatade data efter att den avassocierings åtgärd har lagrats i Log Analytics lagring, det kan ta 90 minuter att slutföra. Du kan kontrol lera status för arbets ytans associering på två sätt:

  1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
  2. Skicka en [arbets yta – get](/rest/api/loganalytics/workspaces/get) -begäran och Observera att svaret, den Avassocierade arbets ytan inte har *clusterResourceId* under *funktioner*.

- **Kontrol lera Association status för arbets ytan**
  
  Utför åtgärden Hämta på arbets ytan och observera om egenskapen *clusterResourceId* finns i svaret under *funktioner*. Den associerade arbets ytan kommer att ha *clusterResourceId* -egenskapen.

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Ta bort *kluster* resursen**

  Du behöver Skriv behörighet för *kluster* resursen för att utföra den här åtgärden. En mjuk borttagnings åtgärd utförs för att tillåta återställning av *kluster* resursen, inklusive dess data inom 14 dagar, om borttagningen var oavsiktlig eller avsiktlig. *Kluster* resurs namnet är reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Efter den mjuka borttagnings perioden släpps *kluster* resurs namnet, *kluster* resursen och data tas bort permanent och går inte att återställa. En associerad arbets yta kopplas bort från *kluster* resursen vid borttagnings åtgärden. Nya inmatade data lagras i Log Analytics lagring och krypteras med Microsoft-nyckel. 
  
  Den Avassocierade arbets ytans åtgärd är asynkron och kan ta upp till 90 minuter att slutföra.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK

- **Återställa *kluster* resursen och dina data** 
  
  En *kluster* resurs som togs bort under de senaste 14 dagarna är i läget för tyst Borttagning och kan återställas med sina data. Eftersom alla arbets ytor har kopplats bort från *kluster* resursen med *kluster* resurs borttagning måste du associera dina arbets ytor igen efter ÅTERställningen för CMK-kryptering. Återställnings åtgärden utförs manuellt av produkt gruppen för närvarande. Använd din Microsoft-kanal för återställnings begär Anden.

## <a name="limitationsandconstraints"></a>Begränsningar och begränsningar

- CMK stöds på dedikerade Log Analytics kluster och lämpar sig för kunder som skickar 1 TB per dag eller mer.

- Det maximala antalet *kluster* resurser per region och prenumeration är 2

- Du kan koppla en arbets yta till *kluster* resursen och sedan ta bort associationen om CMK inte krävs för arbets ytan. Antalet associationer för arbets ytan på en viss arbets yta under en period på 30 dagar är begränsat till 2

- Arbets ytans koppling till *kluster* resurs ska endast utföras när du har kontrollerat att Log Analytics kluster etableringen har slutförts. Data som skickas till din arbets yta innan slut för Ande kommer att tas bort och går inte att återskapa.

- CMK-kryptering gäller nyligen inmatade data efter CMK-konfigurationen. Data som matats in före CMK-konfigurationen förblir krypterade med Microsoft-nyckeln. Du kan fråga efter data som matats in före och efter CMK-konfigurationen sömlöst.

- Azure Key Vault måste konfigureras som återställnings Bart. Dessa egenskaper är inte aktiverade som standard och ska konfigureras med CLI eller PowerShell:<br>
  - [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
  - [Rensnings skyddet](../../key-vault/general/soft-delete-overview.md#purge-protection) bör vara aktiverat för att skydda mot Force borttagning av hemligheten/valvet även efter mjuk borttagning.

- Det finns för närvarande inte stöd för att flytta *kluster* resurser till en annan resurs grupp eller prenumeration.

- Dina Azure Key Vault, *kluster* resursen och de associerade arbets ytorna måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer.

- Associationen för arbets ytan till *kluster* resursen kommer inte att fungera om den är kopplad till en annan *kluster* resurs

## <a name="troubleshooting"></a>Felsökning

- Beteende med Key Vault tillgänglighet
  - I normal drift – Storage cache-AEK under korta tids perioder och går tillbaka till Key Vault för att packa med jämna mellanrum.
    
  - Tillfälliga anslutnings fel – lagring hanterar tillfälliga fel (timeout, anslutnings fel, DNS-problem) genom att tillåta att nycklar hålls kvar i cacheminnet för kort, samtidigt som den överkommer till alla små signaler i tillgänglighet. Funktionerna för fråga och inmatning fortsätter utan avbrott.
    
  - Live site – otillgänglig cirka 30 minuter kommer lagrings kontot att bli otillgängligt. Fråge funktionen är inte tillgänglig och inmatade data cachelagras i flera timmar med Microsoft-nyckeln för att undvika data förlust. När åtkomsten till Key Vault återställs blir frågan tillgänglig och temporära cachelagrade data matas in i data lagringen och krypteras med CMK.

  - Key Vault åtkomst frekvens – den frekvens som Azure Monitor lagrings åtkomst Key Vault för omslutning och unwrap-åtgärder är mellan 6 och 60 sekunder.

- Om du skapar en *kluster* resurs och anger KeyVaultProperties omedelbart kan åtgärden Miss lyckas eftersom det inte går att definiera åtkomst principen förrän system identiteten har tilldelats till *kluster* resursen.

- Om du uppdaterar en befintlig *kluster* resurs med KeyVaultProperties och get nyckel åtkomst principen saknas i Key Vault, Miss kommer åtgärden.

- Om du får ett konflikt fel när du skapar en *kluster* resurs – det kan bero på att du har tagit bort *kluster* resursen under de senaste 14 dagarna och att den är i en mjuk borttagnings period. *Kluster* resurs namnet är reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Namnet släpps efter den mjuka borttagnings perioden när *kluster* resursen tas bort permanent.

- Om du uppdaterar *kluster* resursen medan en åtgärd pågår, fungerar inte åtgärden.

- Om du inte kan distribuera *kluster* resursen kontrollerar du att Azure Key Vault, *kluster*   resursen och tillhör ande Log Analytics arbets ytor finns i samma region. Kan finnas i olika prenumerationer.

- Om du uppdaterar din nyckel version i Key Vault och inte uppdaterar informationen om den nya nyckel identifieraren i *kluster* resursen, fortsätter Log Analytics klustret att använda din tidigare nyckel och dina data blir otillgängliga. Uppdatera informationen om den nya nyckel identifieraren i *kluster* resursen för att återuppta data inmatning och möjlighet att fråga data.

- Vissa åtgärder är långa och kan ta en stund att slutföra – dessa är *kluster* skapa, *kluster* nyckel uppdatering och *kluster* borttagning. Du kan kontrol lera åtgärds statusen på två sätt:
  1. När du använder REST kopierar du URL-värdet för Azure-AsyncOperation från svaret och följer [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
  2. Skicka GET-begäran till *kluster* eller arbets yta och observera svaret. Till exempel har inte den kopplade arbets ytan *clusterResourceId* under *funktioner*.

- För support och hjälp som är relaterat till kund Managed Key använder du dina kontakter i Microsoft.

- Felmeddelanden
  
  Skapa *kluster* resurs:
  -  400--kluster namnet är ogiltigt. Kluster namnet kan innehålla tecknen a – z, A-Z, 0-9 och längden 3-63.
  -  400 – bröd texten i begäran är null eller i felaktigt format.
  -  400--SKU-namnet är ogiltigt. Ange SKU-namn till capacityReservation.
  -  400--kapaciteten tillhandahölls men SKU: n är inte capacityReservation. Ange SKU-namn till capacityReservation.
  -  400--kapacitet saknas i SKU. Ange kapacitets värde till 1000 eller högre i steg om 100 (GB).
  -  400--kapacitet i SKU är inte inom intervallet. Bör vara minst 1000 och upp till den högsta tillåtna kapaciteten som är tillgänglig under "användning och uppskattad kostnad" i din arbets yta.
  -  400 – kapaciteten är låst i 30 dagar. Minskning av kapaciteten tillåts 30 dagar efter uppdateringen.
  -  400--inget SKU har angetts. Ange SKU-namnet till capacityReservation och kapacitets värde till 1000 eller högre i steg om 100 (GB).
  -  400--identiteten är null eller tom. Ange identitet med systemAssigned-typ.
  -  400--KeyVaultProperties anges när de skapas. Uppdatera KeyVaultProperties när klustret har skapats.
  -  400--åtgärden kan inte utföras nu. Den asynkrona åtgärden är i ett annat tillstånd än lyckades. Klustret måste utföra åtgärden innan en uppdaterings åtgärd utförs.

  Uppdatering av *kluster* resurs
  -  400--klustrets status tas bort. En asynkron åtgärd pågår. Klustret måste utföra åtgärden innan en uppdaterings åtgärd utförs.
  -  400--KeyVaultProperties är inte tomt men har ett felaktigt format. Se [uppdatering av nyckel identifierare](#update-cluster-resource-with-key-identifier-details).
  -  400--det gick inte att verifiera nyckeln i Key Vault. Kan bero på otillräckliga behörigheter eller när nyckeln inte finns. Kontrol lera att du [ställer in nyckel-och åtkomst principer](#grant-key-vault-permissions) i Key Vault.
  -  400--det går inte att återskapa nyckeln. Key Vault måste anges som mjuk borttagning och rensnings skydd. Se [Key Vault-dokumentation](../../key-vault/general/soft-delete-overview.md)
  -  400--åtgärden kan inte utföras nu. Vänta tills den asynkrona åtgärden har slutförts och försök igen.
  -  400--klustrets status tas bort. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

    *Kluster* resurs hämtning:
    -  404--det gick inte att hitta klustret, klustret kan ha tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den eller använda ett annat namn för att skapa ett nytt kluster. 

  Ta bort *kluster* resurs
    -  409--det går inte att ta bort ett kluster i etablerings tillstånd. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

  Association för arbets yta:
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409--arbets ytans associering eller en borttagnings åtgärd.
  -  400--det gick inte att hitta klustret, det kluster du angav finns inte eller har tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den.

  Association för arbets yta:
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409--arbets ytans associering eller en borttagnings åtgärd.
