---
title: Azure HDInsight Skapa ett kluster - felordlista
description: Lär dig hur du felsöker fel som uppstår när du skapar Azure HDInsight-kluster
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: b0dc974185ad616d57327e9cc3743db9ecb20e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302737"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Fel för att skapa kluster

I den här artikeln beskrivs lösningar på fel som du kan stöta på när du skapar kluster.

> [!NOTE]
> De tre första felen som beskrivs i den här artikeln är valideringsfel. De kan uppstå när en Azure HDInsight-produkt använder **klassen CsmDocument_2_0.**

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument 'CsmDocument_2_0' misslyckades valideringen

### <a name="error"></a>Fel

"Det går inte att komma\<åt URI: SCRIPT ACTION URL"\>

#### <a name="error-message"></a>Felmeddelande

"Fjärrservern returnerade ett fel: (404) hittades inte."

### <a name="cause"></a>Orsak

HdInsight-tjänsten kan inte komma åt skriptåtgärds-URL:en som du angav som en del av begäran om skapa kluster. Tjänsten får föregående felmeddelande när den försöker komma åt skriptåtgärden.

### <a name="resolution"></a>Lösning

- För en HTTP- eller HTTPS-URL kontrollerar du URL:en genom att försöka gå till den från ett inkognitowebbläsare.
- För en WASB-URL bör du se till att skriptet finns i lagringskontot som du anger i begäran. Kontrollera också att lagringsnyckeln för det här lagringskontot är korrekt.
- För en ADLS-URL kontrollerar du att skriptet finns i lagringskontot.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument 'CsmDocument_2_0' misslyckades valideringen

### <a name="error"></a>Fel

"Det går inte att komma \<åt URI:en\>skriptåtgärd: SCRIPT_ACTION_URL "

#### <a name="error-message"></a>Felmeddelande

"Det angivna skriptet \<URI\> SCRIPT_URI finns i ADLS, men det här klustret har inget huvudnamn för lagring av datasjölagring"

### <a name="cause"></a>Orsak

HdInsight-tjänsten kan inte komma åt skriptåtgärds-URL:en som du angav som en del av begäran om skapa kluster. Tjänsten får föregående felmeddelande när den försöker komma åt skriptåtgärden.

### <a name="resolution"></a>Lösning

Lägg till motsvarande Azure Data Lake Storage Gen 1-konto i klustret. Lägg också till tjänstens huvudnamn som kommer åt datasjölagringsgenm 1-kontot i klustret.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument 'CsmDocument_2_0' misslyckades valideringen

### <a name="error"></a>Fel

"VM-storlek\<'\>CUSTOMER_SPECIFIED_VM_SIZE' som anges i begäran är ogiltig\<\>eller stöds inte för rollen ' ROLL '. Giltiga värden \<är: VALID_VM_SIZE_FOR_ROLE\>."

### <a name="cause"></a>Orsak

Den virtuella datorns storlek som du angav är inte tillåten för rollen. Det här felet kan uppstå eftersom storleksvärdet för den virtuella datorn inte fungerar som förväntat eller inte är lämpligt för datorrollen.

### <a name="resolution"></a>Lösning

Felmeddelandet visar giltiga värden för vm-storleken. Välj ett av dessa värden och försök återskapa begäran om skapa kluster.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Felkod: OgiltigtVirtualNetworkId  

### <a name="error"></a>Fel

"VirtualNetworkId är ogiltigt. VirtualNetworkId\<" USER_VIRTUALNETWORKID\>'*"

### <a name="cause"></a>Orsak

**VirtualNetworkId-värdet** som du angav när klustret skapades är inte i rätt format.

### <a name="resolution"></a>Lösning

Kontrollera att **VirtualNetworkId-** och undernätsvärdena är i rätt format. Så här hämtar du **VirtualNetworkId-värdet:**

1. Gå till Azure Portal.
1. Välj ditt virtuella nätverk.
1. Välj menyalternativet **Egenskaper.** Egenskapsvärdet **för ResourceID** är **VirtualNetworkId-värdet.**

Här är ett exempel på ett virtuellt nätverks-ID:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Felkod: CustomizationFailedErrorCode

### <a name="error"></a>Fel

"Klusterdistributionen misslyckades på grund av ett fel i den anpassade skriptåtgärden. Misslyckade åtgärder: \<SCRIPT_NAME\>, Gå till Ambari UI för att ytterligare felsöka felet."

### <a name="cause"></a>Orsak

Det anpassade skriptet som du angav under begäran om skapa kluster körs när klustret har distribuerats. Den här felkoden anger att ett fel uppstod \<under\>körningen av det anpassade skriptet med namnet SCRIPT_NAME .

### <a name="resolution"></a>Lösning

Eftersom skriptet är ditt anpassade skript rekommenderar vi att du felsöker problemet och kör skriptet igen om det behövs. Om du vill felsöka skriptfelet undersöker du loggarna i mappen /var/lib/ambari-agent/*. Eller öppna sidan **Operationer** i Ambari-användargränssnittet och välj sedan **den run_customscriptaction** åtgärden för att visa felinformationen.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Felkod: InvalidDocumentErrorCode

### <a name="error"></a>Fel

"Den \<META_STORE_TYPE\> metastore \<schemaversionen METASTORE_MAJOR_VERSION\> i \<databasen DATABASE_NAME\> är inte kompatibel \<med klusterversion\>CLUSTER_VERSION "

### <a name="cause"></a>Orsak

Den anpassade metabutiken är inkompatibel med den valda HDInsight-klusterversionen. För närvarande stöder HDInsight 4.0-kluster endast Metastore version 3.0 och senare, medan HDInsight 3.6-kluster inte stöder Metastore version 3.0 och senare.

### <a name="resolution"></a>Lösning

Använd bara Metastore-versioner som din HDInsight-klusterversion stöder. Om du inte anger en anpassad metabutik skapar HDInsight internt en metabutik och tar sedan bort den vid klusterborttagning.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Felkod: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Fel

"Det gick inte att ansluta till slutpunkten för klusterhantering för att utföra skalningsåtgärden. Kontrollera att nätverkssäkerhetsregler inte blockerar extern åtkomst till klustret och att klusterhanteraren (Ambari) användargränssnittet kan nås."

### <a name="cause"></a>Orsak

En brandväggsregel för nätverkssäkerhetsgruppen (NSG) blockerar klusterkommunikation med kritiska Azure-hälso- och hanteringstjänster.

### <a name="resolution"></a>Lösning

Om du planerar att använda nätverkssäkerhetsgrupper för att styra nätverkstrafiken vidtar du följande åtgärder innan du installerar HDInsight:

- Identifiera den Azure-region som du planerar att använda för HDInsight.
- Identifiera de IP-adresser som krävs av HDInsight. Mer information finns i avsnittet om [IP-adresser för HDInsight-hantering](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Skapa eller ändra nätverkssäkerhetsgrupperna för undernätet som du planerar att installera HDInsight i.
  - För nätverksskyddsgrupper tillåter du inkommande trafik på port 443 från IP-adresserna. Den här konfigurationen säkerställer att HDInsight-hanteringstjänster kan nå klustret utanför det virtuella nätverket.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Felkod: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Fel

"Den hanterade identiteten har inte behörighet för lagringskontot. Kontrollera att rollen Storage Blob Data Owner har tilldelats den hanterade identiteten för lagringskontot. Lagring: \</subscriptions/\> Prenumerations-ID\< /resourceGroups/ Resursgruppnamn\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Hanterad identitet: /prenumerationer/ \<Prenumerations-ID\> /resourceGroups/ / \<\>\< Resursgruppnamn\> /providers/Microsoft.ManagedIdentity/userAssignedId-enheter/ Användarhanterat identitetsnamn "

### <a name="cause"></a>Orsak

Du angav inte de behörigheter som krävs för att hantera identiteten. Den användartilldelade hanterade identiteten har inte rollen Blob Storage Contributor på Azure Data Lake Storage Gen2-lagringskontot.

### <a name="resolution"></a>Lösning

1. Öppna Azure Portal.
1. Gå till ditt lagringskonto.
1. Titta under **Åtkomstkontroll (IAM)**.
1. Kontrollera att användaren har rollen Storage Blob Data Contributor eller rollen Storage Blob Data Owner tilldelat dem.

Mer information finns i [Konfigurera behörigheter för den hanterade identiteten på datasjölagringsgenm2-kontot](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Felkod: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Fel

"Säkerhetsreglerna\<i nätverkssäkerhetsgruppen /subscriptions/\>SubscriptionID /resourceGroups/<Resource\> Group name\<default/providers/Microsoft.Network/networkSecurityGroups/ Network Security Group Name\<\>\<\> \<\> konfigurerat med undernät /subscriptionID/ SubscriptionID /resourceGroups/ Resource Group name RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ VirtualNetworks/ Nätverksnamn\>/undernät/\<undernätnamn\> tillåter inte obligatorisk inkommande och/eller utgående anslutning. Mer information finns i [Planera ett virtuellt nätverk för Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)eller kontakta supporten."

### <a name="cause"></a>Orsak

Om nätverkssäkerhetsgrupper eller användardefinierade vägar (UDRs) styr inkommande trafik till ditt HDInsight-kluster, se till att klustret kan kommunicera med kritiska Azure-hälso- och hanteringstjänster.

### <a name="resolution"></a>Lösning

Om du planerar att använda nätverkssäkerhetsgrupper för att styra nätverkstrafiken vidtar du följande åtgärder innan du installerar HDInsight:

- Identifiera den Azure-region som du planerar att använda för HDInsight och skapa en säker lista över IP-adresser för din region. Mer information finns i [Hälso- och förvaltningstjänster: Specifika regioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identifiera de IP-adresser som HDInsight kräver. Mer information finns i [IP-adresser för HDInsight-hantering](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Skapa eller ändra nätverkssäkerhetsgrupperna för undernätet som du planerar att installera HDInsight i. För nätverksskyddsgrupper tillåter du inkommande trafik på port 443 från IP-adresserna. Den här konfigurationen säkerställer att HDInsight-hanteringstjänster kan nå klustret utanför det virtuella nätverket.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Felkod: Klusterinstallationen kunde inte installera komponenter på en eller flera värdar

###  <a name="error"></a>Fel

"Klusterinstallationen kunde inte installera komponenter på en eller flera värdar. Försök igen din begäran."

### <a name="cause"></a>Orsak 

Vanligtvis genereras det här felet när det finns ett tillfälligt problem eller ett Azure-avbrott.

### <a name="resolution"></a>Lösning

Kontrollera [azure-statussidan](https://status.azure.com) för alla Azure-avbrott som kan påverka klusterdistributionen. Om det inte finns några avbrott försöker du om klusterdistributionen.

## <a name="next-steps"></a>Nästa steg

Mer information om felsökning av fel i klustergenerering finns i [Felsöka klusterskapande fel med Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
