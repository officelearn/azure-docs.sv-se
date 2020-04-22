---
title: Anpassa Azure HDInsight-kluster med hjälp av skriptåtgärder
description: Lägg till anpassade komponenter i HDInsight-kluster med hjälp av skriptåtgärder. Skriptåtgärder är Bash-skript som kan användas för att anpassa klusterkonfigurationen. Eller lägg till ytterligare tjänster och verktyg som Hue, Solr eller R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 434d4adb763fd0e0a29c065ce051bfd4fa461180
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770741"
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

Någon med minst deltagaråtkomst till Azure-prenumerationen måste ha registrerat providern tidigare. Providerregistrering sker när en användare med deltagares åtkomst till prenumerationen skapar en resurs. För utan att skapa en resurs, se [registrera en provider med hjälp av REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

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

* Kan bevaras `ad hoc`eller .

    Beständiga skriptåtgärder måste ha ett unikt namn. Beständiga skript används för att anpassa nya arbetsnoder som läggs till i klustret genom skalningsåtgärder. Ett beständigt skript kan också tillämpa ändringar på en annan nodtyp när skalningsåtgärder inträffar. Ett exempel är en huvudnod.

    `Ad hoc`skript inte sparas. Skriptåtgärder som används när klustret skapas sparas automatiskt. De tillämpas inte på arbetsnoder som läggs till i klustret när skriptet har körts. Sedan kan du `ad hoc` befordra ett skript till ett beständigt `ad hoc` skript eller nedgradera ett beständigt skript till ett skript. Skript som misslyckas sparas inte, även om du specifikt anger att de ska vara det.

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

Du kan utföra åtgärder som att stoppa och starta tjänster, inklusive Apache Hadoop-relaterade tjänster. Om du stoppar tjänster, se till att Ambari och andra Hadoop-relaterade tjänster körs innan skriptet är klart. Dessa nödvändiga tjänster avgör hälsotillståndet för klustret medan det skapas.

När klustret skapas kan du använda många skriptåtgärder samtidigt. Dessa skript anropas i den ordning som de angavs i.

> [!IMPORTANT]  
> Skriptåtgärder måste slutföras inom 60 minuter, eller så är de time out. Under klusteretablering körs skriptet samtidigt med andra inställnings- och konfigurationsprocesser. Konkurrens om resurser som CPU-tid eller nätverksbandbredd kan orsaka att skriptet tar längre tid att slutföra än det gör i utvecklingsmiljön.
>
> Undvik uppgifter som att hämta och kompilera program från källan för att minimera hur mycket tid det tar att köra skriptet. Förkompilera program och lagra binärfilen i Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Skriptåtgärd på ett kluster som körs

Ett skriptfel i ett kluster som körs redan gör att klustret inte ändras till ett misslyckat tillstånd. När ett skript har slutförts ska klustret återgå till ett körläge. Även om klustret har ett tillstånd som körs kan det misslyckade skriptet ha brutit saker. Ett skript kan till exempel ta bort filer som behövs av klustret.

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

1. Du kan också välja ellips, **...**, till höger om poster i skriptåtgärder avsnittet för att göra åtgärder.

    ![Beständiga skriptåtgärder tar bort](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funktion |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Hämta information om beständiga skriptåtgärder. Den här cmdleten ångrar inte de åtgärder som utförs av ett skript, den tar bara bort den beständiga flaggan.|
| `Get-AzHDInsightScriptActionHistory` |Hämta en historik över skriptåtgärder som tillämpas på klustret eller information om ett visst skript. |
| `Set-AzHDInsightPersistedScriptAction` |Befordra `ad hoc` en skriptåtgärd till en beständig skriptåtgärd. |
| `Remove-AzHDInsightPersistedScriptAction` |Nedgradera en beständig skriptåtgärd `ad hoc` till en åtgärd. |

Följande exempelskript visas med cmdlets för att befordra och sedan nedgradera ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Kommando | Beskrivning |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Tar bort en angiven beständig skriptåtgärd i klustret. Det här kommandot ångrar inte de åtgärder som utförs av ett skript, det tar bara bort den kvarstående flaggan.|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Kör skriptåtgärder i det angivna HDInsight-klustret.|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Visar en lista över alla beständiga skriptåtgärder för det angivna klustret. |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Visar en lista över alla skripts körningshistorik för det angivna klustret.|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Befordrar den angivna ad hoc-skriptkörningen till ett beständigt skript.|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Hämtar skriptkörningsdetaljerna för det angivna skriptkörnings-ID:et.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att hämta skripthistorik från ett kluster, befordra eller nedgradera skript finns i [Använda en skriptåtgärd mot ett Linux-baserat HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Det här exemplet visar också hur du installerar ett HDInsight-program med hjälp av .NET SDK.

## <a name="next-steps"></a>Nästa steg

* [Utveckla skript åtgärd skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Lägga till ytterligare lagringsutrymme i ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)
* [Felsöka skriptåtgärder](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Faser under skapande av kluster"
