---
title: Azure Monitor kundhanterad nyckel konfiguration
description: Information och steg för att konfigurera kundhanterad nyckel (CMK) för att kryptera data i dina Log Analytics arbets ytor med hjälp av en Azure Key Vault nyckel.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/11/2020
ms.openlocfilehash: 0354abf6a5450a1116423e3a35c3a7e2ae7b9057
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75971096"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor kundhanterad nyckel konfiguration 

Den här artikeln innehåller bakgrunds information och steg för att konfigurera Kundhanterade nycklar (CMK) för dina Log Analytics-arbetsytor och Application Insights-komponenter. När de har kon figurer ATS krypteras alla data som skickas till dina arbets ytor eller komponenter med din Azure Key Vault nyckel.

Vi rekommenderar att du granskar [begränsningar och](#Limitations and constraints) begränsningar nedan före konfigurationen.

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- Azure Monitor CMK är en funktion för tidig åtkomst som är aktive rad för registrerade prenumerationer.

- CMK-distributionen som beskrivs i den här artikeln levereras i produktions kvalitet och stöds, även om det är en tidig åtkomst funktion.

- CMK-funktionen levereras på ett dedikerat data lager-kluster, som är ett Azure Datautforskaren-kluster (ADX) och är lämpligt för kunder som skickar 1 TB per dag eller mer. 

- Pris sättnings modellen för CMK är inte tillgänglig för närvarande och omfattas inte av den här artikeln. En pris modell för dedikerat ADX-kluster förväntas under det andra kvartalet av kalender året (CY) 2020 och gäller för alla befintliga CMK-distributioner.

- I den här artikeln beskrivs CMK-konfigurationen för Log Analytics-arbetsytor. CMK för Application Insights-komponenter stöds också med hjälp av den här artikeln och skillnaderna visas i bilagan.

> [!NOTE]
> Log Analytics och Application Insights använder samma plattform för data lagring och frågemotor.
> Vi samlar dessa två butiker genom att integrera Application Insights i Log Analytics för att skapa ett enda enhetligt logg lager under Azure Monitor. Den här ändringen planeras för det andra kvartalet för kalender året 2020. Om du inte behöver distribuera CMK för dina Application Insights data, rekommenderar vi att du väntar på att konsolideringen ska slutföras eftersom distributionerna avbryts av konsolideringen och du måste konfigurera om CMK efter migreringen till loggen Analytics-arbetsyta. Minst 1 TB per dag gäller för kluster nivån och tills konsolideringen har slutförts under andra kvartalet, Application Insights och Log Analytics kräver separata kluster.

## <a name="customer-managed-key-cmk-overview"></a>Översikt över kundhanterad nyckel (CMK)

[Kryptering i vila](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) är ett gemensamt sekretess-och säkerhets krav i organisationer. Du kan låta Azure helt hantera kryptering i vila, medan du har olika alternativ för att hantera krypterings-eller krypterings nycklar.

Azure Monitor data lagringen säkerställer att alla data som krypteras i vila med hjälp av Azure-hanterade nycklar när de lagras i Azure Storage. Azure Monitor innehåller också ett alternativ för data kryptering med hjälp av en egen nyckel som lagras i [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), som nås med hjälp av systemtilldelad autentisering med [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) . Den här nyckeln kan vara antingen [program vara eller maskin vara-HSM skyddad](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor användningen av kryptering är identisk med hur [Azure Storage kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) fungerar.

Den frekvens som Azure Monitor lagrings åtkomst Key Vault för omslutning och unwrap-åtgärder är mellan 6 och 60 sekunder. Azure Monitor Storage respekterar alltid ändringar i nyckel behörigheter inom en timme.

Inmatade data under de senaste 14 dagarna behålls också i frekvent cache (SSD-backad) för effektiv Operations Engine-åtgärd. Dessa data förblir krypterade med Microsoft-nycklar oavsett CMK-konfiguration, men vi arbetar med att ha SSD krypterat med CMK tidigt 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Så här fungerar CMK i Azure Monitor

Azure Monitor utnyttjar systemtilldelad hanterad identitet för att ge åtkomst till din Azure Key Vault. Systemtilldelad hanterad identitet kan bara kopplas till en enda Azure-resurs. Identiteten för Azure Monitor data lager (ADX-kluster) stöds på kluster nivå och detta anger att CMK-funktionen levereras i ett dedikerat ADX-kluster. För att stödja CMK på flera arbets ytor fungerar en ny Log Analytics resurs (*kluster*) som en mellanliggande identitets anslutning mellan din Key Vault och dina Log Analytics arbets ytor. Det här konceptet följer den systemtilldelade identitets begränsningen och identiteten upprätthålls mellan ADX-klustret och Log Analytics *kluster* resurs, medan data för alla associerade arbets ytor skyddas med din Key Vault-nyckel. Kluster lagringen Underlay ADX använder den hanterade identiteten som\'s som är associerad med *kluster* resursen för att autentisera och komma åt din Azure Key Vault via Azure Active Directory.

![Översikt över CMK](media/customer-managed-keys/cmk-overview.png)
1.  Kundens Key Vault.
2.  Kundens Log Analytics kluster resurs har hanterad identitet med behörighet att Key Vault – identiteten stöds på nivån data lager (ADX Cluster).
3.  Azure Monitor dedikerat ADX-kluster.
4.  Kundens arbets ytor som är kopplade till kluster resursen för CMK-kryptering.

## <a name="encryption-keys-management"></a>Hantering av krypterings nycklar

Det finns tre typer av nycklar som ingår i kryptering av lagrings data:

- Nyckel krypterings nyckel för **KEK** i Key Vault (CMK)
- Krypterings nyckel för **AEK** -konto
- **DEK** – data krypterings nyckel

Följande regler gäller:

- Lagrings kontot ADX genererar en unik krypterings nyckel för varje lagrings konto, vilket kallas för AEK.

- AEK används för att härleda DEKs, vilket är de nycklar som används för att kryptera varje data block som skrivs till disk.

- När du konfigurerar nyckeln i Key Vault och refererar till den i *kluster* resursen, omsluter Azure Storage AEK med KEK i Azure Key Vault.

- Din KEK lämnar aldrig Key Vault och om det finns en HSM-nyckel lämnar den aldrig maskin varan.

- Azure Storage använder den hanterade identitet som är kopplad till *kluster* resursen för att autentisera och komma åt Azure Key Vault via Azure Active Directory.

- Vid Läs-och skriv åtgärder skickar Azure Storage begär anden till Azure Key Vault för att omsluta och packa upp AEK för att utföra kryptering och dekryptering.

## <a name="cmk-provisioning-procedure"></a>Etablerings procedur för CMK

För Application Insights CMK-konfiguration följer du tillägget för innehållet i steg 3 och 6.

1. Prenumerations-vit listning – detta krävs för den här funktionen för tidig åtkomst
2. Skapa Azure Key Vault och lagra nyckel
3. Skapa en *kluster* resurs
4. Bevilja behörighet till din Key Vault
5. Etablering av Azure Monitor data lager (ADX-kluster)
6. Association för Log Analytics arbets ytor

Proceduren stöds för närvarande inte i användar gränssnittet och etablerings processen utförs via REST API.

> [!IMPORTANT]
> Alla API-förfrågningar måste innehålla en token Authorization-token i begär ande huvudet.

Ett exempel:

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Där *eyJ0eXAiO..* . representerar hela autentiseringstoken. 

Du kan hämta token med någon av följande metoder:

1. Använd [Appregistreringar](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) metod.

2. I Azure-portalen
    1. Navigera till Azure Portal i "utvecklarverktyg (F12)
    1. Sök efter Authorization-sträng under "begärandehuvuden" i någon av instanserna "batch? API-version". Det ser ut så här: "auktorisering: Bearer \<token\>". 
    1. Kopiera och Lägg till det i API-anropet enligt exemplen nedan.

3. Gå till webbplatsen för Azure REST-dokumentation. Tryck på "prova" på alla API: er och kopiera Bearer-token.

### <a name="subscription-whitelisting"></a>Vit listning för prenumeration

CMK-funktionen är en funktion för tidig åtkomst. De prenumerationer där du planerar att skapa *kluster* resurser måste vit listas i förväg av Azures produkt grupp. Använd dina kontakter i Microsoft för att tillhandahålla dina prenumerations-ID: n.

> [!IMPORTANT]
> CMK-funktionen är regional. Din Azure Key Vault, lagrings konto, *kluster* resurs och associerade Log Analytics arbets ytor måste finnas i samma region, men de kan finnas i olika prenumerationer.

### <a name="storing-encryption-key-kek"></a>Lagra krypterings nyckel (KEK)

Skapa en Azure Key Vault resurs och generera eller importera sedan en nyckel som ska användas för data kryptering.

Azure Key Vault måste konfigureras som återställnings Bart för att skydda din nyckel och åtkomst till dina Azure Monitor data.

De här inställningarna är tillgängliga via CLI och PowerShell:
- [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) måste aktive ras
- [Rensnings skyddet](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) bör vara aktiverat för att skydda mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

### <a name="create-cluster-resource"></a>Skapa *kluster* resurs

Den här resursen används som mellanliggande identitets anslutning mellan din Key Vault och dina arbets ytor. När du har fått en bekräftelse på att dina prenumerationer har vit listas skapar du en Log Analytics *kluster* resurs i den region där dina arbets ytor finns. Application Insights och Log Analytics kräver separata kluster resurser. Kluster resursens typ definieras när du skapar den genom att ange egenskapen "clusterType" till antingen "LogAnalytics" eller "ApplicationInsights". Det går inte att ändra kluster resurs typen.

För Application Insights CMK-konfiguration följer du tillägget för det här steget.

**Skapa**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"   //Should be "ApplicationInsights" for Application Insights CMK
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```

**Svar**

Identiteten tilldelas till *kluster* resursen vid skapande tillfället.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"    //A GUID that Log Analytics generates for the cluster
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```
> [!IMPORTANT]
> Kopiera och behåll värdet "Cluster-ID" eftersom du kommer att behöva det i nästa steg.

Om du vill ta bort *kluster* resursen av någon anledning, till exempel skapa den med ett annat namn eller clusterType, använder du följande API-anrop:

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Bevilja Key Vault behörigheter

Uppdatera Key Vault och Lägg till åtkomst princip för kluster resursen. Behörigheter till Key Vault sprids sedan till den underplacerande Azure Monitor lagring som ska användas för data kryptering.
Öppna din Key Vault i Azure Portal och klicka på "åtkomst principer" och sedan "+ Lägg till åtkomst princip" för att skapa en ny princip med följande inställningar:

- Nyckel behörigheter: Välj get-, wrap-och unwrap Key-behörigheter.

- Välj huvud namn: Ange kluster-ID, vilket är "clusterId"-värdet i svaret på föregående steg.

![Bevilja Key Vault behörigheter](media/customer-managed-keys/grant-key-vault-permissions.png)

Get-behörighet krävs för att verifiera att Key Vault har kon figurer ATS för att kunna *återskapas* för att skydda din nyckel och till gång till dina Azure Monitor data.

Det tar några minuter innan *kluster* resursen har spridits i Azure Resource Manager. När du konfigurerar den här åtkomst principen direkt efter att *kluster* resursen har skapats kan det uppstå ett tillfälligt fel. I det här fallet, försök igen om några minuter.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Uppdatera kluster resurs med information om nyckel identifierare

Det här steget gäller följande viktiga versions uppdateringar i din Key Vault. Uppdatera *kluster* resursen med Key Vault information om *nyckel identifierare* för att tillåta Azure Monitor lagring att använda den nya nyckel versionen. Välj den aktuella versionen av nyckeln i Azure Key Vault för att hämta information om nyckel identifieraren.

![Bevilja Key Vault behörigheter](media/customer-managed-keys/key-identifier-8bit.png)

Uppdatera *kluster* resursens KeyVaultProperties med information om nyckel identifierare.

**Uppdatering**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": {     //Key Vault key identifier details taken from Key identifier URI
            KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
            KeyName: "<key-name>",
            KeyVersion: "<current-version>"
            },
   },
   "location":"<region-name>",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```

**Svar**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
       "KeyVaultProperties": {     // Key Vault key identifier
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"    //Example: Switzerland North
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Etablering av Azure Monitor data lager (ADX-kluster)

Under den tidigaste åtkomst perioden för funktionen, allokeras ADX-klustret manuellt av produkt teamet när föregående steg har slutförts. Använd den kanal du har med Microsoft för att ange följande information:

- Bekräfta att stegen ovan har slutförts.

- JSON-svaret från föregående steg. Den kan hämtas när som helst med hjälp av ett Get API-anrop:

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
   Authorization: Bearer <token>
   ```

   **Svar**
   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-Id"
     },
     "properties": {
          "KeyVaultProperties": {    // Key Vault key identifier
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

> [!NOTE]
> Det här steget ska **endast** utföras efter att du har fått bekräftelse från produkt gruppen via din Microsoft-kanal att **etablerings tjänsten för Azure Monitor data lagring (ADX-kluster)** uppfylldes. Om du associerar arbets ytor och matar in data före den här **etableringen**tas data bort och går inte att återskapa.

**Koppla en arbets yta till en *kluster* resurs med hjälp av [arbets ytor – skapa eller uppdatera](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) API**

För Application Insights CMK-konfiguration följer du tillägget för det här steget.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2015-11-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "source": "Azure",
    "customerId": "<workspace-id>",    //Available in Azure portal under Log Analytics workspace Overview section
    "features": {
      "clusterDefinitionId": "<cluster-id>"    //It's the "clusterId" value provided in the respond from the previous step 
    }
  },
  "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>",
  "name": "<workspace-name>",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "<region-name>"
}
```

**Svar**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-id",
    "retentionInDays": value,
    "features": {
      "legacy": value,
      "searchVersion": value,
      "clusterDefinitionId": "cluster-id" //The id of the Cluster resource
    },
    "workspaceCapping": {
      "dailyQuotaGb": value,
      "quotaNextResetTime": "timeStamp",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

Efter associationen lagras data som skickas till dina arbets ytor krypterade med din hanterade nyckel.

## <a name="cmk-kek-revocation"></a>CMK (KEK) åter kallelse

Azure Monitor-lagringen kommer alltid att respektera ändringar i nyckel behörigheter inom en timme, vanligt vis snart, och lagring kommer att bli otillgänglig. Data som hämtas till arbets ytor som är associerade med din *kluster* resurs släpps och frågor kommer att Miss förväntas. Tidigare inmatade data är inte tillgängliga i Azure Monitor lagring så länge din nyckel har återkallats, och dina arbets ytor tas inte bort. Otillgängliga data regleras av data bevarande principen och kommer att rensas när kvarhållning har nåtts.

Lagringen avsöker regelbundet din Key Vault för att försöka att packa upp krypterings nyckeln och en gång till, men data inmatningen och frågan återupptas inom 30 minuter.

## <a name="cmk-kek-rotation"></a>CMK (KEK) rotation

Rotationen av CMK kräver en explicit uppdatering av *kluster* resursen med den nya Azure Key Vault nyckel versionen. Om du vill uppdatera Azure Monitor med din nya nyckel version följer du anvisningarna i steget uppdatera *kluster* resurs med information om *nyckel identifierare* .

Om du uppdaterar nyckeln i Key Vault och inte uppdaterar informationen om den nya *nyckel identifieraren* i *kluster* resursen *, kommer Azure Monitor Storage att fortsätta använda din tidigare nyckel.

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

- Funktionen CMK stöds på ADX kluster nivå och kräver ett dedikerat Azure Monitor ADX-kluster

- Det högsta antalet *kluster* resurser per prenumeration är begränsat till 5

- *Kluster* resurs associationen till arbets ytan ska endast utföras efter att du har fått en bekräftelse från produkt gruppen som ADX-klustret uppfylldes. Data som skickas före denna etablering tas bort och går inte att återskapa.

- CMK-kryptering gäller nyligen inmatade data efter CMK-konfigurationen. Data som matats in före CMK-konfigurationen var krypterade med Microsoft-nyckeln. Du kan fråga efter data innan och efter konfigurationen sömlöst.

- CMK-funktionen är regional – din Azure Key Vault, *kluster* resurs och associerade arbets ytor måste finnas i samma region, men kan finnas i olika prenumerationer.

- När arbets ytan är kopplad till en *kluster* resurs kan den inte avassocieras från *kluster* resursen eftersom data krypteras med din nyckel och inte kan nås utan din KEK i Azure Key Vault.

- Azure Key Vault måste konfigureras som återställnings Bart. Dessa egenskaper är inte aktiverade som standard och ska konfigureras med CLI och PowerShell:

  - [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) måste aktive ras
  - [Rensnings skyddet](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) bör vara aktiverat för att skydda mot Force borttagning av hemligheten/valvet även efter mjuk borttagning

- Application Insights och Log Analytics kräver separata *kluster* resurser. *Kluster* resursens typ definieras när du skapar den genom att ange egenskapen "clusterType" till antingen "LogAnalytics" eller "ApplicationInsights". Det går inte att ändra *kluster* resurs typen.

- Det finns för närvarande inte stöd för att flytta *kluster* resurser till en annan resurs grupp eller prenumeration.

- Associationen för arbets ytan till *kluster* resursen kommer inte att fungera om *kluster* resursen finns i en annan klient.

-   Associationen för arbets ytan till *kluster* resursen kommer inte att fungera om den är kopplad till en annan *kluster* resurs

## <a name="troubleshooting-and-management"></a>Fel sökning och hantering

- Key Vault tillgänglighet
    - I normal drift går cachelagring av AEK under korta tids perioder tillbaka till Key Vault till Unwrap.
    
    - Tillfälliga anslutnings fel. Lagring hanterar tillfälliga fel (timeout, anslutnings fel, DNS-problem) genom att tillåta att nycklar hålls kvar i cacheminnet för kort, samtidigt som den överkommer till alla små signaler i tillgänglighet. Funktionerna för fråga och inmatning fortsätter utan avbrott.
    
    - Live site, om du inte har till gång till cirka 30 minuter kommer lagrings kontot att bli otillgängligt. Fråge funktionen är inte tillgänglig och inmatade data cachelagras i flera timmar med Microsoft-nyckeln för att undvika data förlust. När åtkomsten till Key Vault återställs blir frågan tillgänglig och temporära cachelagrade data matas in i data lagringen och krypteras med CMK.

- Om du skapar en *kluster* resurs och anger KeyVaultProperties omedelbart kan åtgärden Miss lyckas eftersom det inte går att definiera åtkomst principen förrän system identiteten har tilldelats till *kluster* resursen.

- Om du uppdaterar en befintlig *kluster* resurs med KeyVaultProperties och get nyckel åtkomst principen saknas i Key Vault, Miss kommer åtgärden.

- Om du försöker ta bort en *kluster* resurs som är kopplad till en arbets yta kommer borttagnings åtgärden att Miss förväntas.

- Använd det här API-anropet för att hämta alla *kluster* resurser för en resurs grupp:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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
           "KeyVaultProperties": {    // Key Vault key identifier
              KeyVaultUri: "https://{key-vault-name}.vault.azure.net",
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

- Använd det här API-anropet för att hämta alla *kluster* resurser för en prenumeration:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Svar**
    
  Samma svar som för "*kluster* resurser för en resurs grupp", men i prenumerations omfånget.
    
- Använd det här API-anropet för att ta bort en *kluster* resurs – du måste ta bort alla associerade arbets ytor innan du kan ta bort *kluster* resursen:

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Svar**

  200 OK


## <a name="appendix"></a>Bilaga

Application Insights kundens hanterade nyckel (CMK) stöds också, men du bör överväga följande ändring för att hjälpa dig att planera distributionen av CMK för dina program insikter-komponenter.

Log Analytics och Application Insights använder samma plattform för data lagring och frågemotor. Vi samlar dessa två butiker genom att integrera Application Insights i Log Analytics för att tillhandahålla ett enda enhetligt logg lager under Azure Monitor av det andra kvartalet
2020. Den här ändringen kommer att överföra insikter om program till Log Analytics arbets ytor och göra frågor, insikter och andra förbättringar som kan uppstå medan konfigurationen av CMK på din arbets yta används även för dina Application Insights data.

> [!NOTE]
> Om du inte behöver distribuera CMK för dina program insikter innan du implementerar den, rekommenderar vi att du väntar med Application Insights CMK eftersom sådana distributioner avbryts av integreringen och du måste konfigurera CMK igen efter migreringen till loggen Analytics-arbetsyta. Minst 1 TB per dag gäller för kluster nivån och tills konsolideringen har slutförts under andra kvartalet, Application Insights och Log Analytics kräver separata kluster.

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK-konfiguration

Konfigurationen av Application Insights CMK är identisk med processen som illustreras i den här artikeln, inklusive begränsningar och fel sökning, förutom dessa steg:

- Skapa en *kluster* resurs

- Associera en komponent till en *kluster* resurs

När du konfigurerar CMK för Application Insights använder du de här stegen i stället för de som anges ovan.

### <a name="create-a-cluster-resource"></a>Skapa en *kluster* resurs

Den här resursen används som mellanliggande identitets anslutning mellan din Key Vault och dina komponenter. När du har fått en bekräftelse på att dina prenumerationer var vit listas skapar du en Log Analytics *kluster* resurs i den region där dina komponenter finns. *Kluster* resursens typ definieras när du skapar den genom att ange egenskapen *ClusterType* till antingen *LogAnalytics*eller *ApplicationInsights*. Den bör vara *ApplicationInsights* för Application Insights CMK. *ClusterType* -inställningen kan inte ändras efter konfigurationen.

**Skapa**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Svar**

Identiteten tilldelas till *kluster* resursen vid skapande tillfället.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"    //A GUID that was generated by the managed identity service
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id"   //A GUID that Log Analytics generates for the cluster - copy it since you need it for Key Vault and components association 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-updatehttpsdocsmicrosoftcomrestapiapplication-insightscomponentscreateorupdate-api"></a>Associera en komponent till en *kluster* resurs med hjälp av [komponenter – skapa eller uppdatera](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"    //It's the "clusterId" value provided in the respond from the previous step
  },
  "location": "<region-name>",
  "kind": "<component-type>",    //Example: web
}
```

**Svar**

```json
{
  "id": "/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.insights/components/{component-name}",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"    //The Cluster resource ID that is associated to this component
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

Efter associationen lagras data som skickas till dina komponenter krypterade med din hanterade nyckel.
