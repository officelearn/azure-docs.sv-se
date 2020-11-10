---
title: Kundhanterad nyckel i Azure Monitor
description: Information och steg för att konfigurera Customer-Managed nyckel för att kryptera data i Log Analytics arbets ytor med hjälp av en Azure Key Vault nyckel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/09/2020
ms.openlocfilehash: 62621a36955808ec3f2c796681fe660e6e8524bc
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443389"
---
# <a name="azure-monitor-customer-managed-key"></a>Kundhanterad nyckel i Azure Monitor 

Den här artikeln innehåller bakgrunds information och steg för att konfigurera Kundhanterade nycklar för dina Log Analytics-arbetsytor. När de har kon figurer ATS krypteras alla data som skickas till dina arbets ytor med din Azure Key Vault nyckel.

Vi rekommenderar att du granskar [begränsningar och](#limitationsandconstraints) begränsningar nedan före konfigurationen.

## <a name="customer-managed-key-overview"></a>Översikt över kundhanterad nyckel

[Kryptering i vila](../../security/fundamentals/encryption-atrest.md) är ett gemensamt sekretess-och säkerhets krav i organisationer. Du kan låta Azure helt hantera kryptering i vila, medan du har olika alternativ för att hantera krypterings-eller krypterings nycklar.

Azure Monitor säkerställer att alla data och sparade frågor krypteras i vila med hjälp av Microsoft-hanterade nycklar (MMK). Azure Monitor också ett alternativ för kryptering med hjälp av din egen nyckel som lagras i [Azure Key Vault](../../key-vault/general/overview.md) och som används av lagring för data kryptering. Nyckeln kan vara antingen [program vara eller maskin vara-HSM skyddad](../../key-vault/general/overview.md). Azure Monitor krypterings användningen är identisk med hur [Azure Storage kryptering](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) fungerar.

Den Kundhanterade nyckel kapaciteten levereras på dedikerade Log Analytics kluster. Det gör att du kan skydda dina data med [Lås](#customer-lockbox-preview) kontrollen och ger dig kontrollen att återkalla åtkomsten till dina data när du vill. Data som matats in under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett kundhanterad nyckel konfiguration, men kontrollen över SSD-data följer [nyckel återkallning](#key-revocation). Vi arbetar med att ha SSD-data krypterade med Customer-Managed Key i första hälften av 2021.

[Pris modellen Log Analytics kluster](./manage-cost-storage.md#log-analytics-dedicated-clusters) använder kapacitets reservationer som börjar med en 1000 GB/dag-nivå.

> [!IMPORTANT]
> På grund av tillfälliga kapacitets begränsningar kräver vi att du förregistrerar dig innan du skapar ett kluster. Använd dina kontakter i Microsoft eller öppna support förfrågan för att registrera dina prenumerations-ID: n.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Så här fungerar Customer-Managed-nyckeln i Azure Monitor

Azure Monitor utnyttjar systemtilldelad hanterad identitet för att ge åtkomst till din Azure Key Vault. Systemtilldelad hanterad identitet kan bara kopplas till en enda Azure-resurs medan identiteten för Log Analytics-klustret stöds på kluster nivå – detta innebär att kapaciteten levereras på ett dedikerat Log Analytics kluster. För att stödja Customer-Managed nyckel på flera arbets ytor fungerar en ny Log Analytics *kluster* resurs som en mellanliggande identitets anslutning mellan dina Key Vault och dina Log Analytics arbets ytor. Den Log Analytics kluster lagringen använder den hanterade identitet som \' är associerad med *kluster* resursen för att autentisera till din Azure Key Vault via Azure Active Directory. 

Efter konfigurationen krypteras alla data som matats in till arbets ytor som är länkade till ditt dedikerade kluster med din nyckel i Key Vault. Du kan när som helst avlänka arbets ytor från klustret. Nya data hämtas sedan till Log Analytics lagring och krypteras med Microsoft-nyckel, medan du kan fråga dina nya och gamla data sömlöst.


![Översikt över Customer-Managed nyckel](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics *kluster* resurs som har hanterad identitet med behörighet att Key Vault--identiteten sprids till Underlay dedikerade Log Analytics kluster lagring
3. Dedikerat Log Analytics kluster
4. Arbets ytor som är länkade till *kluster* resursen 

## <a name="encryption-keys-operation"></a>Åtgärd för krypterings nycklar

Det finns tre typer av nycklar som ingår i kryptering av lagrings data:

- Nyckel krypterings nyckel för **KEK** (din Customer-Managed nyckel)
- Krypterings nyckel för **AEK** -konto
- **DEK** – data krypterings nyckel

Följande regler gäller:

- Log Analytics kluster lagrings konton genererar unik krypterings nyckel för varje lagrings konto, vilket kallas för AEK.
- AEK används för att härleda DEKs, vilket är de nycklar som används för att kryptera varje data block som skrivs till disk.
- När du konfigurerar din nyckel i Key Vault och refererar till den i klustret, Azure Storage skickar begär anden till din Azure Key Vault att omsluta och packa upp AEK för att utföra data kryptering och dekrypterings åtgärder.
- Din KEK lämnar aldrig Key Vault och om det finns en HSM-nyckel lämnar den aldrig maskin varan.
- Azure Storage använder den hanterade identitet som är kopplad till *kluster* resursen för att autentisera och komma åt Azure Key Vault via Azure Active Directory.

## <a name="customer-managed-key-provisioning-procedure"></a>Etablerings procedur för Customer-Manageds nyckel

1. Registrera din prenumeration så att klustret kan skapas
1. Skapa Azure Key Vault och lagra nyckel
1. Kluster skapas
1. Bevilja behörighet till din Key Vault
1. Länkar Log Analytics arbets ytor

Customer-Managed nyckel konfigurationen stöds inte i Azure Portal och etableringen utförs via [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/)-, [CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics) -eller [rest](https://docs.microsoft.com/rest/api/loganalytics/) -begäranden.

### <a name="asynchronous-operations-and-status-check"></a>Asynkrona åtgärder och status kontroll

Vissa konfigurations steg körs asynkront eftersom de inte kan slutföras snabbt. När du använder REST-begäranden i konfigurationen returnerar svaret från början en HTTP-statuskod 200 (OK) och rubriken med *Azure-AsyncOperation* -egenskapen när den godkänns:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Sedan kan du kontrol lera statusen för den asynkrona åtgärden genom att skicka en GET-begäran till värdet för *Azure-AsyncOperation-* huvudet:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
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

Kluster borttagning pågår – när du tar bort ett kluster som har länkade arbets ytor utförs borttagningen av länkar för varje arbets yta asynkront och åtgärden kan ta en stund.
Detta är inte relevant när du tar bort ett kluster utan en länkad arbets yta – i det här fallet tas klustret bort omedelbart.
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

### <a name="allowing-subscription"></a>Tillåter prenumeration

> [!IMPORTANT]
> Customer-Managed nyckel kapaciteten är regional. Dina Azure Key Vault-, kluster-och länkade Log Analytics-arbetsytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

### <a name="storing-encryption-key-kek"></a>Lagra krypterings nyckel (KEK)

Skapa eller Använd en Azure Key Vault som du redan har för att skapa eller importera en nyckel som ska användas för data kryptering. Azure Key Vault måste konfigureras som rekonstruerbar för att skydda din nyckel och åtkomsten till dina data i Azure Monitor. Du kan kontrol lera den här konfigurationen under egenskaper i Key Vault, både *mjuk borttagnings* -och *rensnings skydd* ska vara aktiverat.

![Mjuk borttagning och rensning av skydds inställningar](media/customer-managed-keys/soft-purge-protection.png)

De här inställningarna kan uppdateras i Key Vault via CLI och PowerShell:

- [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
- [Rensa skydds skydd](../../key-vault/general/soft-delete-overview.md#purge-protection) mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

### <a name="create-cluster"></a>Skapa kluster

Följ proceduren som illustreras i [artikeln om dedikerade kluster](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster). 

> [!IMPORTANT]
> Kopiera och spara svaret eftersom du kommer att behöva informationen i nästa steg.

### <a name="grant-key-vault-permissions"></a>Bevilja Key Vault behörigheter

Skapa åtkomst princip i Key Vault för att bevilja behörighet till klustret. Dessa behörigheter används av Underlay Azure Monitor Storage för data kryptering. Öppna din Key Vault i Azure Portal och klicka på "åtkomst principer" och sedan "+ Lägg till åtkomst princip" för att skapa en princip med följande inställningar:

- Nyckel behörigheter: Välj get-, wrap-och unwrap Key-behörigheter.
- Välj huvud namn: Ange det kluster namn eller det huvud-ID-värde som returnerades i svaret i föregående steg.

![bevilja Key Vault behörigheter](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Get-behörighet krävs för att verifiera att Key Vault har kon figurer ATS för att kunna *återskapas* för att skydda din nyckel och till gång till dina Azure Monitor data.

### <a name="update-cluster-with-key-identifier-details"></a>Uppdatera kluster med information om nyckel identifierare

Alla åtgärder i klustret måste ha behörighet för Microsoft. OperationalInsights/kluster/Skriv åtgärd. Den här behörigheten kan beviljas via den ägare eller deltagare som innehåller *åtgärden/Write eller via rollen Log Analytics Contributor som innehåller Microsoft. OperationalInsights/* action.

Det här steget uppdaterar Azure Monitor lagring med den nyckel och version som ska användas för data kryptering. När den uppdateras används den nya nyckeln för att omsluta och packa upp lagrings nyckeln (AEK).

Välj den aktuella versionen av nyckeln i Azure Key Vault för att hämta information om nyckel identifieraren.

![Bevilja Key Vault behörigheter](media/customer-managed-keys/key-identifier-8bit.png)

Uppdatera KeyVaultProperties i kluster med information om nyckel identifierare.

Åtgärden är asynkron och kan ta en stund att slutföra.

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name"?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Response**

Det tar några minuter att slutföra spridningen av nyckel identifieraren. Du kan kontrol lera uppdaterings statusen på två sätt:
1. Kopiera Azure-AsyncOperation URL-värdet från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran i klustret och titta på egenskaperna för *KeyVaultProperties* . Den senast uppdaterade informationen om nyckel identifieraren ska returneras i svaret.

Ett svar på GET-begäran bör se ut så här när nyckel identifierarens uppdatering är slutförd: 200 OK och sidhuvud
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

### <a name="link-workspace-to-cluster"></a>Länka arbets ytan till kluster

Du måste ha Skriv behörighet till både din arbets yta och ditt kluster för att kunna utföra den här åtgärden, bland annat följande åtgärder:

- På arbets ytan: Microsoft. OperationalInsights/arbets ytor/Write
- I kluster: Microsoft. OperationalInsights/kluster/Write

> [!IMPORTANT]
> Det här steget bör endast utföras när Log Analytics kluster etableringen har slutförts. Om du länkar arbets ytor och matar in data före etableringen tas inmatade data bort och går inte att återskapa.

Den här åtgärden är asynkron och kan vara en stund att slutföra.

Följ proceduren som illustreras i [artikeln om dedikerade kluster](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster).

## <a name="key-revocation"></a>Återkallande av nyckel

Du kan återkalla åtkomsten till data genom att inaktivera nyckeln eller ta bort klustrets åtkomst princip i Key Vault. Kluster lagringen för Log Analytics kommer alltid att respektera ändringar i nyckel behörigheter inom en timme eller tidigare och lagringen kommer att bli otillgänglig. Alla nya data som matas in till arbets ytor som är länkade till klustret tas bort och kan inte återställas, inga data är tillgängliga och frågor till dessa arbets ytor fungerar inte. Tidigare inmatade data finns kvar i lagrings utrymmet så länge klustret och arbets ytorna inte har tagits bort. Otillgängliga data regleras av data bevarande principen och kommer att rensas när kvarhållning har nåtts. 

Inmatade data under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Detta tas bort vid nyckel återkallnings åtgärden och blir oåtkomlig även.

Lagringen avsöker regelbundet Key Vault för att försöka att packa upp krypterings nyckeln och en gång till, så fortsätter data inmatningen och frågan att återupptas inom 30 minuter.

## <a name="key-rotation"></a>Nyckelrotation

Customer-Managed Key rotation kräver en explicit uppdatering av klustret med den nya nyckel versionen i Azure Key Vault. Följ anvisningarna i steget uppdatera kluster med information om nyckel identifierare. Om du inte uppdaterar den nya informationen om nyckel identifierare i klustret, fortsätter den Log Analytics kluster lagringen att använda din tidigare nyckel för kryptering. Om du inaktiverar eller tar bort din gamla nyckel innan du uppdaterar den nya nyckeln i klustret kommer du att få statusen för [nyckel återkallning](#key-revocation) .

Alla dina data är tillgängliga efter nyckel rotations åtgärden, eftersom data alltid krypteras med konto krypterings nyckeln (AEK) medan AEK nu krypteras med din nya KEK-version (Key Encryption Key) i Key Vault.

## <a name="customer-managed-key-for-queries"></a>Customer-Managed nyckel för frågor

Frågespråket som används i Log Analytics är lättfattliga programspecifika och kan innehålla känslig information i kommentarer som du lägger till i frågor eller i frågesyntaxen. Vissa organisationer kräver att sådan information hålls skyddad under Customer-Managed nyckel principen och du måste spara dina frågor som är krypterade med din nyckel. Med Azure Monitor kan du lagra *sparade sökningar* och *Logga aviserings* frågor som är krypterade med din nyckel i ditt eget lagrings konto när du är ansluten till din arbets yta. 

> [!NOTE]
> Log Analytics frågor kan sparas i olika butiker beroende på vilket scenario som används. Frågorna förblir krypterade med Microsoft Key (MMK) i följande scenarier, oavsett Customer-Managed nyckel konfiguration: arbets böcker i Azure Monitor, Azure-instrumentpaneler, Azure Logic app, Azure Notebooks och Automation-runbooks.

När du tar med din egen lagring (BYOS) och länkar den till din arbets yta överförs frågor till ditt lagrings konto via tjänsten för *sparade sökningar* och *logg aviseringar* . Det innebär att du styr lagrings kontot och [principen för kryptering vid vila](../../storage/common/customer-managed-keys-overview.md) antingen med samma nyckel som du använder för att kryptera data i Log Analytics kluster eller en annan nyckel. Du kommer dock att vara ansvarig för kostnaderna som är kopplade till det lagrings kontot. 

**Att tänka på innan du ställer in Customer-Managed nyckel för frågor**
* Du måste ha Skriv behörighet till både din arbets yta och ditt lagrings konto
* Se till att skapa ditt lagrings konto i samma region som din Log Analytics arbets yta finns
* *Spara sökningar* i lagring anses som tjänst artefakter och deras format kan ändras
* Befintliga *sparade sökningar* tas bort från din arbets yta. Kopiera och *Spara sökningar* som du behöver före konfigurationen. Du kan visa dina *sparade sökningar* med  [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* Frågans historik stöds inte och du kommer inte att kunna se frågor som du körde
* Du kan länka ett enskilt lagrings konto till arbets ytan för att kunna spara frågor, men det kan användas för både *sparade sökningar* och *logg aviserings* frågor
* Fäst på instrument panelen stöds inte

**Konfigurera BYOS för sparade söknings frågor**

Länka ett lagrings konto för *fråga* till din arbets yta – *sparade – sökningar* frågor sparas i ditt lagrings konto. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
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

Länka ett lagrings konto för *aviseringar* till arbets ytan – *logg aviserings* frågor sparas i ditt lagrings konto. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
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

## <a name="customer-lockbox-preview"></a>Customer Lockbox (för hands version)
Med lås får du kontrollen att godkänna eller avvisa Microsoft Engineer-begäran för att få åtkomst till dina data under en support förfrågan.

I Azure Monitor har du den här kontrollen på data i arbets ytor som är länkade till ditt Log Analytics-dedikerade kluster. Den säkra kontrollen gäller för data som lagras i ett Log Analytics dedikerat kluster där den hålls isolerad i klustrets lagrings konton under den skyddade din säkra prenumeration.  

Läs mer om [Customer lockbox för Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed nyckel åtgärder

- **Hämta alla kluster i en resurs grupp**
  
  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
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

- **Hämta alla kluster i en prenumeration**

  ```azurecli
  az monitor log-analytics cluster list
  ```

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  Samma svar som för "kluster i en resurs grupp", men i prenumerations omfånget.

- **Uppdatera *kapacitets reservation* i kluster**

  När data volymen till dina länkade arbets ytor ändras med tiden och du vill uppdatera kapacitets reservations nivån korrekt. Följ [uppdaterings klustret](#update-cluster-with-key-identifier-details) och ange ditt nya kapacitets värde. Det kan vara mellan 1000 och 3000 GB per dag och i steg om 100. För högre nivå än 3000 GB per dag når du din Microsoft-kontakt för att aktivera den. Observera att du inte behöver ange en fullständig REST-begäran, men bör inkludera SKU: n:

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

- **Uppdatera *billingType* i kluster**

  Egenskapen *billingType* bestämmer fakturerings behörighet för klustret och dess data:
  - *kluster* (standard)--faktureringen tillskrivs till den prenumeration som är värd för kluster resursen
  - *arbets ytor* – faktureringen hänförs till prenumerationerna som är värdar för dina arbets ytor proportionellt
  
  Följ [uppdaterings klustret](#update-cluster-with-key-identifier-details) och ange det nya billingType-värdet. Observera att du inte behöver ange den fullständiga texten i REST-begäran och ska innehålla *billingType* :

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Ta bort arbetsytans länk**

  Du behöver Skriv-behörigheter på arbets ytan och klustret för att utföra den här åtgärden. Du kan när som helst ta bort länken till en arbets yta från klustret. Nya inmatade data efter åtgärden ta bort länk lagras i Log Analytics lagring och krypteras med Microsoft-nyckel. Du kan fråga dig om data som har matats in till din arbets yta före och efter att bryta länken sömlöst så länge klustret är etablerade och konfigurerat med giltig Key Vault nyckel.

  Den här åtgärden är asynkron och kan vara en stund att slutföra.

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  - **Kontrol lera länk status för arbets ytan**
  
  Utför åtgärden Hämta på arbets ytan och observera om egenskapen *clusterResourceId* finns i svaret under *funktioner*. En länkad arbets yta kommer att ha egenskapen *clusterResourceId* .

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Ta bort klustret**

  Du behöver Skriv behörighet för klustret för att utföra den här åtgärden. En mjuk borttagnings åtgärd utförs för att tillåta återställning av klustret, inklusive data inom 14 dagar, om borttagningen var oavsiktlig eller avsiktlig. Kluster namnet förblir reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Efter den mjuka borttagnings perioden släpps kluster namnet och klustret och dess data tas bort permanent och går inte att återställa. En länkad arbets yta tas bort från klustret vid borttagnings åtgärden. Nya inmatade data lagras i Log Analytics lagring och krypteras med Microsoft-nyckel. 
  
  Åtgärden för att ta bort länken är asynkron och kan ta upp till 90 minuter att slutföra.

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```
 
  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
  
- **Återställa ditt kluster och dina data** 
  
  Ett kluster som har tagits bort under de senaste 14 dagarna är i läget för mjuk borttagning och kan återställas med data. Eftersom alla arbets ytor har tagit bort kopplingen från klustret, måste du länka om arbets ytorna efter klustrets återställning. Återställnings åtgärden utförs för närvarande manuellt av produkt gruppen. Använd din Microsoft-kanal eller öppna support förfrågan om återställning av borttaget kluster.

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

- Customer-Managed nyckel stöds på dedikerade Log Analytics kluster och är lämpligt för kunder som skickar 1 TB per dag eller mer.

- Det högsta antalet kluster per region och prenumeration är 2

- Det maximala antalet länkade arbets ytor till klustret är 1000

- Du kan länka en arbets yta till klustret och sedan ta bort länken till den. Antalet länk åtgärder på arbets ytan på en viss arbets yta är begränsat till 2 under en period på 30 dagar.

- Arbets ytans länk till kluster ska endast utföras när du har kontrollerat att Log Analytics kluster etableringen har slutförts. Data som skickas till din arbets yta innan slut för Ande kommer att tas bort och går inte att återskapa.

- Customer-Managed nyckel kryptering gäller för nyligen inmatade data efter konfigurations tiden. Data som matats in före konfigurationen är fortfarande krypterade med Microsoft-nyckeln. Du kan fråga efter data som matats in före och efter Customer-Managed nyckel konfigurationen sömlöst.

- Azure Key Vault måste konfigureras som återställnings Bart. Dessa egenskaper är inte aktiverade som standard och ska konfigureras med CLI eller PowerShell:<br>
  - [Mjuk borttagning](../../key-vault/general/soft-delete-overview.md)
  - [Rensnings skyddet](../../key-vault/general/soft-delete-overview.md#purge-protection) bör vara aktiverat för att skydda mot Force borttagning av hemligheten/valvet även efter mjuk borttagning.

- Det finns för närvarande inte stöd för att flytta kluster till en annan resurs grupp eller prenumeration.

- Dina Azure Key Vault-, kluster-och länkade arbets ytor måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer.

- Arbets ytans länk till klustret Miss fungerar om den är länkad till ett annat kluster.

## <a name="troubleshooting"></a>Felsökning

- Beteende med Key Vault tillgänglighet
  - I normal drift – Storage cache-AEK under korta tids perioder och går tillbaka till Key Vault för att packa med jämna mellanrum.
    
  - Tillfälliga anslutnings fel – lagring hanterar tillfälliga fel (timeout, anslutnings fel, DNS-problem) genom att tillåta att nycklar hålls kvar i cacheminnet för kort, samtidigt som den överkommer till alla små signaler i tillgänglighet. Funktionerna för fråga och inmatning fortsätter utan avbrott.
    
  - Live site – otillgänglig cirka 30 minuter kommer lagrings kontot att bli otillgängligt. Fråge funktionen är inte tillgänglig och inmatade data cachelagras i flera timmar med Microsoft-nyckeln för att undvika data förlust. När åtkomsten till Key Vault återställs blir frågan tillgänglig och temporära cachelagrade data matas in i data lagringen och krypteras med Customer-Managed nyckel.

  - Key Vault åtkomst frekvens – den frekvens som Azure Monitor lagrings åtkomst Key Vault för omslutning och unwrap-åtgärder är mellan 6 och 60 sekunder.

- Om du skapar ett kluster och anger KeyVaultProperties omedelbart kan åtgärden Miss lyckas eftersom det inte går att definiera åtkomst principen förrän system identiteten tilldelas till klustret.

- Om du uppdaterar ett befintligt kluster med KeyVaultProperties och get nyckel åtkomst principen saknas i Key Vault Miss kan åtgärden utföras.

- Om du får ett konflikt fel när du skapar ett kluster, kan det bero på att du har tagit bort klustret under de senaste 14 dagarna och att det är i en mjuk borttagnings period. Kluster namnet förblir reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Namnet släpps efter den mjuka borttagnings perioden när klustret tas bort permanent.

- Om du uppdaterar klustret medan en åtgärd pågår, fungerar inte åtgärden.

- Om du inte kan distribuera klustret kontrollerar du att arbets ytorna Azure Key Vault, Cluster och Linked Log Analytics finns i samma region. Kan finnas i olika prenumerationer.

- Om du uppdaterar din nyckel version i Key Vault och inte uppdaterar informationen om den nya nyckel identifieraren i klustret, kommer Log Analytics-klustret att fortsätta använda din tidigare nyckel och dina data blir otillgängliga. Uppdatera informationen om den nya nyckel identifieraren i klustret för att återuppta data inmatning och möjlighet att fråga data.

- Vissa åtgärder är långa och kan ta en stund att slutföra – dessa är kluster skapa, kluster nyckel uppdatering och kluster borttagning. Du kan kontrol lera åtgärds statusen på två sätt:
  1. När du använder REST kopierar du Azure-AsyncOperation URL-värdet från svaret och följer [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
  2. Skicka GET-begäran till kluster eller arbets yta och observera svaret. Till exempel kan inte en länkad arbets yta ha *clusterResourceId* under *funktioner*.

- För support och hjälp som är relaterat till kund Managed Key använder du dina kontakter i Microsoft.

- Felmeddelanden
  
  Skapa kluster:
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

  Kluster uppdatering
  -  400--klustrets status tas bort. En asynkron åtgärd pågår. Klustret måste utföra åtgärden innan en uppdaterings åtgärd utförs.
  -  400--KeyVaultProperties är inte tomt men har ett felaktigt format. Se [uppdatering av nyckel identifierare](#update-cluster-with-key-identifier-details).
  -  400--det gick inte att verifiera nyckeln i Key Vault. Kan bero på otillräckliga behörigheter eller när nyckeln inte finns. Kontrol lera att du [ställer in nyckel-och åtkomst principer](#grant-key-vault-permissions) i Key Vault.
  -  400--det går inte att återskapa nyckeln. Key Vault måste anges som mjuk borttagning och rensnings skydd. Se [Key Vault-dokumentation](../../key-vault/general/soft-delete-overview.md)
  -  400--åtgärden kan inte utföras nu. Vänta tills den asynkrona åtgärden har slutförts och försök igen.
  -  400--klustrets status tas bort. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

  Hämta kluster:
    -  404--det gick inte att hitta klustret, klustret kan ha tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den eller använda ett annat namn för att skapa ett nytt kluster. 

  Ta bort kluster
    -  409--det går inte att ta bort ett kluster i etablerings tillstånd. Vänta tills den asynkrona åtgärden har slutförts och försök igen.

  Arbets ytans länk:
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409---arbets ytans länk eller ta bort länk i processen.
  -  400--det gick inte att hitta klustret, det kluster du angav finns inte eller har tagits bort. Om du försöker skapa ett kluster med det namnet och få en konflikt, är klustret mjuk borttagning i 14 dagar. Du kan kontakta supporten för att återställa den.

  Ta bort arbets yta:
  -  404--arbets ytan hittades inte. Den arbets yta du angav finns inte eller har tagits bort.
  -  409---arbets ytans länk eller ta bort länk i processen.
