---
title: Anpassa Azure HDInsight-kluster med hjälp av skript åtgärder
description: Lägg till anpassade komponenter i Linux-baserade HDInsight-kluster med hjälp av skript åtgärder. Skript åtgärder är Bash-skript som kan användas för att anpassa kluster konfigurationen eller lägga till ytterligare tjänster och verktyg som nyans, solr eller R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: df9e6e3a9116b9a4490d8847e9a9d3e9e112f4f7
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098798"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Anpassa Azure HDInsight-kluster med hjälp av skript åtgärder

Azure HDInsight tillhandahåller en konfigurations metod som kallas **skript åtgärder** som anropar anpassade skript för att anpassa klustret. Dessa skript används för att installera ytterligare komponenter och ändra konfigurations inställningar. Skript åtgärder kan användas under eller efter att klustret har skapats.

Skript åtgärder kan också publiceras på Azure Marketplace som ett HDInsight-program. Mer information om HDInsight-program finns i [publicera ett HDInsight-program på Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Behörigheter

För ett domänanslutet HDInsight-kluster finns det två Apache Ambari-behörigheter som krävs när du använder skript åtgärder med klustret:

* **AMBARI. KÖR\_ANPASSAT\_KOMMANDO**. Administratörs rollen Ambari har den här behörigheten som standard.
* **KLUSTER. KÖR\_ANPASSAT\_KOMMANDO**. Både HDInsight-kluster administratören och Ambari-administratören har den här behörigheten som standard.

Mer information om hur du arbetar med behörigheter med domänanslutna HDInsight finns i [Hantera HDInsight-kluster med Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Åtkomstkontroll

Om du inte är administratör eller ägare av din Azure-prenumeration måste ditt konto ha minst deltagar åtkomst till resurs gruppen som innehåller HDInsight-klustret.

Om du skapar ett HDInsight-kluster måste någon med minst deltagar åtkomst till Azure-prenumerationen tidigare ha registrerat providern för HDInsight. Leverantörregistrering sker när en användare med deltagaråtkomst skapar en resurs för första gången på en prenumeration. Du kan också göra det utan att skapa en resurs om du [registrerar en Provider med hjälp av rest](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Få mer information om hur du arbetar med åtkomst hantering:

* [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
* [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Förstå skript åtgärder

En skript åtgärd är Bash-skript som körs på noderna i ett HDInsight-kluster. Egenskaper och funktioner för skript åtgärder är följande:

* Måste lagras på en URI som är tillgänglig från HDInsight-klustret. Följande är möjliga lagrings platser:

    * För vanliga kluster:

      * ADLS Gen1: Det tjänst huvud namn som används för att komma åt Data Lake Storage måste ha Läs behörighet till skriptet. URI-formatet för skript som lagras i Data Lake Storage Gen1 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`är.

      * En BLOB i ett Azure Storage konto som antingen är det primära eller ytterligare lagrings kontot för HDInsight-klustret. HDInsight beviljas åtkomst till båda typerna av lagrings konton när klustret skapas.

        > [!IMPORTANT]  
        > Rotera inte lagrings nyckeln på det här Azure Storage kontot eftersom det leder till efterföljande skript åtgärder med skript som lagras där.

      * En offentlig fildelnings tjänst som kan nås via http://sökvägar. Exempel är Azure-Blob, GitHub, OneDrive.

        Till exempel URI: er, se skript för [skript åtgärder](#example-script-action-scripts).

     * För kluster med ESP:

         * Wasb://eller wasbs://eller http [s]://URI: er stöds.

* Kan begränsas till att endast köras på vissa nodtyper. Exempel är Head-noder eller arbetsnoder.

* Kan vara bestående eller ad hoc.

    Bestående skript används för att anpassa nya arbetsnoder som läggs till i klustret genom skalnings åtgärder. Ett bestående skript kan också tillämpa ändringar av en annan nodtyp när skalnings åtgärder sker. Ett exempel är en head-nod.

  > [!IMPORTANT]  
  > Beständiga skript åtgärder måste ha ett unikt namn.

    Ad hoc-skript är inte beständiga. De används inte för arbetsnoder som läggs till i klustret när skriptet har körts. Sedan kan du befordra ett ad hoc-skript till ett beständigt skript eller nedgradera ett beständigt skript till ett ad hoc-skript.

  > [!IMPORTANT]  
  > Skript åtgärder som används när klustret skapas sparas automatiskt.
  >
  > Skript som inte är beständiga, även om du specifikt anger att de ska vara.

* Kan acceptera parametrar som används av skriptet under körningen.

* Kör med behörigheter på rot nivå på klusternoderna.

* Kan användas via Azure Portal, Azure PowerShell, den klassiska Azure-CLI: n eller HDInsight .NET SDK.

Klustret behåller en historik över alla skript som har körts. Historiken hjälper dig när du behöver hitta ID: t för ett skript för befordran eller degradering av åtgärder.

> [!IMPORTANT]  
> Det finns inget automatiskt sätt att ångra de ändringar som gjorts av en skript åtgärd. Ändra antingen manuellt eller ange ett skript som kastar om dem.

### <a name="script-action-in-the-cluster-creation-process"></a>Skript åtgärd i processen för att skapa kluster

Skript åtgärder som används när klustret skapas skiljer sig något från skript åtgärder som körs på ett befintligt kluster:

* Skriptet sparas automatiskt.

* Ett fel i skriptet kan orsaka att klustrets skapande process Miss lyckas.

Följande diagram illustrerar när skript åtgärder körs under skapande processen:

![Anpassning och faser för HDInsight-kluster när klustret skapas][img-hdi-cluster-states]

Skriptet körs medan HDInsight konfigureras. Skriptet körs parallellt på alla angivna noder i klustret. Den körs med rot behörigheter på noderna.

> [!NOTE]  
> Du kan utföra åtgärder som att stoppa och starta tjänster, inklusive Apache Hadoop-relaterade tjänster. Om du stoppar tjänster måste du kontrol lera att Ambari-tjänsten och andra Hadoop-relaterade tjänster körs innan skriptet har slutförts. Dessa tjänster krävs för att kunna fastställa hälso tillståndet och tillståndet för klustret medan det skapas.

När klustret skapas kan du använda många skript åtgärder samtidigt. Dessa skript anropas i den ordning som de har angetts.

> [!IMPORTANT]  
> Skript åtgärder måste avslutas inom 60 minuter eller tids gränsen uppnåddes. Vid kluster etablering körs skriptet samtidigt med andra installations-och konfigurations processer. Konkurrens för resurser som CPU-tid eller nätverks bandbredd kan orsaka att skriptet tar längre tid att slutföra än i utvecklings miljön.
>
> Undvik hur lång tid det tar att köra skriptet genom att undvika åtgärder som att ladda ned och kompilera program från källan. Förkompilera program och lagra binärfilen i Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Skript åtgärd i ett kluster som körs

Ett fel i ett skript som körs på ett kluster som redan körs medför inte automatiskt att klustret ändrar till ett felaktigt tillstånd. När ett skript har slutförts bör klustret återgå till ett kör tillstånd.

> [!IMPORTANT]  
> Även om klustret har ett kör tillstånd kan det felaktiga skriptet ha brutna saker. Ett skript kan till exempel ta bort filer som krävs av klustret.
>
> Skript åtgärder körs med rot privilegier. Se till att du förstår vad ett skript gör innan du tillämpar det på klustret.

När du tillämpar ett skript på ett kluster ändras kluster statusen från att **köras** till **godkänd**. Sedan ändras den till **HDInsight-konfigurationen** och slutligen tillbaka till att **köras** för lyckade skript. Skript status loggas i historiken för skript åtgärden. Den här informationen visar om skriptet lyckades eller misslyckades. `Get-AzHDInsightScriptActionHistory` PowerShell-cmdleten visar till exempel status för ett skript. Den returnerar information som liknar följande text:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Om du ändrar kluster användaren, administratören, lösen ordet efter att klustret har skapats kan skript åtgärder som körs mot det här klustret Miss förväntas. Om du har bestående skript åtgärder som riktar sig mot arbetsnoder kan dessa skript Miss Miss förväntat när du skalar klustret.

## <a name="example-script-action-scripts"></a>Exempel skript åtgärds skript

Skript åtgärds skript kan användas via följande verktyg:

* Azure Portal
* Azure PowerShell
* Den klassiska Azure CLI
* An-HDInsight .NET SDK

HDInsight innehåller skript för att installera följande komponenter i HDInsight-kluster:

| Name | Skript |
| --- | --- |
| Lägg till ett Azure Storage-konto |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Se [lägga till ytterligare lagrings konton i HDInsight](hdinsight-hadoop-add-storage.md). |
| Installera Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Se [Installera och använda nyans på HDInsight Hadoop-kluster](hdinsight-hadoop-hue-linux.md). |
| Installera Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Se [installera Apache Giraph på HDInsight Hadoop-kluster](hdinsight-hadoop-giraph-install-linux.md). |
| Förhandsladda Hive-bibliotek |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Se [Lägg till anpassade Apache Hive-bibliotek när du skapar ett HDInsight-kluster](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Använd en skript åtgärd när klustret skapas

I det här avsnittet beskrivs de olika sätten att använda skript åtgärder när du skapar ett HDInsight-kluster.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Använd en skript åtgärd när du skapar kluster från Azure Portal

1. Börja skapa ett kluster enligt beskrivningen i [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md). När klustret skapas, kommer du till sidan __kluster Sammanfattning__ . På sidan __kluster Sammanfattning__ väljer du länken __Redigera__ för __Avancerade inställningar__.

    ![Avancerade inställningar för Azure Portal Cluster](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

1. I avsnittet __Avancerade inställningar__ väljer du __skript åtgärder__. I avsnittet __skript åtgärder__ väljer du __+ Skicka ny__.

    ![Portal skript åtgärder skicka ny](./media/hdinsight-hadoop-customize-cluster-linux/add-new-script-action.png)

1. Använd posten __Välj en skript__ för att välja ett förtillverkade skript. Välj __anpassad__om du vill använda ett anpassat skript. Ange sedan __namnet__ och __bash skript-URI__ för skriptet.

    ![Lägg till ett skript i formuläret Välj skript](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    I följande tabell beskrivs elementen i formuläret:

    | Egenskap | Value |
    | --- | --- |
    | Välj ett skript | Välj __anpassad__om du vill använda ett eget skript. Annars väljer du något av de angivna skripten. |
    | Name |Ange ett namn för skript åtgärden. |
    | Bash-skript-URI |Ange URI: n för skriptet. |
    | Head/Worker/ZooKeeper |Ange noderna som skriptet körs på: **Head**-, **Worker**-eller **ZooKeeper**. |
    | Parametrars |Ange parametrarna, om det krävs av skriptet. |

    Använd posten __Behåll den här skript åtgärden__ för att kontrol lera att skriptet används vid skalnings åtgärder.

1. Välj __skapa__ för att spara skriptet. Sedan kan du använda __+ Skicka ny__ för att lägga till ett annat skript.

    ![Åtgärder för flera skript i HDInsight](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    När du är klar med att lägga till skript väljer du knappen __Välj__ och klickar sedan på __Nästa__ för att återgå till avsnittet __kluster Sammanfattning__ .

1. Skapa klustret genom att välja __skapa__ från __kluster sammanfattnings__ valet.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Använda en skript åtgärd från Azure Resource Manager mallar

Skript åtgärder kan användas med Azure Resource Manager mallar. Ett exempel finns i [skapa HDInsight Linux-kluster och köra en skript åtgärd](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

I det här exemplet läggs skript åtgärden till med hjälp av följande kod:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Få mer information om hur du distribuerar en mall:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Använd en skript åtgärd när klustret skapas från Azure PowerShell

I det här avsnittet använder du cmdleten [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) för att anropa skript för att anpassa ett kluster. Innan du börjar ska du kontrol lera att du installerar och konfigurerar Azure PowerShell. Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande skript visar hur du använder en skript åtgärd när du skapar ett kluster med hjälp av PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Det kan ta flera minuter innan klustret skapas.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Använd en skript åtgärd när du skapar kluster från HDInsight .NET SDK

HDInsight .NET SDK innehåller klient bibliotek som gör det enklare att arbeta med HDInsight från ett .NET-program. Ett kod exempel finns i [skapa Linux-baserade kluster i HDInsight med hjälp av .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Tillämpa en skript åtgärd på ett kluster som körs

I det här avsnittet beskrivs hur du tillämpar skript åtgärder på ett kluster som körs.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Tillämpa en skript åtgärd på ett kluster som körs från Azure Portal

Gå till [Azure Portal](https://portal.azure.com):

1. Välj **alla tjänster**på den vänstra menyn.

1. Under **Analytics**väljer du **HDInsight-kluster**.

1. Välj ditt kluster i listan, så öppnas standardvyn.

1. I standardvyn väljer du **skript åtgärder**under **Inställningar**.

1. Överst på sidan **skript åtgärder** väljer du **+ Skicka ny**.

    ![Lägga till ett skript i ett kluster som körs](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Använd posten __Välj en skript__ för att välja ett förtillverkade skript. Välj __anpassad__om du vill använda ett anpassat skript. Ange sedan __namnet__ och __bash skript-URI__ för skriptet.

    ![Lägg till ett skript i formuläret Välj skript](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    I följande tabell beskrivs elementen i formuläret:

    | Egenskap | Value |
    | --- | --- |
    | Välj ett skript | Välj __anpassad__om du vill använda ett eget skript. Annars väljer du ett tillhandahållet skript. |
    | Name |Ange ett namn för skript åtgärden. |
    | Bash-skript-URI |Ange URI: n för skriptet. |
    | HEAD/Worker/ZooKeeper |Ange noderna som skriptet körs på: **Head**-, **Worker**-eller **ZooKeeper**. |
    | Parametrars |Ange parametrarna, om det krävs av skriptet. |

    Använd posten __Behåll den här skript åtgärden__ för att kontrol lera att skriptet används vid skalnings åtgärder.

1. Slutligen väljer du knappen **skapa** för att tillämpa skriptet på klustret.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Tillämpa en skript åtgärd på ett kluster som körs från Azure PowerShell

Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](https://docs.microsoft.com/powershell/azure/overview).

I följande exempel visas hur du använder en skript åtgärd för ett kluster som körs:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

När åtgärden har slutförts visas information som liknar följande text:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Tillämpa en skript åtgärd på ett kluster som körs från Azure CLI

Innan du börjar ska du kontrol lera att du installerar och konfigurerar Azure CLI. Mer information finns i [installera den klassiska Azure-CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Växla till Azure Resource Manager läge:

    ```bash
    azure config mode arm
    ```

2. Autentisera till din Azure-prenumeration:

    ```bash
    azure login
    ```

3. Tillämpa en skript åtgärd på ett kluster som körs:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Om du utelämnar parametrar för det här kommandot uppmanas du att göra det. Om skriptet som du anger med `-u` accepterar parametrar kan du ange dem med `-p` hjälp av-parametern.

    Giltiga nodtyper är `headnode`, `workernode`och `zookeeper`. Om skriptet ska tillämpas på flera nodtyper anger du typerna avgränsade med semikolon `;`. Till exempel `-n headnode;workernode`.

    Lägg till `--persistOnSuccess`för att spara skriptet. Du kan också Spara skriptet senare med hjälp `azure hdinsight script-action persisted set`av.

    När jobbet har slutförts visas utdata som följande text:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Tillämpa en skript åtgärd på ett kluster som körs med hjälp av REST API

Se [kluster REST API i Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Tillämpa en skript åtgärd på ett kluster som körs från HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att tillämpa skript i ett kluster finns i [tillämpa en skript åtgärd mot ett Linux-baserat HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Visa historik och befordran och degradering av skript åtgärder

### <a name="the-azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster**på den vänstra menyn.

1. Under **Analytics**väljer du **HDInsight-kluster**.

1. Välj ditt kluster i listan, så öppnas standardvyn.

1. I standardvyn väljer du **skript åtgärder**under **Inställningar**.

1. En historik över skript för det här klustret visas i avsnittet skript åtgärder. Den här informationen innehåller en lista över bestående skript. Följande skärm bild visar att solr-skriptet har körts på det här klustret. Skärm bilden visar inte några sparade skript.

    ![Portal skript åtgärder skicka historik](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Välj ett skript från historiken för att visa avsnittet **Egenskaper** för det här skriptet. Överst på skärmen kan du köra skriptet igen eller befordra det.

    ![Egenskaper för skript åtgärder befordran](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Du kan också välja ellipsen ( **...** ) till höger om poster i avsnittet skript åtgärder för att utföra åtgärder.

    ![Borttagning av beständiga skript åtgärder](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Kommandon | Funktion |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Hämta information om beständiga skript åtgärder. |
| `Get-AzHDInsightScriptActionHistory` |Hämta en historik över skript åtgärder som tillämpas på klustret eller information för ett speciellt skript. |
| `Set-AzHDInsightPersistedScriptAction` |Flytta upp en ad hoc-skript åtgärd till en sparad skript åtgärd. |
| `Remove-AzHDInsightPersistedScriptAction` |Nedgradera en bestående skript åtgärd till en ad hoc-åtgärd. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction`ångrar inte de åtgärder som utförs av ett skript. Den här cmdleten tar bara bort den sparade flaggan.

Följande exempel skript visar hur du använder cmdlet: ar för att befordra och sedan nedgradera ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Den klassiska Azure CLI

| Kommandon | Funktion |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Hämta en lista över beständiga skript åtgärder. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Hämta information om en speciell bestående skript åtgärd. |
| `azure hdinsight script-action history list <clustername>` |Hämta en historik över skript åtgärder som tillämpas på klustret. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Hämta information om en speciell skript åtgärd. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Flytta upp en ad hoc-skript åtgärd till en sparad skript åtgärd. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Nedgradera en bestående skript åtgärd till en ad hoc-åtgärd. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete`ångrar inte de åtgärder som utförs av ett skript. Den här cmdleten tar bara bort den sparade flaggan.

### <a name="the-hdinsight-net-sdk"></a>HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att hämta skript historik från ett kluster, befordra eller nedgradera skript finns i [tillämpa en skript åtgärd mot ett Linux-baserat HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Det här exemplet visar också hur du installerar ett HDInsight-program med hjälp av .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för program med öppen källkod som används i HDInsight-kluster

Microsoft Azure HDInsights tjänsten använder ett eko system med tekniker med öppen källkod som bildas runt Apache Hadoop. Microsoft Azure ger en allmän support nivå för tekniker med öppen källkod. Mer information finns i avsnittet **support omfattning** i [vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare en nivå av stöd för inbyggda komponenter.

Det finns två typer av komponenter med öppen källkod i HDInsight-tjänsten:

* **Inbyggda komponenter**. Dessa komponenter förinstalleras i HDInsight-kluster och tillhandahåller kärn funktioner i klustret. Följande komponenter tillhör den här kategorin:

  * [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Hanteraren.
  * [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)för Hive-frågespråket.
  * [Apache Mahout](https://mahout.apache.org/). 
    
    En fullständig lista över kluster komponenter finns i [vilka Apache Hadoop-komponenter och-versioner som är tillgängliga med HDInsight?](hdinsight-component-versioning.md)

* **Anpassade komponenter**. Som användare av klustret kan du installera eller använda i din arbets belastning, vilken komponent som helst som är tillgänglig i communityn eller som du har skapat.

> [!WARNING]  
> Komponenter som ingår i HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Microsoft Support kanske kan lösa problemet. Du kan också be dig att engagera tillgängliga kanaler för teknikerna med öppen källkod där djupgående expertis för tekniken hittas. Många Community-webbplatser kan användas. Exempel är [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) och [Stack Overflow](https://stackoverflow.com).
>
> Apache-projekt har också projekt webbplatser på [Apache-webbplatsen](https://apache.org). Ett exempel är [Hadoop](https://hadoop.apache.org/).

HDInsight-tjänsten tillhandahåller flera olika sätt att använda anpassade komponenter. Samma nivå av support gäller, oavsett hur en komponent används eller installeras i klustret. I följande lista beskrivs de vanligaste sätten att använda anpassade komponenter i HDInsight-kluster:

1. **Jobb sändning**. Hadoop eller andra typer av jobb som kör eller använder anpassade komponenter kan skickas till klustret.

2. **Kluster anpassning**. När klustret skapas kan du ange ytterligare inställningar och anpassade komponenter som är installerade på klusternoderna.

3. **Exempel**. För populära anpassade komponenter kan Microsoft och andra tillhandahålla exempel på hur dessa komponenter kan användas i HDInsight-kluster. De här exemplen tillhandahålls utan support.

## <a name="troubleshooting"></a>Felsökning

Du kan använda Ambari-webbgränssnittet för att visa information som loggats av skript åtgärder. Om skriptet Miss lyckas när klustret skapas är loggarna också tillgängliga i det standard lagrings konto som är associerat med klustret. Det här avsnittet innehåller information om hur du hämtar loggarna med båda dessa alternativ.

### <a name="the-apache-ambari-web-ui"></a>Webb gränssnittet för Apache Ambari

1. Gå till https://CLUSTERNAME.azurehdinsight.net i webbläsaren. Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

    När du uppmanas till det anger du administratörens konto namn, **administratör**och lösen ord för klustret. Du kanske måste ange administratörs behörigheten på ett webb formulär igen.

2. Välj **Ops** -posten från fältet överst på sidan. En lista visar aktuella och tidigare åtgärder som utförs på klustret via Ambari.

    ![Ambari Web UI-fältet med OPS valt](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

3. Hitta de poster som har **kört\_customscriptaction** i kolumnen **åtgärder** . Dessa poster skapas när skript åtgärderna körs.

    ![Åtgärds åtgärder för Apache Ambari-skript](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Om du vill visa **STDOUT** -och **stderr** -utdata väljer du posten **run\customscriptaction** och ökar detalj nivån genom länkarna. Dessa utdata skapas när skriptet körs och kan ha användbar information.

### <a name="access-logs-from-the-default-storage-account"></a>Åtkomst loggar från standard lagrings kontot

Om det inte går att skapa ett kluster på grund av ett skript fel sparas loggarna i klustrets lagrings konto.

* Lagrings loggarna är tillgängliga `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`på.

    ![Skript åtgärds loggar](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Under den här katalogen ordnas loggarna separat för **huvudnoden**, **arbetsnoden**och **Zookeeper-noden**. Se följande exempel:

    * **Huvudnoden**:`<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Arbets nod**:`<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper-nod**:`<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Alla **STDOUT** och **stderr** för motsvarande värd överförs till lagrings kontot. Det finns en **output-\*. txt** och **errors\*-. txt** för varje skript åtgärd. Filen **output-*. txt** innehåller information om URI: n för skriptet som kördes på värden. Följande text är ett exempel på den här informationen:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Det är möjligt att du upprepade gånger skapar ett skript åtgärds kluster med samma namn. I så fall kan du särskilja relevanta loggar baserat på namnet på mappen för **datum** . Mappstrukturen för ett **kluster, till exempel, som**skapas på olika datum ser ut ungefär som i följande logg poster:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Om du skapar ett skript åtgärds kluster med samma namn samma dag kan du använda det unika prefixet för att identifiera relevanta loggfiler.

* Om du skapar ett kluster nära 12:00 AM, midnatt, är det möjligt att loggfilerna sträcker sig över två dagar. I så fall visas två olika date-mappar för samma kluster.

* Det kan ta upp till fem minuter att ladda upp loggfiler till standard behållaren, särskilt för stora kluster. Så om du vill komma åt loggarna bör du inte omedelbart ta bort klustret om en skript åtgärd Miss lyckas.

### <a name="ambari-watchdog"></a>Ambari övervaknings enhet

> [!WARNING]  
> Ändra inte lösen ordet för Ambari-övervaknings enheten, hdinsightwatchdog, på ditt Linux-baserade HDInsight-kluster. Om du ändrar lösen ordet för det här kontot bryts möjligheten att köra nya skript åtgärder i HDInsight-klustret.

### <a name="cant-import-name-blobservice"></a>Det går inte att importera namnet BlobService

__Symptom__. Skript åtgärden Miss lyckas. Text som liknar följande fel visas när du visar åtgärden i Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Orsak__. Det här felet uppstår om du uppgraderar python-Azure Storage-klienten som ingår i HDInsight-klustret. HDInsight förväntar sig Azure Storage 0.20.0-klient.

__Lösning__. För att lösa det här felet ansluter du manuellt till varje klusternod med `ssh`hjälp av. Kör följande kommando för att installera om rätt version av lagrings klienten:

```bash
sudo pip install azure-storage==0.20.0
```

Information om hur du ansluter till klustret med SSH finns i [ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historik visar inte de skript som används när klustret skapas

Om klustret skapades före den 15 mars 2016 kanske du inte ser någon post i åtgärds historiken för skriptet. Att ändra storlek på klustret gör att skripten visas i skript åtgärds historik.

Det finns två undantag:

* Klustret skapades före den 1 september 2015. Det här datumet är när skript åtgärder introducerades. Alla kluster som skapats före det här datumet kunde inte använda skript åtgärder för att skapa kluster.

* Du har använt flera skript åtgärder under skapandet av klustret. Eller så har du använt samma namn för flera skript eller samma namn, samma URI, men olika parametrar för flera skript. I dessa fall får du följande fel meddelande:

    Inga nya skript åtgärder kan köras på det här klustret på grund av motstridiga skript namn i befintliga skript. Skript namn som anges när klustret skapas måste vara unika. Befintliga skript körs vid storleks ändring.

## <a name="next-steps"></a>Nästa steg

* [Utveckla skript åtgärds skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installera och använda Apache Giraph i HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md)
* [Lägg till ytterligare lagrings utrymme i ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Steg när klustret skapas"
