---
title: Azure Monitor kundhanterad nyckel konfiguration
description: Information och steg för att konfigurera kundhanterad nyckel (CMK) för att kryptera data i dina Log Analytics arbets ytor med hjälp av en Azure Key Vault nyckel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758816"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor kundhanterad nyckel konfiguration 

Den här artikeln innehåller bakgrunds information och steg för att konfigurera Kundhanterade nycklar (CMK) för dina Log Analytics-arbetsytor och Application Insights-komponenter. När de har kon figurer ATS krypteras alla data som skickas till dina arbets ytor eller komponenter med din Azure Key Vault nyckel.

Vi rekommenderar att du granskar [begränsningar och](#limitations-and-constraints) begränsningar nedan före konfigurationen.

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- Azure Monitor CMK är en funktion för tidig åtkomst som är aktive rad för registrerade prenumerationer.

- CMK-distributionen som beskrivs i den här artikeln levereras i produktions kvalitet och stöds, även om det är en tidig åtkomst funktion.

- CMK-funktionen levereras på ett dedikerat data lager-kluster, som är ett Azure Datautforskaren-kluster (ADX) och är lämpligt för kunder som skickar 1 TB per dag eller mer. 

- Pris sättnings modellen för CMK är inte tillgänglig för närvarande och omfattas inte av den här artikeln. En pris modell för dedikerat ADX-kluster förväntas under det andra kvartalet av kalender året (CY) 2020 och gäller för alla befintliga CMK-distributioner.

- I den här artikeln beskrivs CMK-konfigurationen för Log Analytics-arbetsytor. CMK för Application Insights-komponenter stöds också med hjälp av den här artikeln och skillnaderna visas i bilagan.

> [!NOTE]
> Log Analytics och Application Insights använder samma plattform för data lagring och frågemotor.
> Vi samlar dessa två butiker genom att integrera Application Insights i Log Analytics för att skapa ett enda enhetligt logg lager under Azure Monitor. Den här ändringen planeras för det andra kvartalet för kalender året 2020. Om du inte behöver distribuera CMK för dina Application Insights data, rekommenderar vi att du väntar på att konsolideringen ska slutföras eftersom distributionerna avbryts av konsolideringen och du måste konfigurera om CMK efter migreringen till Log Analytics arbets yta. Minst 1 TB per dag gäller för kluster nivån och tills konsolideringen har slutförts under andra kvartalet, Application Insights och Log Analytics kräver separata kluster.

## <a name="customer-managed-key-cmk-overview"></a>Översikt över kundhanterad nyckel (CMK)

[Kryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) är ett gemensamt sekretess-och säkerhets krav i organisationer. Du kan låta Azure helt hantera kryptering i vila, medan du har olika alternativ för att hantera krypterings-eller krypterings nycklar.

Azure Monitor data lagringen säkerställer att alla data som krypteras i vila med hjälp av Azure-hanterade nycklar när de lagras i Azure Storage. Azure Monitor innehåller också ett alternativ för data kryptering med hjälp av din egen nyckel som lagras i din [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), som används med systemtilldelad autentisering med [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) . Den här nyckeln kan vara antingen [program vara eller maskin vara-HSM skyddad](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor användningen av kryptering är identisk med hur [Azure Storage kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) fungerar.

Den frekvens som Azure Monitor lagrings åtkomst Key Vault för omslutning och unwrap-åtgärder är mellan 6 och 60 sekunder.Azure Monitor Storage respekterar alltid ändringar i nyckel behörigheter inom en timme.

Inmatade data under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett CMK-konfiguration, men vi arbetar med att ha SSD krypterat med CMK i första hälften av 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Så här fungerar CMK i Azure Monitor

Azure Monitor utnyttjar systemtilldelad hanterad identitet för att ge åtkomst till din Azure Key Vault.Systemtilldelad hanterad identitet kan bara kopplas till en enda Azure-resurs. Identiteten för Azure Monitor data lager (ADX-kluster) stöds på kluster nivå och detta anger att CMK-funktionen levereras i ett dedikerat ADX-kluster. För att stödja CMK på flera arbets ytor fungerar en ny Log Analytics resurs (*kluster*) som en mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor. Det här konceptet följer den systemtilldelade identitets begränsningen och identiteten upprätthålls mellan ADX-klustret och Log Analytics *kluster* resurs, medan data för alla associerade arbets ytor skyddas med din Key Vault-nyckel. Underlay ADX kluster lagring använder den hanterade identitet som\'är kopplad till *kluster* resursen för att autentisera och komma åt din Azure Key Vault via Azure Active Directory.

![Översikt över CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Kundens Key Vault.
2.    Kundens Log Analytics *kluster* resurs har hanterad identitet med behörighet att Key Vault – identiteten stöds på nivån data lager (ADX Cluster).
3.    Azure Monitor dedikerat ADX-kluster.
4.    Kundens arbets ytor som är kopplade till *kluster* RESURSEN för CMK-kryptering.

## <a name="encryption-keys-management"></a>Hantering av krypterings nycklar

Det finns tre typer av nycklar som ingår i kryptering av lagrings data:

- Nyckel krypterings nyckel för **KEK** (CMK)
- Krypterings nyckel för **AEK** -konto
- **DEK** – data krypterings nyckel

Följande regler gäller:

- ADX lagrings konton genererar unik krypterings nyckel för varje lagrings konto, vilket kallas för AEK.

- AEK används för att härleda DEKs, vilket är de nycklar som används för att kryptera varje data block som skrivs till disk.

- När du konfigurerar din nyckel i Key Vault och refererar till den i *kluster* resursen, skickar Azure Storage begär anden till Azure Key Vault för att omsluta och packa upp AEK för att utföra data kryptering och dekryptering.

- Din KEK lämnar aldrig Key Vault och om det finns en HSM-nyckel lämnar den aldrig maskin varan.

- Azure Storage använder den hanterade identitet som är kopplad till *kluster* resursen för att autentisera och komma åt Azure Key Vault via Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Etablerings procedur för CMK

För Application Insights CMK-konfiguration följer du tillägget för innehållet i steg 3 och 6.

1. Prenumerations-vit listning – detta krävs för den här funktionen för tidig åtkomst
2. Skapa Azure Key Vault och lagra nyckel
3. Skapa en *kluster* resurs
4. Etablering av Azure Monitor data lager (ADX-kluster)
5. Bevilja behörighet till din Key Vault
6. Associera Log Analytics-arbetsytor

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

CMK-funktionen är en funktion för tidig åtkomst. De prenumerationer där du planerar att skapa *kluster* resurser måste vit listas i förväg av Azures produkt grupp. Använd dina kontakter i Microsoft för att tillhandahålla dina prenumerations-ID: n.

> [!IMPORTANT]
> CMK-funktionen är regional. Din Azure Key Vault, *kluster* resurs och associerade Log Analytics arbets ytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

### <a name="storing-encryption-key-kek"></a>Lagra krypterings nyckel (KEK)

Skapa eller Använd en Azure Key Vault som du redan har för att skapa eller importera en nyckel som ska användas för data kryptering. Azure Key Vault måste konfigureras som rekonstruerbar för att skydda din nyckel och åtkomsten till dina data i Azure Monitor. Du kan kontrol lera den här konfigurationen under egenskaper i Key Vault, både *mjuk borttagnings* -och *rensnings skydd* ska vara aktiverat.

![Mjuk borttagning och rensning av skydds inställningar](media/customer-managed-keys/soft-purge-protection.png)

De här inställningarna är tillgängliga via CLI och PowerShell:
- [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Rensa skydds skydd](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

### <a name="create-cluster-resource"></a>Skapa *kluster* resurs

Den här resursen används som en mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor. När du har fått en bekräftelse på att dina prenumerationer har vit listas skapar du en Log Analytics *kluster* resurs i den region där dina arbets ytor finns. Application Insights och Log Analytics kräver separata *kluster* resurs typer. *Kluster* resursens typ definieras när du skapar den genom att ange egenskapen *ClusterType* till antingen *LogAnalytics*eller *ApplicationInsights*. Kluster resurs typen kan inte ändras efter.

För Application Insights CMK-konfiguration följer du innehållet i bilagan.

Du måste ange kapacitets reservations nivå (SKU) när du skapar en *kluster* resurs. Kapacitets reservations nivån kan ligga inom intervallet 1 000 till 2 000 GB per dag och du kan uppdatera den i steg om 100 senare. Om du behöver kapacitets reservations nivån högre än 2 000 GB per dag kan du kontakta din Microsoft-kontakt för att aktivera den. Den här egenskapen påverkar inte faktureringen för närvarande – när pris modellen för dedikerat kluster införs, kommer faktureringen att gälla för alla befintliga CMK-distributioner.

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
Identiteten tilldelas till *kluster* resursen vid skapande tillfället.

**Svar**

200 OK och rubrik.
Under den tidigaste åtkomst perioden för funktionen, allokeras ADX-klustret manuellt. När den tar etablering av det ADX klustret a När ska slutföras, kan du kontrol lera etablerings statusen på två sätt:
1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *kluster* resursen och titta på *provisioningState* -värdet. Den är *ProvisioningAccount* medan *etableringen har slutförts och* slutförts.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Etablering av Azure Monitor data lager (ADX-kluster)

Under den tidigaste åtkomst perioden för funktionen, allokeras ADX-klustret manuellt av produkt teamet när föregående steg har slutförts. Använd Microsoft-kanalen för det här steget och ange ett *kluster* resurs svar. 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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

> [!Warning]
> Du måste ange en fullständig brödtext i *kluster* resurs uppdateringen som innehåller *identitet*, *SKU*, *KeyVaultProperties* och *plats*. Om du saknar *KeyVaultProperties* -information tas nyckel identifieraren bort från *kluster* resursen och [återkalls av nyckeln](#cmk-kek-revocation).

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

### <a name="workspace-association-to-cluster-resource"></a>Arbets ytans koppling till *kluster* resurs
För Application Insights CMK-konfiguration följer du tillägget för det här steget.

Du måste ha behörigheten "Skriv" till både din arbets yta och *kluster* resurs för att utföra den här åtgärden, bland annat följande åtgärder:

- På arbets ytan: Microsoft. OperationalInsights/arbets ytor/Write
- I *kluster* resurs: Microsoft. OperationalInsights/kluster/Write

> [!IMPORTANT]
> Det här steget ska endast utföras efter ADX kluster etablering. Om du associerar arbets ytor och matar in data före etableringen tas inmatade data bort och går inte att återskapa.

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

## <a name="cmk-kek-revocation"></a>CMK (KEK) åter kallelse

Du kan återkalla åtkomsten till dina data genom att inaktivera nyckeln eller ta bort *kluster* resurs åtkomst principen i Key Vault. Azure Monitor-lagringen kommer alltid att respektera ändringar i nyckel behörigheter inom en timme, vanligt vis snart, och lagring kommer att bli otillgänglig. Data som hämtas till arbets ytor som är associerade med din *kluster* resurs släpps och frågor kommer att Miss förväntas. Tidigare inmatade data är inte tillgängliga i Azure Monitor lagring så länge som du är *kluster* resurs och dina arbets ytor inte tas bort. Otillgängliga data regleras av data bevarande principen och kommer att rensas när kvarhållning har nåtts.

Lagringen avsöker regelbundet din Key Vault för att försöka att packa upp krypterings nyckeln och en gång till, men data inmatningen och frågan återupptas inom 30 minuter.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotation

Rotationen av CMK kräver en explicit uppdatering av *kluster* resursen med den nya nyckel versionen i Azure Key Vault. Om du vill uppdatera Azure Monitor med din nya nyckel version följer du anvisningarna i steget uppdatera *kluster* resurs med information om nyckel identifierare. Om du uppdaterar din nyckel version i Key Vault och inte uppdaterar informationen om den nya nyckel identifieraren i *kluster* resursen, kommer Azure Monitor Storage att fortsätta använda din tidigare nyckel.
Alla dina data är tillgängliga efter nyckel rotations åtgärden, inklusive data som matats in före rotationen och efter det, eftersom alla data förblir krypterade av konto krypterings nyckeln (AEK) medan AEK nu krypteras av din nya KEK-version (Key Encryption Key).

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

- Funktionen CMK stöds på ADX kluster nivå och kräver ett dedikerat Azure Monitor ADX-kluster med kravet att skicka 1 TB per dag eller mer.

- Det högsta antalet *kluster* resurser per prenumeration är begränsat till 2

- *Kluster* resurs associationen till arbets ytan ska endast utföras när du har verifierat att kluster ETABLERINGEN för ADX har slutförts. Data som skickas till din arbets yta innan etableringen slutförs kommer att tas bort och kan inte återskapas.

- CMK-kryptering gäller nyligen inmatade data efter CMK-konfigurationen. Data som matats in före CMK-konfigurationen förblir krypterade med Microsoft-nyckeln. Du kan fråga efter data som matats in före och efter CMK-konfigurationen sömlöst.

- Du kan ta bort en arbets yta från en *kluster* resurs när du bestämmer att CMK inte krävs för en viss arbets yta. Nya inmatade data efter det att associationen har lagrats i delad Log Analytics-lagring som den var innan den var kopplad till *kluster* resursen. Du kan fråga efter data som matats in före och efter att associationen är sömlös om *kluster* resursen är etablerad och konfigurerad med giltig Key Vault nyckel.

- Azure Key Vault måste konfigureras som återställnings Bart. Dessa egenskaper är inte aktiverade som standard och ska konfigureras med CLI eller PowerShell:

  - [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) måste aktive ras
  - [Rensnings skyddet](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) bör vara aktiverat för att skydda mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

- Application Insights och Log Analytics kräver separata *kluster* resurser. *Kluster* resursens typ definieras när du skapar den genom att ange egenskapen "clusterType" till antingen "LogAnalytics" eller "ApplicationInsights". Det går inte att ändra *kluster* resurs typen.

- Det finns för närvarande inte stöd för att flytta *kluster* resurser till en annan resurs grupp eller prenumeration.

- Dina Azure Key Vault, *kluster* resursen och de associerade arbets ytorna måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer.

- Associationen för arbets ytan till *kluster* resursen kommer inte att fungera om den är kopplad till en annan *kluster* resurs

## <a name="troubleshooting-and-management"></a>Fel sökning och hantering

- Key Vault tillgänglighet
    - I normal drift – Storage cache-AEK under korta tids perioder och går tillbaka till Key Vault för att packa med jämna mellanrum.
    
    - Tillfälliga anslutnings fel – lagring hanterar tillfälliga fel (timeout, anslutnings fel, DNS-problem) genom att tillåta att nycklar hålls kvar i cacheminnet för kort, samtidigt som den överkommer till alla små signaler i tillgänglighet. Funktionerna för fråga och inmatning fortsätter utan avbrott.
    
    - Live site – otillgänglig cirka 30 minuter kommer lagrings kontot att bli otillgängligt. Fråge funktionen är inte tillgänglig och inmatade data cachelagras i flera timmar med Microsoft-nyckeln för att undvika data förlust. När åtkomsten till Key Vault återställs blir frågan tillgänglig och temporära cachelagrade data matas in i data lagringen och krypteras med CMK.

- Om du skapar en *kluster* resurs och anger KeyVaultProperties omedelbart kan åtgärden Miss lyckas eftersom det inte går att definiera åtkomst principen förrän system identiteten har tilldelats till *kluster* resursen.

- Om du uppdaterar en befintlig *kluster* resurs med KeyVaultProperties och get nyckel åtkomst principen saknas i Key Vault, Miss kommer åtgärden.

- Om du försöker ta bort en *kluster* resurs som är kopplad till en arbets yta kommer borttagnings åtgärden att Miss förväntas.

- Om du får ett konflikt fel när du skapar en *kluster* resurs – det kan bero på att du har tagit bort *kluster* resursen under de senaste 14 dagarna och att den är i en mjuk borttagnings period. *Kluster* resurs namnet är reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Namnet släpps efter den mjuka borttagnings perioden när *kluster* resursen tas bort permanent.

- Hämta alla *kluster* resurser för en resurs grupp:

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

- Hämta alla *kluster* resurser för en prenumeration:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Svar**
    
  Samma svar som för "*kluster* resurser för en resurs grupp", men i prenumerations omfånget.

- Uppdatera *kapacitets reservationen* i *kluster* resurs – när data volymen till dina associerade arbets ytor ändras och du vill uppdatera kapacitets reservations nivån för fakturerings överväganden, följer du [uppdaterings *kluster* resursen](#update-cluster-resource-with-key-identifier-details) och anger ditt nya kapacitets värde. Kapacitets reservations nivån kan ligga inom intervallet 1 000 till 2 000 GB per dag och i steg om 100. För högre nivå än 2 000 GB per dag når du din Microsoft-kontakt för att aktivera den.

- Ta bort *kluster* resursen – en mjuk borttagnings åtgärd utförs för att tillåta återställning av *kluster* resursen, inklusive data inom 14 dagar, om borttagningen var oavsiktlig eller avsiktlig. *Kluster* resurs namnet är reserverat under den mjuka borttagnings perioden och du kan inte skapa ett nytt kluster med det namnet. Efter den mjuka borttagnings perioden släpps *kluster* resurs namnet, *kluster* resursen och data tas bort permanent och går inte att återställa. Alla associerade arbets ytor tas bort från *kluster* resursen vid borttagnings åtgärden. Nya inmatade data lagras i delad Log Analytics lagring och krypteras med Microsoft-nyckel. Den tillhör ande arbets ytan har en asynkron åtgärd.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Svar**

  200 OK

- Återställ *kluster* resursen och dina data – en *kluster* resurs som togs bort under de senaste 14 dagarna är i läget för mjuk borttagning och kan återställas. Detta utförs manuellt av produkt gruppen för närvarande. Använd din Microsoft-kanal för återställnings begär Anden.

## <a name="appendix"></a>Bilaga

Application Insights kundens hanterade nyckel (CMK) stöds också, men du bör överväga följande ändring för att hjälpa dig att planera distributionen av CMK för dina program insikter-komponenter.

Log Analytics och Application Insights använder samma plattform för data lagring och frågemotor. Vi samlar dessa två butiker genom att integrera Application Insights i Log Analytics för att tillhandahålla ett enda enhetligt logg lager under Azure Monitor av det andra kvartalet
2020. Den här ändringen kommer att överföra insikter om program till Log Analytics arbets ytor och göra frågor, insikter och andra förbättringar som kan uppstå medan konfigurationen av CMK på din arbets yta används även för dina Application Insights data.

> [!NOTE]
> Om du inte behöver distribuera CMK för dina program insikter innan du implementerar den, rekommenderar vi att du väntar med Application Insights CMK eftersom sådana distributioner avbryts av integreringen och du måste konfigurera CMK igen efter migreringen till Log Analytics arbets yta. Minst 1 TB per dag gäller för kluster nivån och tills konsolideringen har slutförts under andra kvartalet, Application Insights och Log Analytics kräver separata kluster.

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK-konfiguration

Konfigurationen av Application Insights CMK är identisk med processen som illustreras i den här artikeln, inklusive begränsningar och fel sökning, förutom dessa steg:

- Skapa en *kluster* resurs
- Associera en komponent till en *kluster* resurs

När du konfigurerar CMK för Application Insights använder du de här stegen i stället för de som anges ovan.

### <a name="create-a-cluster-resource"></a>Skapa en *kluster* resurs

Den här resursen används som mellanliggande identitets anslutning mellan din Key Vault och dina komponenter. När du har fått en bekräftelse på att dina prenumerationer var vit listas skapar du en Log Analytics *kluster* resurs i den region där dina komponenter finns. *Kluster* resursens typ definieras när du skapar den genom att ange egenskapen *ClusterType* till antingen *LogAnalytics*eller *ApplicationInsights*. Den bör vara *ApplicationInsights* för Application Insights CMK. *ClusterType* -inställningen kan inte ändras efter konfigurationen.

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

200 OK och rubrik.
Under den tidigaste åtkomst perioden för funktionen, allokeras ADX-klustret manuellt. När den tar etablering av det ADX klustret a När ska slutföras, kan du kontrol lera etablerings statusen på två sätt:
1. Kopiera URL-värdet för Azure-AsyncOperation från svaret och följ [status kontrollen asynkrona åtgärder](#asynchronous-operations-and-status-check).
2. Skicka en GET-begäran på *kluster* resursen och titta på *provisioningState* -värdet. Den är *ProvisioningAccount* medan *etableringen har slutförts och* slutförts.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Associera en komponent till en *kluster* resurs med hjälp av [komponenter – skapa eller uppdatera](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

Du måste ha behörigheten "Skriv" både för din komponent och *kluster* resurs för att utföra den här åtgärden, vilket innefattar följande åtgärder:

- I komponent: Microsoft. Insights/komponent/Skriv
- I *kluster* resurs: Microsoft. OperationalInsights/kluster/Write

> [!IMPORTANT]
> Det här steget ska endast utföras efter ADX kluster etablering. Om du kopplar komponenter och matar in data före etableringen kommer inmatade data att tas bort och går inte att återskapa.
> Verifiera att ADX-klustret är etablerad genom att köra *kluster* resursen get REST API och kontrol lera att *provisioningState* -värdet har *slutförts*.

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
> Kopiera och behåll svaret eftersom du kommer att behöva det i nästa steg.

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
"Natura"-exemplet är "Web".

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
"clusterDefinitionId" är det *kluster* resurs-ID som är kopplat till den här komponenten.

Efter associationen lagras data som skickas till dina komponenter krypterade med din hanterade nyckel.
