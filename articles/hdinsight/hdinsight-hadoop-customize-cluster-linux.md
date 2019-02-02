---
title: Anpassa HDInsight-kluster med skriptåtgärder, Azure
description: Lägga till anpassade komponenter till Linux-baserade HDInsight-kluster med skriptåtgärder. Skriptåtgärder är Bash-skript som kan användas för att anpassa klusterkonfigurationen eller lägga till ytterligare tjänster och verktyg som Hue, Solr eller R.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: ea83d061f7160db04c847be66e79da60da78634d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662915"
---
# <a name="customize-linux-based-hdinsight-clusters-by-using-script-actions"></a>Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder

Azure HDInsight är en konfigurationsmetod som kallas **skriptåtgärder** som anropar anpassade skript för att anpassa klustret. Dessa skript används för att installera ytterligare komponenter och ändra konfigurationsinställningarna. Skriptåtgärder kan användas under eller när klustret har skapats.

> [!IMPORTANT]  
> Möjligheten att använda skriptåtgärder på ett pågående kluster är endast tillgängligt för Linux-baserade HDInsight-kluster.
>
> Linux är det enda operativsystem som används på HDInsight version 3.4 och senare. Mer information finns i [dras tillbaka HDInsight Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Skriptåtgärder kan även publiceras på Azure Marketplace som ett HDInsight-program. Mer information om HDInsight-program finns i [publicera ett HDInsight-program på Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Behörigheter

Det finns två Apache Ambari-behörigheter som krävs när du använder skriptåtgärder med klustret för ett domänanslutet HDInsight-kluster:

* **AMBARI.RUN\_CUSTOM\_COMMAND**. Ambari-administratörsroll har denna behörighet som standard.
* **KLUSTER. KÖR\_ANPASSADE\_KOMMANDOT**. HDInsight-klustrets administratör såväl Ambari-administratör har denna behörighet som standard.

Mer information om hur du arbetar med behörigheter med domänanslutna HDInsight finns i [hantera HDInsight-kluster med Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Åtkomstkontroll

Om du inte är administratör / ägare av din Azure-prenumeration, måste kontot ha minst deltagaråtkomst till den resursgrupp som innehåller HDInsight-kluster.

Om du skapar ett HDInsight-kluster, person med minst deltagaråtkomst till Azure-prenumerationen måste redan har registrerat providern för HDInsight. Leverantörregistrering sker när en användare med deltagaråtkomst skapar en resurs för första gången på en prenumeration. Det kan också göras utan att skapa en resurs om du [registrera en provider med hjälp av REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Få mer information om arbete med åtkomsthantering:

* [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
* [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Förstå skriptåtgärder

En skriptåtgärder är Bash-skript som körs på noderna i ett HDInsight-kluster. Egenskaper och funktioner i skriptåtgärder är följande:

* Måste vara lagrade på en URI som kan nås från HDInsight-kluster. Följande är möjliga storage-platser:

    * Ett Azure Data Lake Storage-konto som är tillgänglig för HDInsight-klustret. Information om hur du använder Azure Data Lake Storage med HDInsight finns i [snabbstarten: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

        URI-format för skript som lagras i Data Lake Storage är `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]  
        > Tjänstens huvudnamn HDInsight använder för att få åtkomst till Data Lake Storage måste ha läsbehörighet till skriptet.

    * En blob i ett Azure Storage-konto som antingen primära eller ytterligare lagringskontot för HDInsight-klustret. HDInsight beviljas åtkomst till båda typerna av lagringskonton när klustret skapas.

    * En offentlig tjänst fildelning. Exempel är Azure Blob, GitHub, OneDrive och Dropbox.

        Till exempel URI: er, se [exempelskript skriptet åtgärd](#example-script-action-scripts).

        > [!WARNING]  
        > HDInsight stöder endast Blob i Azure Storage-konton med en standard-prestandanivån. 

* Kan begränsas för att köras på vissa nodtyper. Exempel är huvud- eller arbetsnoder.

* Kan vara beständiga eller ad hoc.

    Bestående skript används för att anpassa nya arbetsnoderna läggas till i klustret via skalningsåtgärder. Ett bestående skript kan också tillämpa ändringar till en annan nodtyp när skalningsåtgärder uppstår. Ett exempel är en huvudnod.

  > [!IMPORTANT]  
  > Beständiga skriptåtgärder måste ha ett unikt namn.

    Ad hoc-skript är inte beständiga. De tillämpas inte till arbetsnoder som lagts till i klustret när skriptet har körts. Du kan sedan uppgradera ett ad hoc-skript i ett bestående skript eller nedgradera ett bestående skript till en ad hoc-skript.

  > [!IMPORTANT]  
  > Skriptåtgärder användas när klustret skapas sparas automatiskt.
  >
  > Skript som inte är inte beständiga, även om du uttryckligen har angett att de ska vara.

* Kan acceptera parametrar som ska användas av skriptet vid körningen.

* Kör med privilegier på rotnivå på klusternoderna.

* Kan användas via Azure portal, Azure PowerShell, Azure klassiska CLI eller HDInsight .NET SDK.

Klustret sparar en historik över alla skript som har körts. Historiken hjälper när du behöver att hitta ID för ett skript för befordran eller degraderingen åtgärder.

> [!IMPORTANT]  
> Det finns inget automatiskt sätt att återställa de ändringar som gjorts av en skriptåtgärd. Manuellt ångra ändringarna eller lägga till ett skript som gör dem.

### <a name="script-action-in-the-cluster-creation-process"></a>Skriptåtgärd i klustret skapas

Skriptåtgärder användas när klustret skapas skiljer sig något från skriptåtgärder som körs på ett befintligt kluster:

* Skriptet sparas automatiskt.

* Ett fel i skriptet kan orsaka klusterskapningsprocessen misslyckas.

Följande diagram illustrerar när skriptåtgärd körs när du skapar:

![HDInsight-kluster anpassning och steg när klustret skapas][img-hdi-cluster-states]

Skriptet körs medan HDInsight konfigureras. Skriptet körs parallellt på alla angivna noder i klustret. Den körs med rotprivilegier på noderna.

> [!NOTE]  
> Du kan utföra åtgärder som att stoppa och starta tjänster, inklusive Apache Hadoop-relaterade tjänster. Om du stoppa tjänsterna, se till att tjänsten Ambari och andra Hadoop-relaterade tjänster körs innan skriptet är färdigt. Dessa tjänster används för att avgöra har hälsotillstånd och status för klustret medan det skapas.


När du skapar klustret kan du använda många skriptåtgärder på samma gång. Dessa skript anropas i den ordning som de har angetts.

> [!IMPORTANT]  
> Skriptåtgärder måste slutförs inom 60 minuter, eller timeout. Under klusteretablering, körs skriptet samtidigt med andra processer för installation och konfiguration. Konkurrens om resurser, till exempel CPU-tid eller nätverk bandbredd kan orsaka skriptet tar längre tid att slutföra än i din utvecklingsmiljö.
>
> Undvik att aktiviteter som att ladda ned och kompilera program från källan för att minimera den tid det tar för att köra skriptet. Förkompilera program och lagra den binära filen i Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Skriptåtgärd på ett kluster som körs

Ett fel i ett skript som körs på ett pågående kluster att inte automatiskt klustret för att ändra till ett felaktigt tillstånd. När ett skript är klar bör klustret återgå till körläge.

> [!IMPORTANT]  
> Även om klustret har ett fungerande tillstånd, har misslyckade skriptet brutit saker. Till exempel ett skript kan ta bort filer som behövs i klustret.
>
> Åtgärder som skript körs med rotprivilegier. Se till att du förstår vad en skriptet gör innan du tillämpar den till ditt kluster.

När du använder ett skript för ett kluster, kluster-status ändras från **kör** till **godkända**. Sedan ändras till **HDInsight configuration** och slutligen tillbaka till **kör** för lyckad skript. Skriptstatus för är inloggad i historiken för skriptåtgärder. Den här informationen visar om skriptet har lyckats eller misslyckats. Till exempel den `Get-AzureRmHDInsightScriptActionHistory` PowerShell-cmdlet visar status för ett skript. Den returnerar information liknande följande text:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Om du ändrar klusteranvändaren, admin, lösenord när klustret har skapats, misslyckas skriptåtgärder som körs mot det här klustret. Om du har några beständiga skriptåtgärder arbetsnoder som mål, kan skripten misslyckas när du skalar klustret.

## <a name="example-script-action-scripts"></a>Exempelskript för skript-åtgärd

Skript för skripta åtgärd kan användas via följande verktyg:

* Azure Portal
* Azure PowerShell
* Den klassiska Azure CLI
* En HDInsight .NET SDK

HDInsight tillhandahåller skript för att installera följande komponenter i HDInsight-kluster:

| Namn | Skript |
| --- | --- |
| Lägg till ett Azure Storage-konto |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Se [lägga till ytterligare lagringskonton till HDInsight](hdinsight-hadoop-add-storage.md). |
| Installera Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Se [installera och använda Hue på HDInsight Hadoop-kluster](hdinsight-hadoop-hue-linux.md). |
| Installera Presto |`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`. Se [installera och använda Presto på Hadoop-baserade HDInsight-kluster](hdinsight-hadoop-install-presto.md). |
| Installera Solr |`https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh`. Se [installerar och använder Apache Solr på HDInsight Hadoop-kluster](hdinsight-hadoop-solr-install-linux.md). |
| Installera Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Se [installera Apache Giraph på HDInsight Hadoop-kluster](hdinsight-hadoop-giraph-install-linux.md). |
| Förhandsladda Hive-bibliotek |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Se [lägga till anpassade Apache Hive-bibliotek när du skapar HDInsight-kluster](hdinsight-hadoop-add-hive-libraries.md). |
| Installera eller uppdatera Mono | `https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash`. Se [installera eller uppdatera Mono på HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Använda en skriptåtgärd när klustret skapas

Det här avsnittet beskrivs olika sätt du kan använda skriptåtgärder när du skapar ett HDInsight-kluster.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Använda en skriptåtgärd när klustret skapas från Azure portal

1. Börja att skapa ett kluster, enligt beskrivningen i [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md). När du skapar klustret som du kommer till en __klustersammanfattning__ sidan. Från den __klustersammanfattning__ väljer den __redigera__ länka för __avancerade inställningar__.

    ![Länken avancerade inställningar](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Från den __avancerade inställningar__ väljer __skriptåtgärder__. Från den __skriptåtgärder__ väljer __+ Skicka ny__.

    ![Skicka en ny skriptåtgärd](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Använd den __Välj ett skript__ post att välja en fördefinierad skript. Om du vill använda ett anpassat skript, Välj __anpassade__. Ange sedan den __namn__ och __Bash-skript-URI: N__ för skriptet.

    ![Lägga till ett skript i formuläret väljer skriptet](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    I följande tabell beskriver elementen i formuläret:

    | Egenskap  | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ett eget skript, Välj __anpassad__. Annars väljer du något av skript som tillhandahålls. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash-skript-URI |Ange URI för skriptet. |
    | HEAD/Worker/Zookeeper |Ange de noder som skriptet körs: **HEAD**, **Worker**, eller **ZooKeeper**. |
    | Parametrar |Ange parametrar, om det krävs av skriptet. |

    Använd den __spara den här skriptåtgärden__ posten för att se till att skriptet tillämpas under skalningsåtgärder.

5. Välj __skapa__ att spara skriptet. Du kan använda __+ skicka nya__ att lägga till ett annat skript.

    ![Flera skriptåtgärder](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    När du är klar att lägga till skript väljer du den __Välj__ knappen och sedan den __nästa__ vill gå tillbaka till den __klustersammanfattning__ avsnittet.

3. Om du vill skapa klustret, Välj __skapa__ från den __klustersammanfattning__ val.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Använda en skriptåtgärd från Azure Resource Manager-mallar

Skriptåtgärder kan användas med Azure Resource Manager-mallar. Ett exempel finns i [skapa HDInsight Linux-kluster och kör en skriptåtgärd](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

I det här exemplet skriptåtgärd har lagts till med hjälp av följande kod:

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

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Använda en skriptåtgärd när klustret skapas från Azure PowerShell

I det här avsnittet ska du använda den [Lägg till AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction) cmdlet för att anropa-skript för att anpassa ett kluster. Innan du börjar bör du kontrollera att du installerar och konfigurerar Azure PowerShell. Information om hur du konfigurerar en arbetsstation för att köra HDInsight PowerShell cmdlets finns i [översikt av Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0#run-or-install).

Följande skript visar hur du använder en skriptåtgärd när du skapar ett kluster med hjälp av PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Det kan ta flera minuter innan klustret har skapats.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Använda en skriptåtgärd när klustret skapas från HDInsight .NET SDK

HDInsight .NET SDK innehåller klientbibliotek som gör det enklare att arbeta med HDInsight från en .NET-program. Finns ett kodexempel i [skapa Linux-baserade kluster i HDInsight med hjälp av .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Använda en skriptåtgärd till ett kluster som körs

Det här avsnittet beskrivs hur du använder skriptåtgärder för ett aktivt kluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Använda en skriptåtgärd till ett kluster som körs i Azure Portal

Gå till den [Azure-portalen](https://portal.azure.com):

1. I den vänstra menyn, Välj **alla tjänster**.

1. Under **ANALYTICS**väljer **HDInsight-kluster**.

1. Välj ditt kluster från listan, vilket öppnar standardvyn.

1. Från standardvyn under **inställningar**väljer **skriptåtgärder**.

1. Högst upp på den **skriptåtgärder** väljer **+ Skicka ny**.

    ![Lägga till ett skript i ett kluster som körs](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Använd den __Välj ett skript__ post att välja en fördefinierad skript. Om du vill använda ett anpassat skript, Välj __anpassade__. Ange sedan den __namn__ och __Bash-skript-URI: N__ för skriptet.

    ![Lägga till ett skript i formuläret väljer skriptet](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    I följande tabell beskriver elementen i formuläret:

    | Egenskap  | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ett eget skript, Välj __anpassade__. Annars väljer du en medföljande skriptet. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash-skript-URI |Ange URI för skriptet. |
    | HEAD/Worker/Zookeeper |Ange de noder som skriptet körs: **HEAD**, **Worker**, eller **ZooKeeper**. |
    | Parametrar |Ange parametrar, om det krävs av skriptet. |

    Använd den __spara den här skriptåtgärden__ posten för att kontrollera att skriptet används under skalningsåtgärder.

5. Välj slutligen den **skapa** för att tillämpa skriptet till klustret.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Använda en skriptåtgärd för ett kluster som körs från Azure PowerShell

Innan du börjar bör du kontrollera att du installerar och konfigurerar Azure PowerShell. Information om hur du konfigurerar en arbetsstation för att köra HDInsight PowerShell cmdlets finns i [översikt av Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0#run-or-install).

I följande exempel visas hur du använder en skriptåtgärd för ett kluster som körs:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

När åtgärden har slutförts visas information liknande följande text:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Använda en skriptåtgärd för ett kluster som körs från Azure CLI

Innan du börjar bör du kontrollera att du installerar och konfigurerar Azure CLI. Mer information finns i [installera Azure CLI för klassiska](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Växla till läget Azure Resource Manager:

    ```bash
    azure config mode arm
    ```

2. Autentisera till din Azure-prenumeration:

    ```bash
    azure login
    ```

3. Gäller en skriptåtgärd för ett kluster som körs:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Om du utelämnar parametrarna för det här kommandot uppmanas du för dessa. Om skriptet som du anger med `-u` accepterar parametrar, du kan ange dem med hjälp av den `-p` parametern.

    Giltigt nodtyperna `headnode`, `workernode`, och `zookeeper`. Om skriptet ska tillämpas på flera olika typer av noden, ange vilka typer som är avgränsade med semikolon `;`. Till exempel `-n headnode;workernode`.

    Lägg till för att spara skriptet `--persistOnSuccess`. Du kan också spara skriptet senare med hjälp av `azure hdinsight script-action persisted set`.

    När jobbet har slutförts kan du få utdata som liknar följande text:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Använda en skriptåtgärd till ett kluster som körs med hjälp av REST API

Se [REST-API i Azure HDInsight-kluster](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Använda en skriptåtgärd för ett kluster som körs från HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att tillämpa skript till ett kluster finns i [gäller en skriptåtgärd mot ett körs Linux-baserade HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Visa historik och höja eller sänka nivån skriptåtgärder

### <a name="the-azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn, Välj **alla tjänster**.

1. Under **ANALYTICS**väljer **HDInsight-kluster**.

1. Välj ditt kluster från listan, vilket öppnar standardvyn.

1. Från standardvyn under **inställningar**väljer **skriptåtgärder**.

4. En historik över skript för det här klustret visas i avsnittet skriptet åtgärder. Denna information innehåller en lista över bestående skript. Följande skärmbild visar att Solr skriptet har körts i det här klustret. Skärmbilden visar inte några bestående skript.

    ![Skriptåtgärder](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Välj ett skript från historiken över att visa den **egenskaper** avsnittet för det här skriptet. Du kan köra skriptet igen eller uppgradera den högst upp på skärmen.

    ![Skriptåtgärder, egenskaper](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Du kan också välja de tre punkterna **...** , till höger om poster i avsnittet skriptet åtgärder att utföra åtgärder.

    ![Skriptåtgärder, ellipsen](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funktion |
| --- | --- |
| `Get-AzureRmHDInsightPersistedScriptAction` |Hämta information om beständiga skriptåtgärder. |
| `Get-AzureRmHDInsightScriptActionHistory` |Hämta en historik över skriptåtgärder som tillämpas på det kluster eller information om ett visst skript. |
| `Set-AzureRmHDInsightPersistedScriptAction` |Flytta upp en ad hoc-skriptåtgärd till en bestående skriptåtgärd. |
| `Remove-AzureRmHDInsightPersistedScriptAction` |Degradera en bestående skriptåtgärd till en ad hoc-åtgärd. |

> [!IMPORTANT]  
> `Remove-AzureRmHDInsightPersistedScriptAction` inte ångra åtgärder som utförs av ett skript. Denna cmdlet tar endast bort flaggan beständiga.

Följande exempelskript visar hur du använder cmdlets kan marknadsföra och flytta ned ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Den klassiska Azure CLI

| cmdlet | Funktion |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Hämta en lista över beständiga skriptåtgärder. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Hämta information om en specifik bestående skriptåtgärd. |
| `azure hdinsight script-action history list <clustername>` |Hämta en historik över skriptåtgärder som tillämpas på klustret. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Hämta information om en specifik skriptåtgärd. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Flytta upp en ad hoc-skriptåtgärd till en bestående skriptåtgärd. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Degradera en bestående skriptåtgärd till en ad hoc-åtgärd. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` inte ångra åtgärder som utförs av ett skript. Denna cmdlet tar endast bort flaggan beständiga.

### <a name="the-hdinsight-net-sdk"></a>HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att hämta skript historik från ett kluster, uppgradera eller nedgradera skript kan du läsa [ gäller en skriptåtgärd mot ett körs Linux-baserade HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Det här exemplet visar också hur du installerar ett HDInsight-program med hjälp av .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för öppen källkod-programvara som används i HDInsight-kluster

Microsoft Azure HDInsight-tjänsten använder ett ekosystem med öppen källkod-tekniker som skapats på rätt sätt runt Apache Hadoop. Microsoft Azure tillhandahåller en allmän supportnivå för tekniker med öppen källkod. Mer information finns i den **supportens omfattning** delen av [Azure stöd för vanliga frågor och svar](https://azure.microsoft.com/support/faq/). Tjänsten HDInsight ger en extra nivå av stöd för inbyggda komponenterna.

Det finns två typer av komponenter med öppen källkod i HDInsight-tjänsten:

* **Inbyggda komponenterna**. Dessa komponenter är förinstallerade på HDInsight-kluster och tillhandahåller huvudfunktionerna i klustret. Följande komponenter som hör till den här kategorin:

    * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
    * Hive-frågespråket [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
    * [Apache Mahout](https://mahout.apache.org/). 
    
    En fullständig lista över komponenter i serverkluster finns i [vad är Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](hdinsight-component-versioning.md)

* **Anpassade komponenter**. Du kan installera eller använda i din arbetsbelastning någon komponent som är tillgänglig i diskussionsgruppen eller skapats av dig som en användare i klustret.

> [!WARNING]  
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support hjälpa dig att felsöka problemet ytterligare. Microsoft Support kanske kan lösa problemet. Eller de kan be dig att engagera tillgängliga kanaler för öppen källkod-teknik där djup kompetens för den tekniken hittas. Du kan använda många community-webbplatser. Exempel är [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) och [Stack Overflow](https://stackoverflow.com). 
>
> Apache-projekt har också project-webbplatser på den [Apache webbplats](https://apache.org). Ett exempel är [Hadoop](https://hadoop.apache.org/).

Tjänsten HDInsight finns flera sätt att använda anpassade komponenter. Samma nivå av support gäller oavsett hur en komponent som används eller installeras i klustret. I följande lista beskrivs de vanligaste sätt att anpassade komponenter används för HDInsight-kluster:

1. **Jobb skickas**. Hadoop- eller andra typer av jobb som kör eller använda anpassade komponenter kan skickas till klustret.

2. **Klusteranpassning**. Du kan ange ytterligare inställningar och anpassade komponenter som installeras på noderna i klustret när klustret skapas.

3. **Exempel**. För populära anpassade komponenter, kan Microsoft och andra ger exempel på hur du kan använda dessa komponenter på HDInsight-kluster. De här exemplen tillhandahålls utan stöd.

## <a name="troubleshooting"></a>Felsökning

Du kan använda Ambari-webbgränssnittet för att visa information som loggas av skriptåtgärder. Om skriptet misslyckas när klustret skapas finns också loggarna standardkontot för lagring som är associerade med klustret. Det här avsnittet innehåller information om hur du hämtar loggarna genom att använda båda alternativen.

### <a name="the-apache-ambari-web-ui"></a>Apache Ambari-webbgränssnittet

1. I webbläsaren går du till https://CLUSTERNAME.azurehdinsight.net. Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.

    När du uppmanas, anger du namnet på admin-kontot **admin**, och lösenordet för klustret. Du kan behöva ange administratörsautentiseringsuppgifter i ett webbformulär.

2. I fältet högst upp på sidan väljer du den **ops** posten. En lista visar aktuella och tidigare åtgärder i klustret via Ambari.

    ![Ambari web UI-fältet med åtgärder som har valts](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Hitta de poster som har **kör\_customscriptaction** i den **Operations** kolumn. Dessa poster skapas när åtgärderna som skriptet körs.

    ![Skärmbild av åtgärder](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Visa den **STDOUT** och **STDERR** utdata, väljer den **run\customscriptaction** posten och bryter ned via länkarna. Dessa utdata genereras när skriptet körs och kan ha användbar information.

### <a name="access-logs-from-the-default-storage-account"></a>Åtkomst till loggar från standardkontot för lagring

Om det går inte att skapa kluster på grund av ett skriptfel, behålls loggarna i klustret storage-konto.

* Storage-loggar finns på `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Skärmbild av åtgärder](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Under den här katalogen loggarna är ordnade separat för **huvudnoden**, **arbetsnod**, och **zookeeper-nod**. Se följande exempel:

    * **Huvudnoden**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Arbetsnod**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper-nod**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Alla **stdout** och **stderr** för motsvarande värden har överförts till lagringskontot. Det finns en sådan **utdata -\*.txt** och **fel -\*.txt** för respektive skriptåtgärd. Den **utdata *.txt** filen innehåller information om URI: N för det skript som kördes på värden. Följande text är ett exempel på den här informationen:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Det är möjligt att du skapar ett kluster för skriptet åtgärd flera gånger med samma namn. I så fall kan du skilja relevanta loggarna baserat på den **datum** mappnamn. Till exempel mappstrukturen för ett kluster, **mycluster**nyskapade vid olika tidpunkter ser ut som följande loggposter:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Om du skapar ett kluster för skript-åtgärd med samma namn på samma dag, kan du använda prefixet unikt identifiera relevanta loggfiler.

* Om du skapar ett kluster nära 12:00 AM, midnatt, är det möjligt att loggfilerna span över två dagar. I så fall kan se du två olika datum mappar för samma kluster.

* Ladda upp loggfiler till standardbehållaren kan det ta upp till fem minuter, särskilt för stora kluster. Så om du vill komma åt loggarna du bör inte omedelbart ta bort klustret om en skriptåtgärd misslyckas.

### <a name="ambari-watchdog"></a>Ambari watchdog

> [!WARNING]  
> Ändra inte lösenordet för Ambari watchdog, hdinsightwatchdog på Linux-baserade HDInsight-klustret. Ändra lösenordet för det här kontot delar möjlighet att köra nya skriptåtgärder på HDInsight-kluster.

### <a name="cant-import-name-blobservice"></a>Det går inte att importera namn BlobService

__Symptom__. Det går inte att skriptåtgärd. Text som liknar följande fel visas när du visar igen i Ambari:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Orsak__. Det här felet uppstår om du uppgraderar Python Azure Storage-klienten som ingår i HDInsight-klustret. HDInsight förväntar sig att Azure Storage-klienten 0.20.0.

__Lösning__. För att lösa det här felet kan ansluta manuellt till varje nod i klustret med hjälp av `ssh`. Kör följande kommando för att installera om rätt storage klientversion:

```bash
sudo pip install azure-storage==0.20.0
```

Information om hur du ansluter till klustret med SSH finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Historik visar inte skripten som används när klustret skapas

Om klustret har skapats innan den 15 mars 2016 kanske du inte ser en post i historiken för skriptåtgärder. Ändra storlek på klustret gör att skripten ska visas i historiken för skriptåtgärder.

Det finns två undantag:

* Klustret har skapats före den 1 September 2015. Det här datumet är när skriptåtgärder introducerades. Alla kluster som skapats före detta datum kunde inte har använt skriptåtgärder för att skapa kluster.

* Du använde flera skriptåtgärder när klustret skapas. Eller samma namn för flera skript eller samma namn, samma URI, men olika parametrar som används för flera skript. I dessa fall kan få du följande fel:

    Inga nya skriptåtgärder kan inte köras på det här klustret på grund av motstridiga skriptet namnen i befintliga skript. Skriptnamn har angetts när klustret skapas måste vara alla unika. Befintliga skript körs vid storleksändring.

## <a name="next-steps"></a>Nästa steg

* [Utveckla script åtgärd-skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installera och använda Apache Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md)
* [Installera och använda Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md)
* [Lägga till ytterligare lagringsutrymme till ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Steg när klustret skapas"
