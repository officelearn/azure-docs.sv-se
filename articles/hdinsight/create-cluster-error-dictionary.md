---
title: Skapa en kluster fel ord lista
description: Lär dig hur du skapar en kluster fel ord lista.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483068"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: fel vid skapande av kluster

I den här artikeln beskrivs lösningar på fel som uppstår när du skapar kluster. 

> [!NOTE]
> De första tre felen i listan nedan inträffar på grund av verifierings fel när klassen CsmDocument_2_0 används av en HDInsight-produkt.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument ' CsmDocument_2_0 ' misslyckades med verifieringen

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Fel**: skript åtgärdens plats kan inte nås URI:\<URL-åtgärdens URL\>

**Fel meddelande: "Fjärrservern returnerade ett fel: (404) hittades inte."**

### <a name="cause"></a>Orsak
Den skript åtgärds-URL som tillhandahålls som en del av Create Cluster-begäran går inte att komma åt från HDInsight-tjänsten. Vi tar emot "ErrorMessage" när vi försöker få åtkomst till skript åtgärden.

### <a name="resolution"></a>Upplösning 
  - För en HTTP/HTTPS-URL kan du verifiera genom att försöka öppna URL: en från ett Incognito webbläsarfönster. 
  - För en WASB-URL, se till att skriptet finns i det lagrings konto som anges i begäran. Kontrol lera att lagrings nyckeln för det här lagrings kontot är korrekt. 
  - Kontrol lera att skriptet finns i lagrings kontot för en ADLS-URL.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument ' CsmDocument_2_0 ' misslyckades med verifieringen

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Fel**: skript åtgärdens plats kan inte nås URI: \<URL-åtgärdens URL\>

**Fel meddelande: den aktuella skript-URI: n \<skript-URI\> är i ADLS, men det här klustret har inget data Lake Storage-huvudobjekt**

### <a name="cause"></a>Orsak
Den skript åtgärds-URL som tillhandahålls som en del av Create Cluster-begäran kan inte nås från HDInsight-tjänsten. Vi tar emot "ErrorMessage" när vi försöker få åtkomst till skript åtgärden. 

### <a name="resolution"></a>Upplösning

Se till att motsvarande Azure Data Lake Store gen 1-konto läggs till i klustret. Tjänstens huvud namn som används för att komma åt Azure Data Lake Store gen 1-kontot läggs också till i klustret. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument ' CsmDocument_2_0 ' misslyckades med verifieringen

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Fel**: VM-storlek\<kundens angivna VM-storlek\>som angavs i begäran är ogiltig eller stöds inte för rollen\<roll\>. Giltiga värden är: \<giltig storlek på virtuell dator för roll\>.

### <a name="cause"></a>Orsak
De angivna VM-storlekarna kunde inte användas för rollen. Detta kan bero på att värdet för den virtuella datorns storlek inte fungerar som förväntat eller inte är lämpligt för dator rollen. 

### <a name="resolution"></a>Upplösning
Fel meddelandet visar en lista över giltiga värden för VM-storleken. Välj ett av dessa giltiga värden och försök att skapa en kluster förfrågan igen. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Felkod: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Fel**: VirtualNetworkId är inte giltig. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' *

### <a name="cause"></a>Orsak
Det **VirtualNetworkId** -värde som angavs när klustret skapades har fel format. 

### <a name="resolution"></a>Upplösning
Kontrol lera att **VirtualNetworkId** och Subnet har rätt format. Om du vill hämta **VirtualnetworkId** -värdet går du till Azure Portal, väljer ditt virtuella nätverk och väljer sedan **Egenskaper** på menyn. Egenskapen **ResourceID** är **VirtualNetworkId** -värdet. 

Ett exempel på ett virtuellt nätverks-ID är: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Felkod: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Fel**: kluster distributionen misslyckades på grund av ett fel i den anpassade skript åtgärden. Misslyckade åtgärder: \<SCRIPT_NAME\>kan du gå till Ambari UI för att ytterligare Felsöka fel sökningen.

### <a name="cause"></a>Orsak
Användarens anpassade skript som angavs under Create Cluster-begäran körs när klustret har distribuerats. Den här felkoden anger att ett fel uppstod när det här anpassade skriptet kördes med namnet \<SCRIPT_NAME\>.   

### <a name="resolution"></a>Upplösning
Eftersom det här är användarens anpassade skript bör användarna felsöka problemet och köra skriptet igen om det behövs. Om du vill felsöka skript fel granskar du loggarna i mappen/var/lib/Ambari-agent/*. Eller öppna sidan åtgärder i Ambari-ANVÄNDARGRÄNSSNITTET och välj sedan åtgärden **run_customscriptionaction** för att visa fel informationen. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Felkod: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Fel**: \<META_STORE_TYPE\> metaarkiv schema version \<METASTORE_MAJOR_VERSION\> i databasen \<DATABASE_NAME\> är inkompatibel med kluster versionen \<CLUSTER_VERSION\>

### <a name="cause"></a>Orsak
Den anpassade metaarkiv är inte kompatibel med den valda HDInsight-klustrets version. HDInsight 4,0-kluster har för närvarande endast stöd för Metaarkiv version 3. *x*och HDInsight 3,6 stöder inte metaarkiv version 3. *x* eller senare. 

### <a name="resolution"></a>Upplösning
Se till att endast använda Metaarkiv-versioner som stöds av varje HDInsight-kluster version. Observera att om en anpassad metaarkiv inte anges skapas en metaarkiv internt i HDInsight. Den här metaarkiv tas dock bort automatiskt när klustret tas bort. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Felkod: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Fel**: det gick inte att ansluta till kluster hanterings slut punkten för att utföra skalnings åtgärden. Kontrol lera att nätverks säkerhets reglerna inte blockerar extern åtkomst till klustret och att Ambari-gränssnittet (Cluster Manager) kan nås.

### <a name="cause"></a>Orsak
Du har en brand Väggs regel i nätverks säkerhets gruppen (NSG) som blockerar kluster kommunikation med kritiska Azure-tjänster för hälso tillstånd och hantering. 

### <a name="resolution"></a>Upplösning
Om du planerar att använda **nätverks säkerhets grupper** för att kontrol lera nätverks trafiken, vidta följande åtgärder innan du installerar HDInsight: 
  - Identifiera den Azure-region som du planerar att använda för HDInsight. 
  - Identifiera de IP-adresser som krävs av HDInsight. Mer information finns i avsnittet om [IP-adresser för HDInsight-hantering](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
    - Skapa eller ändra nätverks säkerhets grupper för det undernät som du planerar att installera HDInsight i. 
    - **Nätverks säkerhets grupper:** Tillåt **inkommande** trafik på port **443** från IP-adresserna. Detta säkerställer att HDInsight Management Services kan komma åt klustret utanför det virtuella nätverket. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Felkod: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Fel**: den hanterade identiteten saknar behörighet för lagrings kontot. Verifiera att rollen Storage BLOB data Owner har tilldelats till den hanterade identiteten för lagrings kontot. Lagring:/Subscriptions/\<prenumerations-ID\>/resourceGroups/\< resurs grupp namn\>/providers/Microsoft.Storage/storageAccounts/\<lagrings kontots namn\>, hanterad identitet:/Subscriptions/\<prenumerations-ID\>/resourceGroups//\< resurs grupp namn\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<användarens hanterade identitets namn\>

### <a name="cause"></a>Orsak
De behörigheter som krävs har inte angetts för att **Hantera identiteten.** **Användarens hanterade identitet** har inte Blob Storage deltagar rollen på ADLS Gen2 lagrings konto. 

### <a name="resolution"></a>Upplösning

Öppna Azure Portal, gå till ditt lagrings konto, se **Access Control (IAM)** och se till att Storage BLOB-datadeltagaren eller rollen som lagrings-BLOB-dataägare har tilldelats åtkomst till den **användarspecifika hanterade identiteten** för prenumerationen. Mer information finns i [Konfigurera behörigheter för den hanterade identiteten på data Lake Storage Gen2-kontot](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Felkod: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Fel**: säkerhets reglerna i nätverks säkerhets gruppen/Subscriptions/\<SubscriptionID >\/resourceGroups/<Resource Group name> default/providers/Microsoft. Network/networkSecurityGroups/\<nätverks säkerhets gruppens namn\> har kon figurer ATS med undernät/Subscriptions/\<SubscriptionID >\/resourceGroups/\<resurs grupp namn > RG-westeurope-VNet-TomTom-default\/providers/Microsoft. Network/virtualNetworks/\<Virtual Nätverks namn >\/undernät/\<under nät namn\> 
tillåter inte obligatorisk inkommande och/eller utgående anslutning. Om du vill ha mer information går du [till planera ett virtuellt nätverk för Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)eller kontaktar support. * *

### <a name="cause"></a>Orsak
Om du använder nätverks säkerhets grupper (NSG: er) eller användardefinierade vägar (UDR) för att styra inkommande trafik till ditt HDInsight-kluster, måste du se till att klustret kan kommunicera med kritiska Azure-tjänster för hälso tillstånd och hantering.

### <a name="resolution"></a>Upplösning
Om du planerar att använda **nätverks säkerhets grupper** för att kontrol lera nätverks trafiken, vidta följande åtgärder innan du installerar HDInsight: 
  - Identifiera den Azure-region som du planerar att använda för HDInsight och skapa en säker lista över IP-adresser för din region: [hälso-och hanterings tjänster: vissa regioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Identifiera de IP-adresser som krävs av HDInsight. Mer information finns i avsnittet om [hantering av HDInsight-IP-adresser](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Skapa eller ändra nätverks säkerhets grupper för det undernät som du planerar att installera HDInsight i. **Nätverks säkerhets grupper**: Tillåt inkommande trafik på port **443** från IP-adresserna. Detta säkerställer att HDInsight Management Services kan komma åt klustret utanför det virtuella nätverket.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Felkod: kluster installationen kunde inte installera komponenter på en eller flera värdar

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Fel**: kluster installationen kunde inte installera komponenter på en eller flera värdar. Försök att utföra begäran igen. 

### <a name="cause"></a>Orsak 
Detta fel uppstår vanligt vis när det uppstår ett tillfälligt problem eller om det uppstår ett Azure-avbrott 

### <a name="resolution"></a>Upplösning

På sidan [Azure-status](https://status.azure.com/status) hittar du möjliga Azure-avbrott som kan påverka kluster distributionen. Försök att distribuera kluster igen om det inte uppstår några avbrott. 

## <a name="next-steps"></a>Efterföljande moment

Mer information om fel sökning av problem med att skapa kluster finns i [Felsöka kluster skapande fel med Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
