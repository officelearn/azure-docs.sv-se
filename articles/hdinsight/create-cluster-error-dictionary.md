---
title: Azure HDInsight skapa ett kluster-fel ord lista
description: Lär dig hur du felsöker fel som uppstår när du skapar Azure HDInsight-kluster
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: b0dc974185ad616d57327e9cc3743db9ecb20e54
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302737"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: fel vid skapande av kluster

I den här artikeln beskrivs lösningar på fel som du kan stöta på när du skapar kluster.

> [!NOTE]
> De första tre fel som beskrivs i den här artikeln är verifierings fel. De kan inträffa när en Azure HDInsight-produkt använder klassen **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument ' CsmDocument_2_0 ' misslyckades med verifieringen

### <a name="error"></a>Fel

"Det går inte att få åtkomst till den angivna URI: n för skript åtgärd:\<URL för skript åtgärd\>

#### <a name="error-message"></a>Felmeddelande

"Fjärrservern returnerade ett fel: (404) hittades inte."

### <a name="cause"></a>Orsak

HDInsight-tjänsten har inte åtkomst till den skript åtgärds webb adress som du angav som en del av förfrågan om att skapa kluster. Tjänsten tar emot föregående fel meddelande när det försöker få åtkomst till skript åtgärden.

### <a name="resolution"></a>Lösning

- För en HTTP-eller HTTPS-URL, verifiera URL: en genom att försöka gå till den från ett Incognito webbläsarfönster.
- För en WASB-URL, se till att skriptet finns i det lagrings konto som du har angett i begäran. Kontrol lera också att lagrings nyckeln för det här lagrings kontot är korrekt.
- Se till att skriptet finns i lagrings kontot för en ADLS-URL.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument ' CsmDocument_2_0 ' misslyckades med verifieringen

### <a name="error"></a>Fel

"URL för skript åtgärd kan inte nås URI: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Felmeddelande

"Den aktuella skript-URI-\<SCRIPT_URI\> i ADLS, men det här klustret har inget data Lake Storage-huvudobjekt"

### <a name="cause"></a>Orsak

HDInsight-tjänsten har inte åtkomst till den skript åtgärds webb adress som du angav som en del av förfrågan om att skapa kluster. Tjänsten tar emot föregående fel meddelande när det försöker få åtkomst till skript åtgärden.

### <a name="resolution"></a>Lösning

Lägg till motsvarande Azure Data Lake Storage gen 1-konto i klustret. Lägg också till tjänstens huvud namn som ansluter till Data Lake Storage gen 1-konto till klustret.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Felkod: DeploymentDocument ' CsmDocument_2_0 ' misslyckades med verifieringen

### <a name="error"></a>Fel

Den virtuella dator storleken\<CUSTOMER_SPECIFIED_VM_SIZE\>som tillhandahölls i begäran är ogiltig eller stöds inte för rollen\<roll\>. Giltiga värden är: \<VALID_VM_SIZE_FOR_ROLE\>. "

### <a name="cause"></a>Orsak

Den angivna storleken för den virtuella datorn är inte tillåten för rollen. Det här felet kan inträffa eftersom värdet för VM-storlek inte fungerar som förväntat eller inte är lämpligt för dator rollen.

### <a name="resolution"></a>Lösning

Fel meddelandet visar en lista över giltiga värden för VM-storleken. Välj ett av dessa värden och försök att skapa en kluster förfrågan igen.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Felkod: InvalidVirtualNetworkId  

### <a name="error"></a>Fel

"VirtualNetworkId är ogiltig. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>' * "

### <a name="cause"></a>Orsak

Det **VirtualNetworkId** -värde som du angav när klustret skapades har inte rätt format.

### <a name="resolution"></a>Lösning

Kontrol lera att värdena för **VirtualNetworkId** och under nätet är i rätt format. Så här hämtar du **VirtualNetworkId** -värdet:

1. Gå till Azure Portal.
1. Välj ditt virtuella nätverk.
1. Välj meny alternativet **Egenskaper** . Värdet för egenskapen **ResourceID** är **VirtualNetworkId** -värdet.

Här är ett exempel på ett virtuellt nätverks-ID:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Felkod: CustomizationFailedErrorCode

### <a name="error"></a>Fel

"Kluster distributionen misslyckades på grund av ett fel i den anpassade skript åtgärden. Misslyckade åtgärder: \<SCRIPT_NAME\>går du till Ambari UI för att ytterligare Felsöka fel sökningen. "

### <a name="cause"></a>Orsak

Det anpassade skript som du angav under Create Cluster-begäran körs när klustret har distribuerats. Den här felkoden anger att ett fel uppstod under körningen av det anpassade skriptet med namnet \<SCRIPT_NAME\>.

### <a name="resolution"></a>Lösning

Eftersom skriptet är ditt anpassade skript rekommenderar vi att du felsöker problemet och kör skriptet igen om det behövs. Om du vill felsöka skript fel granskar du loggarna i mappen/var/lib/Ambari-agent/*. Eller öppna sidan **åtgärder** i AMBARI-användargränssnittet och välj sedan åtgärden **run_customscriptaction** för att visa fel information.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Felkod: InvalidDocumentErrorCode

### <a name="error"></a>Fel

"\<META_STORE_TYPE\> Metaarkiv schema version \<METASTORE_MAJOR_VERSION\> i databasen \<DATABASE_NAME\> är inkompatibel med kluster versionen \<CLUSTER_VERSION\>"

### <a name="cause"></a>Orsak

Den anpassade metaarkiv är inte kompatibel med den valda HDInsight-klustrets version. HDInsight 4,0-kluster stöder för närvarande endast Metaarkiv version 3,0 och senare, medan HDInsight 3,6-kluster inte stöder Metaarkiv-version 3,0 och senare.

### <a name="resolution"></a>Lösning

Använd endast Metaarkiv-versioner som stöds av din HDInsight-kluster version. Om du inte anger en anpassad metaarkiv skapar HDInsight en metaarkiv internt och tar sedan bort den när klustret tas bort.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Felkod: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Fel

"Det gick inte att ansluta till kluster hanterings slut punkten för att utföra skalnings åtgärden. Kontrol lera att nätverks säkerhets reglerna inte blockerar extern åtkomst till klustret och att Ambari-gränssnittet (Cluster Manager) kan nås. "

### <a name="cause"></a>Orsak

En brand Väggs regel i nätverks säkerhets gruppen (NSG) blockerar kluster kommunikation med kritiska Azure-tjänster för hälso tillstånd och hantering.

### <a name="resolution"></a>Lösning

Om du planerar att använda nätverks säkerhets grupper för att kontrol lera nätverks trafiken vidtar du följande åtgärder innan du installerar HDInsight:

- Identifiera den Azure-region som du planerar att använda för HDInsight.
- Identifiera de IP-adresser som krävs av HDInsight. Mer information finns i avsnittet om [IP-adresser för HDInsight-hantering](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Skapa eller ändra nätverks säkerhets grupper för det undernät som du planerar att installera HDInsight i.
  - För nätverks säkerhets grupper tillåter du inkommande trafik på port 443 från IP-adresserna. Den här konfigurationen säkerställer att hanterings tjänster för HDInsight kan komma åt klustret utanför det virtuella nätverket.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Felkod: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Fel

"Den hanterade identiteten har inte behörighet för lagrings kontot. Verifiera att rollen Storage BLOB data Owner har tilldelats till den hanterade identiteten för lagrings kontot. Lagring:/Subscriptions/\<prenumerations-ID\>/resourceGroups/\< resurs grupp namn\>/providers/Microsoft.Storage/storageAccounts/\<lagrings kontots namn\>, hanterad identitet:/Subscriptions/\<prenumerations-ID\>/resourceGroups//\< resurs grupp namn\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<användarens hanterade identitets namn\>"

### <a name="cause"></a>Orsak

Du har inte angett de behörigheter som krävs för att hantera identiteten. Den användardefinierade hanterade identiteten har inte rollen Blob Storage Contributor på Azure Data Lake Storage Gen2 lagrings konto.

### <a name="resolution"></a>Lösning

1. Öppna Azure Portal.
1. Gå till ditt lagringskonto.
1. Titta under **Access Control (IAM)** .
1. Se till att användaren har rollen Storage BLOB data Contributor eller rollen som ägare till lagrings-BLOB-data.

Mer information finns i [Konfigurera behörigheter för den hanterade identiteten på data Lake Storage Gen2-kontot](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Felkod: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Fel

"Säkerhets reglerna i nätverks säkerhets gruppen/Subscriptions/\<SubscriptionID\>/resourceGroups/< resurs gruppens namn\> default/providers/Microsoft. Network/networkSecurityGroups/\<nätverks säkerhets gruppens namn\> konfigurerat med undernät/Subscriptions/\<SubscriptionID\>/resourceGroups/\<resurs grupp namn\> RG-westeurope-vnet-tomtom-default/providers/Microsoft. Network/virtualNetworks/\<Virtual Nätverks namn\>/subnets/\<-undernätets namn\> tillåter inte obligatorisk inkommande och/eller utgående anslutning. Om du vill ha mer information går du [till planera ett virtuellt nätverk för Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)eller kontaktar supporten. "

### <a name="cause"></a>Orsak

Om nätverks säkerhets grupper eller användardefinierade vägar (UDR) kontrollerar inkommande trafik till ditt HDInsight-kluster måste du se till att klustret kan kommunicera med kritiska Azure-tjänster för hälso tillstånd och hantering.

### <a name="resolution"></a>Lösning

Om du planerar att använda nätverks säkerhets grupper för att kontrol lera nätverks trafiken vidtar du följande åtgärder innan du installerar HDInsight:

- Identifiera den Azure-region som du planerar att använda för HDInsight och skapa en säker lista över IP-adresserna för din region. Mer information finns i [hälso-och hanterings tjänster: vissa regioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Identifiera de IP-adresser som HDInsight kräver. Mer information finns i avsnittet om [hantering av HDInsight-IP-adresser](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Skapa eller ändra nätverks säkerhets grupper för det undernät som du planerar att installera HDInsight i. För nätverks säkerhets grupper tillåter du inkommande trafik på port 443 från IP-adresserna. Den här konfigurationen säkerställer att hanterings tjänster för HDInsight kan komma åt klustret utanför det virtuella nätverket.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Felkod: kluster installationen kunde inte installera komponenter på en eller flera värdar

###  <a name="error"></a>Fel

"Det gick inte att installera komponenter på en eller flera värdar under kluster installationen. Gör om begäran. "

### <a name="cause"></a>Orsak 

Detta fel uppstår vanligt vis när det uppstår ett tillfälligt problem eller ett Azure-avbrott.

### <a name="resolution"></a>Lösning

På sidan [Azure-status](https://status.azure.com) finns några Azure-avbrott som kan påverka kluster distributionen. Försök att distribuera kluster igen om det inte finns några avbrott.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du felsöker fel i skapa kluster finns i [Felsöka kluster skapande fel med Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
