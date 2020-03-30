---
title: Anpassa Azure HDInsight-kluster med hjälp av skriptåtgärder
description: Lägg till anpassade komponenter i HDInsight-kluster med hjälp av skriptåtgärder. Skriptåtgärder är Bash-skript som kan användas för att anpassa klusterkonfigurationen eller lägga till ytterligare tjänster och verktyg som Hue, Solr eller R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272505"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Anpassa Azure HDInsight-kluster med hjälp av skriptåtgärder

Azure HDInsight tillhandahåller en konfigurationsmetod som kallas **skriptåtgärder** som anropar anpassade skript för att anpassa klustret. Dessa skript används för att installera ytterligare komponenter och ändra konfigurationsinställningar. Skriptåtgärder kan användas under eller efter att klustret skapas.

Skriptåtgärder kan också publiceras på Azure Marketplace som ett HDInsight-program. Mer information om HDInsight-program finns [i Publicera ett HDInsight-program på Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Behörigheter

För ett domänanslutet HDInsight-kluster finns det två Apache Ambari-behörigheter som krävs när du använder skriptåtgärder med klustret:

* **AMBARI. KÖR\_\_ANPASSAT KOMMANDO**. Rollen Ambari-administratör har den här behörigheten som standard.
* **KLUSTER. KÖR\_\_ANPASSAT KOMMANDO**. Både HDInsight-klusteradministratören och Ambari-administratören har den här behörigheten som standard.

Mer information om hur du arbetar med behörigheter med domänansluten HDInsight finns i [Hantera HDInsight-kluster med Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Åtkomstkontroll

Om du inte är administratör eller ägare av din Azure-prenumeration måste ditt konto ha minst deltagaråtkomst till resursgruppen som innehåller HDInsight-klustret.

Om du skapar ett HDInsight-kluster måste någon med minst deltagaråtkomst till Azure-prenumerationen ha registrerat providern för HDInsight. Leverantörregistrering sker när en användare med deltagaråtkomst skapar en resurs för första gången på en prenumeration. Det kan också göras utan att skapa en resurs om du [registrerar en leverantör med hjälp av REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Få mer information om hur du arbetar med åtkomsthantering:

* [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
* [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Förstå skriptåtgärder

En skriptåtgärd är Bash-skript som körs på noderna i ett HDInsight-kluster. Egenskaper och funktioner i skriptåtgärder är följande:

* Måste lagras på en URI som är tillgänglig från HDInsight-klustret. Följande är möjliga lagringsplatser:

    * För vanliga kluster:

      * ADLS Gen1: Tjänstens huvudnamn HDInsight använder för att komma åt DataSjölagring måste ha läsbehörighet till skriptet. URI-formatet för skript som lagras i `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`Data Lake Storage Gen1 är .

      * En blob i ett Azure Storage-konto som är antingen det primära eller ytterligare lagringskontot för HDInsight-klustret. HDInsight beviljas åtkomst till båda dessa typer av lagringskonton när kluster skapas.

        > [!IMPORTANT]  
        > Rotera inte lagringsnyckeln på det här Azure Storage-kontot, eftersom det kommer att orsaka efterföljande skriptåtgärder med skript som lagras där misslyckas.

      * En offentlig fildelningstjänst som är tillgänglig via http:// sökvägar. Exempel är Azure Blob, GitHub, OneDrive. Se exempelvis [URI:er, exempel på skriptåtgärdsskript](#example-script-action-scripts).

     * För kluster med ESP stöds wasb:// eller wasbs:// eller http[s]:// URI:er.

* Kan begränsas till att köras på endast vissa nodtyper. Exempel är huvudnoder eller arbetsnoder.

* Kan bevaras eller ad hoc.

    Beständiga skriptåtgärder måste ha ett unikt namn. Beständiga skript används för att anpassa nya arbetsnoder som läggs till i klustret genom skalningsåtgärder. Ett beständigt skript kan också tillämpa ändringar på en annan nodtyp när skalningsåtgärder inträffar. Ett exempel är en huvudnod.

    Ad hoc-skript sparas inte. Skriptåtgärder som används när klustret skapas sparas automatiskt. De tillämpas inte på arbetsnoder som läggs till i klustret när skriptet har körts. Sedan kan du befordra ett ad hoc-skript till ett beständigt skript eller nedgradera ett beständigt skript till ett ad hoc-skript. Skript som misslyckas sparas inte, även om du specifikt anger att de ska vara det.

* Kan acceptera parametrar som används av skriptet under körningen.

* Kör med privilegier på rotnivå på klusternoderna.

* Kan användas via Azure-portalen, Azure PowerShell, Azure CLI eller HDInsight .NET SDK.

Klustret behåller en historik över alla skript som har körts. Historiken hjälper när du behöver hitta ID för ett skript för befordran eller degradering åtgärder.

> [!IMPORTANT]  
> Det finns inget automatiskt sätt att ångra de ändringar som gjorts av en skriptåtgärd. Antingen bakåtförändrar ändringarna manuellt eller anger ett skript som återför dem.

### <a name="script-action-in-the-cluster-creation-process"></a>Skriptåtgärd i processen för att skapa kluster

Skriptåtgärder som används när klustret skapas skiljer sig något från skriptåtgärder som körs i ett befintligt kluster:

* Skriptet sparas automatiskt.

* Ett fel i skriptet kan orsaka att klusterskapandeprocessen misslyckas.

Följande diagram illustrerar när skriptåtgärden körs under skapandeprocessen:

![Anpassning och faser för HDInsight-kluster under klustergenerering][img-hdi-cluster-states]

Skriptet körs medan HDInsight konfigureras. Skriptet körs parallellt på alla angivna noder i klustret. Den körs med rotprivilegier på noderna.

Du kan utföra åtgärder som att stoppa och starta tjänster, inklusive Apache Hadoop-relaterade tjänster. Om du stoppar tjänster kontrollerar du att Ambari-tjänsten och andra Hadoop-relaterade tjänster körs innan skriptet är klart. Dessa tjänster krävs för att framgångsrikt fastställa hälsotillståndet och tillståndet för klustret medan det skapas.

När klustret skapas kan du använda många skriptåtgärder samtidigt. Dessa skript anropas i den ordning som de angavs i.

> [!IMPORTANT]  
> Skriptåtgärder måste slutföras inom 60 minuter, eller så är de time out. Under klusteretablering körs skriptet samtidigt med andra inställnings- och konfigurationsprocesser. Konkurrens om resurser som CPU-tid eller nätverksbandbredd kan orsaka att skriptet tar längre tid att slutföra än det gör i utvecklingsmiljön.
>
> Undvik uppgifter som att hämta och kompilera program från källan för att minimera hur mycket tid det tar att köra skriptet. Förkompilera program och lagra binärfilen i Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Skriptåtgärd på ett kluster som körs

Ett fel i ett skript som körs på ett kluster som körs redan gör det inte automatiskt att klustret ändras till ett misslyckat tillstånd. När ett skript har slutförts ska klustret återgå till ett körläge. Även om klustret har ett tillstånd som körs kan det misslyckade skriptet ha brutit saker. Ett skript kan till exempel ta bort filer som behövs av klustret.

Skriptåtgärder körs med rotprivilegier. Se till att du förstår vad ett skript gör innan du använder det på klustret.

När du använder ett skript på ett kluster ändras klustertillståndet från **Löpning** till **Accepterad**. Sedan ändras till **HDInsight konfiguration** och slutligen tillbaka till **Kör** för framgångsrika skript. Skriptstatusen loggas i skriptåtgärdshistoriken. Den här informationen talar om för dig om skriptet lyckades eller misslyckades. `Get-AzHDInsightScriptActionHistory` PowerShell-cmdlet visar till exempel status för ett skript. Den returnerar information som liknar följande text:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Om du ändrar klusteranvändaren, administratören, lösenordet efter att klustret har skapats kan skriptåtgärder som körs mot det här klustret misslyckas. Om du har några beständiga skriptåtgärder som riktar sig till arbetsnoder kan dessa skript misslyckas när du skalar klustret.

## <a name="example-script-action-scripts"></a>Exempel på skriptåtgärdsskript

Skriptåtgärdsskript kan användas via följande verktyg:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight tillhandahåller skript för att installera följande komponenter på HDInsight-kluster:

| Namn | Skript |
| --- | --- |
| Lägga till ett Azure Storage-konto |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Se [Lägga till ytterligare lagringskonton i HDInsight](hdinsight-hadoop-add-storage.md). |
| Installera nyans |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Se [Installera och använda Hue på HDInsight Hadoop-kluster](hdinsight-hadoop-hue-linux.md). |
| Förladda Hive-bibliotek |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Se [Lägga till anpassade Apache Hive-bibliotek när du skapar ditt HDInsight-kluster](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Skriptåtgärd under klusterskapande

I det här avsnittet beskrivs olika sätt att använda skriptåtgärder när du skapar ett HDInsight-kluster.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Använda en skriptåtgärd när kluster skapas från Azure-portalen

1. Börja skapa ett kluster enligt beskrivningen i [Skapa Linux-baserade kluster i HDInsight med hjälp av Azure-portalen](hdinsight-hadoop-create-linux-clusters-portal.md). På fliken **Konfiguration + prissättning** väljer du + Lägg till **skriptåtgärd**.

    ![Åtgärden för Azure Portal-klusterskript](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Använd posten __Välj ett skript__ för att välja ett förgjort skript. Om du vill använda ett anpassat skript väljer du __Anpassad__. Ange sedan __skript-URI för__ __namn__ och bash för skriptet.

    ![Lägga till ett skript i formuläret välj skript](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    I följande tabell beskrivs elementen i formuläret:

    | Egenskap | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ditt eget skript väljer du __Anpassad__. Annars väljer du ett av de angivna skripten. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash skript URI |Ange URI för skriptet. |
    | Huvud/Arbetare/ZooKeeper |Ange de noder som skriptet körs på: **Huvud,** **Arbetare**eller **ZooKeeper**. |
    | Parametrar |Ange parametrarna om det krävs av skriptet. |

    Använd åtgärdsposten __Bevara skriptet__ för att kontrollera att skriptet används under skalningsåtgärder.

1. Välj __Skapa__ för att spara skriptet. Sedan kan du använda __+ Skicka nya__ för att lägga till ett annat skript.

    ![HdInsight flera skriptåtgärder](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    När du är klar med att lägga till skript går du tillbaka till fliken **Konfiguration + prissättning.**

1. Slutför de återstående stegen för att skapa kluster som vanligt.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Använda en skriptåtgärd från Azure Resource Manager-mallar

Skriptåtgärder kan användas med Azure Resource Manager-mallar. Ett exempel finns i [Skapa HDInsight Linux-kluster och kör en skriptåtgärd](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

I det här exemplet läggs skriptåtgärden till med hjälp av följande kod:

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

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Använda en skriptåtgärd när kluster skapas från Azure PowerShell

I det här avsnittet använder du cmdleten [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) för att anropa skript för att anpassa ett kluster. Innan du börjar kontrollerar du att du installerar och konfigurerar Azure PowerShell. Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](https://docs.microsoft.com/powershell/azure/overview).

Följande skript visar hur du använder en skriptåtgärd när du skapar ett kluster med PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Det kan ta flera minuter innan klustret skapas.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Använda en skriptåtgärd när klustret skapas från HDInsight .NET SDK

HDInsight .NET SDK tillhandahåller klientbibliotek som gör det enklare att arbeta med HDInsight från ett .NET-program. Ett kodexempel finns i [Skriptåtgärder](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Skriptåtgärd till ett kluster som körs

I det här avsnittet beskrivs hur du använder skriptåtgärder på ett kluster som körs.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Använda en skriptåtgärd på ett kluster som körs från Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) och hitta ditt kluster.

1. Välj **Skriptåtgärder**under **Inställningar**i standardvyn .

1. Välj **+ Skicka nya**på sidan **Skriptåtgärder** .

    ![Lägga till ett skript i ett kluster som körs](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Använd posten __Välj ett skript__ för att välja ett förgjort skript. Om du vill använda ett anpassat skript väljer du __Anpassad__. Ange sedan __skript-URI för__ __namn__ och bash för skriptet.

    ![Lägga till ett skript i formuläret välj skript](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    I följande tabell beskrivs elementen i formuläret:

    | Egenskap | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ditt eget skript väljer du __anpassad__. Annars väljer du ett medföljande skript. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash skript URI |Ange URI för skriptet. |
    | Huvud/Arbetare/Zookeeper |Ange de noder som skriptet körs på: **Huvud,** **Arbetare**eller **ZooKeeper**. |
    | Parametrar |Ange parametrarna om det krävs av skriptet. |

    Använd åtgärdsposten __Bevara skriptet__ för att kontrollera att skriptet används under skalningsåtgärder.

1. Slutligen väljer du knappen **Skapa** för att använda skriptet på klustret.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Använda en skriptåtgärd på ett kluster som körs från Azure PowerShell

Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](https://docs.microsoft.com/powershell/azure/overview). I följande exempel visas hur du använder en skriptåtgärd på ett kluster som körs:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

När åtgärden är klar får du information som liknar följande text:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Använda en skriptåtgärd på ett kluster som körs från Azure CLI

Innan du börjar kontrollerar du att du installerar och konfigurerar Azure CLI. Se till att du har den senaste versionen. Mer information finns [i Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Autentisera till din Azure-prenumeration:

    ```azurecli
    az login
    ```

1. Använda en skriptåtgärd på ett kluster som körs:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Giltiga roller `headnode` `workernode`är `zookeepernode` `edgenode`, , , . Om skriptet ska tillämpas på flera nodtyper avgränsar du rollerna efter ett blanksteg. Till exempel `--roles headnode workernode`.

    Om du vill `--persist-on-success`spara skriptet lägger du till . Du kan också spara skriptet senare med hjälp `az hdinsight script-action promote`av .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Använda en skriptåtgärd på ett kluster som körs med REST API

Se [Kluster-REST API i Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Använda en skriptåtgärd på ett kluster som körs från HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att använda skript på ett kluster finns i [Använda en skriptåtgärd mot ett Linux-baserat HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Visa historik och befordra och nedgradera skriptåtgärder

### <a name="the-azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com) och hitta ditt kluster.

1. Välj **Skriptåtgärder**under **Inställningar**i standardvyn .

1. En historik över skript för det här klustret visas i avsnittet skriptåtgärder. Den här informationen innehåller en lista över beständiga skript. Följande skärmbild visar att Solr-skriptet har körts på det här klustret. Skärmbilden visar inga beständiga skript.

    ![Portalskriptåtgärder skickar historik](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Välj ett skript från historiken om du vill visa avsnittet **Egenskaper** för det här skriptet. Högst upp på skärmen kan du köra skriptet igen eller marknadsföra det.

    ![Egenskaper för skriptåtgärder befordras](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Du kan också välja ellips, **...**, till höger om poster i skriptåtgärder avsnitt för att utföra åtgärder.

    ![Beständiga skriptåtgärder tar bort](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funktion |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Hämta information om beständiga skriptåtgärder. Den här cmdleten ångrar inte de åtgärder som utförs av ett skript, den tar bara bort den beständiga flaggan.|
| `Get-AzHDInsightScriptActionHistory` |Hämta en historik över skriptåtgärder som tillämpas på klustret eller information om ett visst skript. |
| `Set-AzHDInsightPersistedScriptAction` |Befordra en ad hoc-skriptåtgärd till en beständig skriptåtgärd. |
| `Remove-AzHDInsightPersistedScriptAction` |Nedgradera en beständig skriptåtgärd till en ad hoc-åtgärd. |

Följande exempelskript visas med cmdlets för att befordra och sedan nedgradera ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Kommando | Beskrivning |
| --- | --- |
| [az hdinsight script-action delete az hdinsight script-action delete az hdinsight script-action delete az hd](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Tar bort en angiven beständig skriptåtgärd i klustret. Det här kommandot ångrar inte de åtgärder som utförs av ett skript, det tar bara bort den beständiga flaggan.|
|[az hdinsight script-action kör](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Kör skriptåtgärder i det angivna HDInsight-klustret.|
| [az hdinsight script-action lista](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Visar en lista över alla beständiga skriptåtgärder för det angivna klustret. |
|[az hdinsight script-action list-execution-historia](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Visar en lista över alla skripts körningshistorik för det angivna klustret.|
|[az hdinsight script-action främja](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Befordrar den angivna ad hoc-skriptkörningen till ett beständigt skript.|
|[az hdinsight script-action show-execution-details az hdinsight script-action show-execution-details az hdinsight script-action show-execution-details az hd](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Hämtar skriptkörningsdetaljerna för det angivna skriptkörnings-ID:et.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att hämta skripthistorik från ett kluster, befordra eller nedgradera skript finns i [Använda en skriptåtgärd mot ett Linux-baserat HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Det här exemplet visar också hur du installerar ett HDInsight-program med hjälp av .NET SDK.

## <a name="support-for-open-source-software"></a>Stöd för programvara med öppen källkod

Microsoft Azure HDInsight-tjänsten använder ett ekosystem av tekniker med öppen källkod som bildas runt Apache Hadoop. Microsoft Azure ger en allmän supportnivå för tekniker med öppen källkod. Mer information finns i avsnittet **Supportscope i** vanliga frågor om [Azure Support](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare stöd för inbyggda komponenter.

Två typer av komponenter med öppen källkod finns i HDInsight-tjänsten:

* **Inbyggda komponenter**. Dessa komponenter är förinstallerade på HDInsight-kluster och tillhandahåller kärnfunktioner i klustret. Följande komponenter tillhör den här kategorin:

  * [Apache Hadoop GARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * Hive-frågespråket [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout](https://mahout.apache.org/).

    En fullständig lista över klusterkomponenter finns i [Vilka är Apache Hadoop-komponenterna och versionerna tillgängliga med HDInsight?](hdinsight-component-versioning.md)

* **Anpassade komponenter**. Som användare av klustret kan du installera eller använda alla komponenter som är tillgängliga i communityn eller som skapats av dig i din arbetsbelastning.

> [!WARNING]  
> Komponenter som medföljer HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som är relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimligt stöd för att hjälpa dig att felsöka problemet ytterligare. Microsoft Support kanske kan lösa problemet. Eller så kan de be dig att engagera tillgängliga kanaler för öppen källkod teknik där djup expertis för den tekniken finns. Många community-webbplatser kan användas. Exempel är [MSDN forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) och [Stack Överflöd](https://stackoverflow.com).
>
> Apache projekt har också projektwebbplatser på [Apache webbplats](https://apache.org). Ett exempel är [Hadoop](https://hadoop.apache.org/).

HDInsight-tjänsten innehåller flera sätt att använda anpassade komponenter. Samma supportnivå gäller, oavsett hur en komponent används eller installeras i klustret. I följande lista beskrivs de vanligaste sätten att anpassade komponenter används i HDInsight-kluster:

1. **Jobbinlämning**. Hadoop eller andra typer av jobb som kör eller använder anpassade komponenter kan skickas till klustret.

2. **Anpassning av kluster**. När klustret skapas kan du ange ytterligare inställningar och anpassade komponenter som är installerade på klusternoderna.

3. **Prover**. För populära anpassade komponenter kan Microsoft och andra ge exempel på hur dessa komponenter kan användas på HDInsight-kluster. Dessa prover tillhandahålls utan stöd.

## <a name="troubleshooting"></a>Felsökning

Du kan använda webbgränssnittet i Ambari för att visa information som loggas av skriptåtgärder. Om skriptet misslyckas när klustret skapas är loggarna också tillgängliga i standardlagringskontot som är associerat med klustret. Det här avsnittet innehåller information om hur du hämtar loggarna med hjälp av båda dessa alternativ.

### <a name="the-apache-ambari-web-ui"></a>Apache Ambari webbgränssnitt

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

1. Välj **ops-posten** högst upp på sidan. En lista visar aktuella och tidigare åtgärder som utförs i klustret via Ambari.

    ![Ambari webbgränssnittsfält med ops valt](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. Leta reda på de poster som har **\_kört customscriptaction** i kolumnen **Operationer.** Dessa poster skapas när skriptåtgärderna körs.

    ![Åtgärder för apache ambari-skriptåtgärder](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Om du vill visa UTDATA FÖR **STDOUT** och **STDERR** markerar du posten **run\customscriptaction** och detaljgranskar nedåt genom länkarna. Den här utdata genereras när skriptet körs och kan ha användbar information.

### <a name="access-logs-from-the-default-storage-account"></a>Komma åt loggar från standardlagringskontot

Om klusterskapande misslyckas på grund av ett skriptfel sparas loggarna i klusterlagringskontot.

* Förvaringsloggarna finns `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`på .

    ![Åtgärdsloggar för skript](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Under den här katalogen ordnas loggarna separat för **headnode,** **arbetarnod**och **zookeeper-nod**. Se följande exempel:

    * **Headnode**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Arbetsnod:**`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper nod:**`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Alla **stdout** och **stderr** av motsvarande värd laddas upp till lagringskontot. Det finns en **utdata-\*.txt** och **fel-\*.txt** för varje skriptåtgärd. **Filen output-*.txt** innehåller information om URI-filen för skriptet som kördes på värden. Följande text är ett exempel på denna information:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Det är möjligt att du upprepade gånger skapar ett skriptåtgärdskluster med samma namn. I så fall kan du skilja relevanta loggar baserat på **mappnamnet DATUM.** Mappstrukturen för ett kluster, **mycluster**, som skapats på olika datum, liknar till exempel följande loggposter:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Om du skapar ett skriptåtgärdskluster med samma namn samma dag kan du använda det unika prefixet för att identifiera relevanta loggfiler.

* Om du skapar ett kluster nära 12:00, midnatt, är det möjligt att loggfilerna sträcker sig över två dagar. I så fall visas två olika datummappar för samma kluster.

* Det kan ta upp till fem minuter att överföra loggfiler till standardbehållaren, särskilt för stora kluster. Så om du vill komma åt loggarna bör du inte omedelbart ta bort klustret om en skriptåtgärd misslyckas.

### <a name="ambari-watchdog"></a>Ambari vakthund

> [!WARNING]  
> Ändra inte lösenordet för Ambari vakthund, hdinsightwatchdog, på din Linux-baserade HDInsight kluster. Om du ändrar lösenordet för det här kontot bryts möjligheten att köra nya skriptåtgärder i HDInsight-klustret.

### <a name="cant-import-name-blobservice"></a>Det går inte att importera namn BlobService

__Symptom__. Skriptåtgärden misslyckas. Text som liknar följande fel visas när du visar åtgärden i Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Orsak__. Det här felet uppstår om du uppgraderar Python Azure Storage-klienten som ingår i HDInsight-klustret. HDInsight förväntar sig Azure Storage-klient 0.20.0.

__Upplösning__. Lös det här felet genom att manuellt ansluta `ssh`till varje klusternod med hjälp av . Kör följande kommando för att installera om rätt lagringsklientversion:

```bash
sudo pip install azure-storage==0.20.0
```

Information om hur du ansluter till klustret med SSH finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historiken visar inte de skript som används när klustret skapades

Om klustret skapades före den 15 mars 2016 kanske du inte ser någon post i skriptåtgärdshistoriken. Om du ändrar storlek på klustret visas skripten i skriptåtgärdshistoriken.

Det finns två undantag:

* Klustret skapades före den 1 september 2015. Det här datumet är när skriptåtgärder introducerades. Alla kluster som skapats före det här datumet kunde inte ha använt skriptåtgärder för att skapa kluster.

* Du använde flera skriptåtgärder när klustret skapades. Du har också använt samma namn för flera skript eller samma namn, samma URI, men olika parametrar för flera skript. I dessa fall får du följande felmeddelande:

    Inga nya skriptåtgärder kan köras i det här klustret på grund av skriptnamn i konflikt i befintliga skript. Skriptnamn som anges vid klusterskapande måste vara unika. Befintliga skript körs på ändra storlek.

## <a name="next-steps"></a>Nästa steg

* [Utveckla skript åtgärd skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Lägga till ytterligare lagringsutrymme i ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Faser under skapande av kluster"
