---
title: Anpassa HDInsight-kluster med skriptåtgärder - Azure
description: Lägga till anpassade komponenter i Linux-baserade HDInsight-kluster med skriptåtgärder. Skriptåtgärder är Bash-skript som kan användas för att anpassa klusterkonfigurationen eller lägga till ytterligare tjänster och verktyg som Hue, Solr eller R.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5ef7ddc068fea7703dad67b80b96c292bfd26943
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870715"
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder

HDInsight är en konfigurationsmetod som kallas **skriptåtgärder** som anropar anpassade skript för att anpassa klustret. Dessa skript används för att installera ytterligare komponenter och ändra konfigurationsinställningarna. Skriptåtgärder kan användas under eller när klustret har skapats.

> [!IMPORTANT]
> Möjligheten att använda skriptåtgärder på ett pågående kluster är endast tillgängligt för Linux-baserade HDInsight-kluster.
>
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Skriptåtgärder kan även publiceras på Azure Marketplace som ett HDInsight-program. Mer information om HDInsight-program finns i [publicera HDInsight-program på Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Behörigheter

Om du använder ett domänanslutet HDInsight-kluster, finns det två Ambari-behörigheter som krävs när du använder skriptåtgärder med klustret:

* **AMBARI. KÖR\_anpassade\_kommandot**: The Ambari administratörsroll har denna behörighet som standard.
* **KLUSTER. KÖR\_anpassade\_kommandot**: båda HDInsight Klusteradministratören och Ambari-administratör har denna behörighet som standard.

Mer information om hur du arbetar med behörigheter med domänanslutna HDInsight finns i [hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Åtkomstkontroll

Om du inte är administratör/ägare av din Azure-prenumeration, måste kontot ha minst **deltagare** åtkomst till den resursgrupp som innehåller HDInsight-kluster.

Även om du skapar ett HDInsight-kluster, person med minst **deltagare** åtkomst till Azure-prenumerationen måste redan har registrerat providern för HDInsight. Leverantörregistrering sker när en användare med deltagaråtkomst skapar en resurs för första gången på en prenumeration. Detta kan också inträffa utan att en resurs skapas, om en [leverantör registreras med REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Se följande dokument för mer information om arbete med åtkomst hantering:

* [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
* [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)

## <a name="understanding-script-actions"></a>Förstå skriptåtgärder

En skriptåtgärder är Bash-skript som körs på noderna i ett HDInsight-kluster. Här följer egenskaper och funktioner i skriptåtgärder.

* Måste vara lagrade på en URI som kan nås från HDInsight-kluster. Följande är möjliga storage-platser:

    * En **Azure Data Lake Store** konto som kan nås av HDInsight-klustret. Information om hur du använder Azure Data Lake Store med HDInsight finns i [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

        När du använder ett skript som lagras i Data Lake Store, URI-format är `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Tjänstens huvudnamn HDInsight använder för att få åtkomst till Data Lake Store måste ha läsbehörighet till skriptet.

    * En blob i ett **Azure Storage-konto** som är antingen primära eller ytterligare lagringskontot för HDInsight-klustret. HDInsight beviljas åtkomst till båda typerna av lagringskonton när klustret skapas.

    * En offentlig fildelning tjänst, till exempel Azure Blob, GitHub, OneDrive, Dropbox, osv.

        Till exempel URI: er, finns i den [exempelskript skriptet åtgärd](#example-script-action-scripts) avsnittet.

        > [!WARNING]
        > HDInsight stöder endast Blob i Azure Storage-konton med standard-prestandanivån. 

* Kan vara begränsad till **köras på vissa nodtyper**för exempel huvud- eller arbetsnoder.

* Kan vara **beständiga** eller **ad hoc-**.

    **Beständiga** skript används för att anpassa nya arbetsnoderna läggas till i klustret via skalningsåtgärder. Ett bestående skript kan gäller ändringar till en annan nodtyp, till exempel en huvudnod, även när skalningsåtgärder uppstår.

  > [!IMPORTANT]
  > Beständiga skriptåtgärder måste ha ett unikt namn.

    **Ad hoc-** skript har inte sparats. De tillämpas inte på arbetsnoder som lagts till i klustret när skriptet har körts. Du kan sedan uppgradera ett ad hoc-skript i ett bestående skript eller nedgradera ett bestående skript till en ad hoc-skript.

  > [!IMPORTANT]
  > Skriptåtgärder användas när klustret skapas sparas automatiskt.
  >
  > Skript som misslyckas inte är beständiga, även om du uttryckligen har angett att de ska vara.

* Kan acceptera **parametrar** som används av skriptet vid körningen.

* Kör med **rätt behörighet för rotmappen** på klusternoderna.

* Kan användas via den **Azure-portalen**, **Azure PowerShell**, **klassiska Azure-CLI**, eller **HDInsight .NET SDK**

Klustret sparar en historik över alla skript som har varit kördes. Historiken är användbart när du behöver att hitta ID för ett skript för befordran eller degraderingen åtgärder.

> [!IMPORTANT]
> Det finns inget automatiskt sätt att återställa de ändringar som gjorts av en skriptåtgärd. Manuellt ångra ändringarna eller lägga till ett skript som gör dem.

### <a name="script-action-in-the-cluster-creation-process"></a>Skriptåtgärd i klustret skapas

Skriptåtgärder användas när klustret skapas kan variera från skript som körts på ett befintligt kluster:

* Skriptet är **automatiskt beständiga**.

* En **fel** i skriptet kan orsaka klusterskapningsprocessen misslyckas.

Följande diagram illustrerar när skriptåtgärd körs när du skapar:

![HDInsight-kluster anpassning och steg när klustret skapas][img-hdi-cluster-states]

Skriptet körs medan HDInsight konfigureras. Skriptet körs parallellt på alla angivna noder i klustret och körs med rotprivilegier på noderna.

> [!NOTE]
> Du kan utföra åtgärder som att stoppa och starta tjänster, inklusive Apache Hadoop-relaterade tjänster. Om du stoppar tjänster måste du kontrollera att tjänsten Ambari och andra Hadoop-relaterade tjänster som körs innan skriptet har slutförts. Dessa tjänster används för att avgöra har hälsotillstånd och status för klustret medan det skapas.


När du skapar klustret kan du använda flera skriptåtgärder på samma gång. Dessa skript anropas i den ordning som de har angetts.

> [!IMPORTANT]
> Skriptåtgärder måste slutföras inom 60 minuter eller tidsgräns. Under klusteretablering, körs skriptet samtidigt med andra processer för installation och konfiguration. Konkurrens om resurser, till exempel CPU-tid eller nätverk bandbredd kan orsaka skriptet tar längre tid att slutföra än i din utvecklingsmiljö.
>
> Undvik åtgärder som att ladda ned och kompilera program från källa för att minimera den tid det tar för att köra skriptet. Före kompilera program och lagra den binära filen i Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Skriptåtgärd på ett kluster som körs

Ett fel i ett skript som kördes på en redan körs klustret inte automatiskt att klustret för att ändra till ett felaktigt tillstånd. När en skriptet har körts returnera klustret i tillståndet ”körs”.

> [!IMPORTANT]
> Även om klustret har tillståndet ”körs”, har misslyckade skriptet brutit saker. Till exempel ett skript kan ta bort filer som behövs i klustret.
>
> Åtgärder som skript körs med rotprivilegier. Se till att du förstår vad vi gör ett skript innan den tillämpas på ditt kluster.

När du använder ett skript till ett kluster, kluster-status ändras från **kör** till **godkända**, sedan **HDInsight configuration**, och slutligen tillbaka till  **Kör** för lyckad skript. Skriptstatus för är inloggad i historiken för skriptåtgärder och du kan använda den här informationen för att avgöra om skriptet lyckades eller misslyckades. Till exempel den `Get-AzureRmHDInsightScriptActionHistory` PowerShell-cmdlet kan användas för att visa status för ett skript. Den returnerar information liknande följande text:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]
> Om du har ändrat det kluster-användarlösenordet (admin) när klustret har skapats kan misslyckas skriptet har körts mot det här klustret. Om du har några beständiga skriptåtgärder arbetsnoder som mål, misslyckas skripten när du skalar klustret.

## <a name="example-script-action-scripts"></a>Exempelskript för skript-åtgärd

Skript för skripta åtgärd kan användas via följande verktyg:

* Azure Portal
* Azure PowerShell
* Klassisk Azure CLI
* HDInsight .NET SDK

HDInsight tillhandahåller skript för att installera följande komponenter i HDInsight-kluster:

| Namn | Skript |
| --- | --- |
| **Lägg till ett Azure Storage-konto** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Se [lägga till ytterligare lagringsutrymme till ett HDInsight-kluster](hdinsight-hadoop-add-storage.md). |
| **Installera Hue** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Se [installera och använda Hue i HDInsight-kluster](hdinsight-hadoop-hue-linux.md). |
| **Installera Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Se [installera och använda Presto på HDInsight-kluster](hdinsight-hadoop-install-presto.md). |
| **Installera Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Se [installera och använda Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md). |
| **Installera Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Se [installera och använda Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). |
| **Förhandsladda Hive-bibliotek** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Se [lägga till Hive-bibliotek på HDInsight-kluster](hdinsight-hadoop-add-hive-libraries.md). |
| **Installera eller uppdatera Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Se [installera eller uppdatera Mono på HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Använda en skriptåtgärd när klustret skapas

Det här avsnittet innehåller exempel på olika sätt du kan använda skriptåtgärder när du skapar ett HDInsight-kluster.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Använda en skriptåtgärd när klustret skapas från Azure portal

1. Börja skapa ett kluster, enligt beskrivningen i [skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md). När du skapar klustret du anländer till en __klustersammanfattning__ sidan. Från den __klustersammanfattning__ väljer den __redigera__ länka för __avancerade inställningar__.

    ![Länken avancerade inställningar](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Från den __avancerade inställningar__ väljer __skriptåtgärder__. Från den __skriptåtgärder__ väljer __+ Skicka ny__

    ![Skicka en ny skriptåtgärd](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Använd den __Välj ett skript__ post att välja en fördefinierad skript. Om du vill använda ett anpassat skript, Välj __anpassade__ och ange sedan den __namn__ och __Bash-skript-URI: N__ för skriptet.

    ![Lägga till ett skript i formuläret väljer skriptet](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    I följande tabell beskriver elementen i formuläret:

    | Egenskap  | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ett eget skript, Välj __anpassad__. Annars väljer du något av skript som tillhandahålls. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash-skript-URI |Ange URI för skriptet. |
    | HEAD/Worker/Zookeeper |Ange noderna (**Head**, **Worker**, eller **ZooKeeper**) som skriptet körs. |
    | Parametrar |Ange parametrar, om det krävs av skriptet. |

    Använd den __spara den här skriptåtgärden__ posten för att säkerställa att skriptet tillämpas under skalningsåtgärder.

5. Välj __skapa__ att spara skriptet. Du kan sedan använda __+ skicka nya__ att lägga till ett annat skript.

    ![Flera skriptåtgärder](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    När du är klar att lägga till skript kan använda den __Välj__ knapp, och sedan den __nästa__ vill gå tillbaka till den __klustersammanfattning__ avsnittet.

3. Om du vill skapa klustret, Välj __skapa__ från den __klustersammanfattning__ val.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Använda en skriptåtgärd från Azure Resource Manager-mallar

Skriptåtgärder kan användas med Azure Resource Manager-mallar. Ett exempel finns i [ https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/ ](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

I det här exemplet läggs skriptåtgärd till med följande kod:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Information om hur du distribuerar en mall finns i följande dokument:

* [Distribuera resurser med mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Distribuera resurser med mallar och Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Använda en skriptåtgärd när klustret skapas från Azure PowerShell

I det här avsnittet ska du använda den [Lägg till AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction) cmdlet för att anropa-skript för att anpassa ett kluster. Innan du fortsätter, kontrollera att du har installerat och konfigurerat Azure PowerShell. Information om hur du konfigurerar en arbetsstation för att köra HDInsight PowerShell cmdlets finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Följande skript visar hur du använder en skriptåtgärd när du skapar ett kluster med hjälp av PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Det kan ta flera minuter innan klustret har skapats.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Använda en skriptåtgärd när klustret skapas från HDInsight .NET SDK

HDInsight .NET SDK innehåller klientbibliotek som gör det enklare att arbeta med HDInsight från en .NET-program. Finns ett kodexempel i [skapa Linux-baserade kluster i HDInsight med .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Använda en skriptåtgärd till ett kluster som körs

I det här avsnittet lär du dig hur du använder skriptåtgärder för ett aktivt kluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Använda en skriptåtgärd till ett kluster som körs i Azure Portal

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt HDInsight-kluster.

2. Översikt för HDInsight-kluster, Välj den **skriptåtgärder** panelen.

    ![Skriptet åtgärder panel](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Du kan också välja **alla inställningar** och välj sedan **skriptåtgärder** från avsnittet Inställningar.

3. Högst upp på avsnittet skriptet åtgärder, Välj **Skicka ny**.

    ![Lägga till ett skript i ett kluster som körs](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Använd den __Välj ett skript__ post att välja en fördefinierad skript. Om du vill använda ett anpassat skript, Välj __anpassade__ och ange sedan den __namn__ och __Bash-skript-URI: N__ för skriptet.

    ![Lägga till ett skript i formuläret väljer skriptet](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    I följande tabell beskriver elementen i formuläret:

    | Egenskap  | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ett eget skript, Välj __anpassade__. Annars väljer du en medföljande skriptet. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash-skript-URI |Ange URI för skriptet. |
    | HEAD/Worker/Zookeeper |Ange noderna (**Head**, **Worker**, eller **ZooKeeper**) som skriptet körs. |
    | Parametrar |Ange parametrar, om det krävs av skriptet. |

    Använd den __spara den här skriptåtgärden__ posten för att kontrollera att skriptet används under skalningsåtgärder.

5. Använd slutligen den **skapa** för att tillämpa skriptet till klustret.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Använda en skriptåtgärd för ett kluster som körs från Azure PowerShell

Innan du fortsätter, kontrollera att du har installerat och konfigurerat Azure PowerShell. Information om hur du konfigurerar en arbetsstation för att köra HDInsight PowerShell cmdlets finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

I följande exempel visar hur du använder en skriptåtgärd för ett kluster som körs:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

När åtgärden har slutförts får du information som liknar följande text:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Använda en skriptåtgärd för ett kluster som körs från Azure CLI

Innan du fortsätter, kontrollera att du har installerat och konfigurerat Azure CLI. Mer information finns i [installerar den klassiska Azure-CLI](../cli-install-nodejs.md).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Om du vill växla till läget Azure Resource Manager, använder du följande kommando på kommandoraden:

    ```bash
    azure config mode arm
    ```

2. Använd följande för att autentisera till din Azure-prenumeration.

    ```bash
    azure login
    ```

3. Använd följande kommando för att tillämpa en skriptåtgärd på ett kluster som körs

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Om du utelämnar parametrarna för det här kommandot uppmanas du för dessa. Om skriptet som du anger med `-u` accepterar parametrar, du kan ange dem med hjälp av den `-p` parametern.

    Giltigt nodtyperna `headnode`, `workernode`, och `zookeeper`. Om skriptet ska tillämpas på flera nodtyper, anger du de typer som är avgränsade med ett ””;. Till exempel `-n headnode;workernode`.

    För att spara skriptet, lägger du till den `--persistOnSuccess`. Du kan också spara skriptet senare med hjälp av `azure hdinsight script-action persisted set`.

    När jobbet är klart visas utdata som liknar följande text:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Använda en skriptåtgärd till ett aktivt kluster med hjälp av REST API

Se [kör skriptåtgärder på ett aktivt kluster](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Använda en skriptåtgärd för ett kluster som körs från HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att tillämpa skript till ett kluster finns i [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Visa historik, flytta upp och flytta ned skriptåtgärder

### <a name="using-the-azure-portal"></a>Använda Azure Portal

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt HDInsight-kluster.

2. Översikt för HDInsight-kluster, Välj den **skriptåtgärder** panelen.

    ![Skriptet åtgärder panel](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Du kan också välja **alla inställningar** och välj sedan **skriptåtgärder** från avsnittet Inställningar.

4. En historik över skript för det här klustret visas i avsnittet skriptet åtgärder. Denna information innehåller en lista över bestående skript. I skärmbilden nedan ser du att Solr skriptet har körts i det här klustret. Skärmbilden visar inte några bestående skript.

    ![Skriptet åtgärder avsnittet](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Om du väljer ett skript från historiken över visas avsnittet Egenskaper för det här skriptet. Du kan köra skriptet igen eller uppgradera den högst upp på skärmen.

    ![Åtgärder-skriptegenskaper](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Du kan också använda den **...**  till höger om poster i avsnittet skriptet åtgärder att utföra åtgärder.

    ![Skriptåtgärder... användning](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

| Använd följande... | Till... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Hämta information om beständiga skriptåtgärder |
| Get-AzureRmHDInsightScriptActionHistory |Hämta en historik över skriptåtgärder som tillämpas på det kluster eller information om ett visst skript |
| Set-AzureRmHDInsightPersistedScriptAction |Främjar en ad hoc-skriptåtgärd till en bestående skriptåtgärd |
| Remove-AzureRmHDInsightPersistedScriptAction |Flyttar ned ett bestående skriptåtgärd till en ad hoc-åtgärd |

> [!IMPORTANT]
> Med hjälp av `Remove-AzureRmHDInsightPersistedScriptAction` inte ångra åtgärder som utförs av ett skript. Denna cmdlet tar endast bort flaggan beständiga.

Följande exempelskript visar hur du använder cmdletarna att flytta upp och flytta ned ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-classic-cli"></a>Med den klassiska Azure CLI

| Använd följande... | Till... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Hämta en lista över beständiga skriptåtgärder |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Hämta information om en specifik bestående skriptåtgärd |
| `azure hdinsight script-action history list <clustername>` |Hämta en historik över skriptåtgärder som tillämpas på klustret |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Hämta information om en specifik skriptåtgärd |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Främjar en ad hoc-skriptåtgärd till en bestående skriptåtgärd |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Flyttar ned ett bestående skriptåtgärd till en ad hoc-åtgärd |

> [!IMPORTANT]
> Med hjälp av `azure hdinsight script-action persisted delete` inte ångra åtgärder som utförs av ett skript. Denna cmdlet tar endast bort flaggan beständiga.

### <a name="using-the-hdinsight-net-sdk"></a>Med HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att hämta skript historik från ett kluster, uppgradera eller nedgradera skript kan du läsa [ https://github.com/Azure-Samples/hdinsight-dotnet-script-action ](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Det här exemplet visar också hur du installerar ett HDInsight-program med .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för öppen källkod-programvara som används i HDInsight-kluster

Microsoft Azure HDInsight-tjänsten använder ett ekosystem med öppen källkod-tekniker som skapats på rätt sätt runt Apache Hadoop. Microsoft Azure tillhandahåller en allmän supportnivå för tekniker med öppen källkod. Mer information finns i den **supportens omfattning** delen av den [stöd för vanliga frågor och svar för Azure-webbplats](https://azure.microsoft.com/support/faq/). Tjänsten HDInsight ger en extra nivå av stöd för inbyggda komponenterna.

Det finns två typer av öppen källkod-komponenter som är tillgängliga i HDInsight-tjänsten:

* **Inbyggda komponenterna** -komponenterna är förinstallerade på HDInsight-kluster och tillhandahåller huvudfunktionerna i klustret. Till exempel [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager, Hive-frågespråket ([HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)), och [Apache Mahout](https://mahout.apache.org/) bibliotek som tillhör den här kategorin. En fullständig lista över komponenter i serverkluster finns i [Nyheter i Hadoop-klusterversionerna från HDInsight](hdinsight-component-versioning.md).
* **Anpassade komponenter** -du, som en användare i klustret, kan installera eller använda i din arbetsbelastning någon komponent som är tillgänglig i diskussionsgruppen eller skapats av dig.

> [!WARNING]
> Komponenter som tillhandahålls med HDInsight-kluster stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem relaterade till dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att felsöka problemet ytterligare. Microsoft-supporten kanske kan lösa problemet eller de kan be dig att engagera tillgängliga kanaler för tekniker med öppen källkod som där djup kompetens för den tekniken hittas. Det finns exempelvis många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Även Apache-projekt har project-webbplatser på [ http://apache.org ](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).

Tjänsten HDInsight finns flera sätt att använda anpassade komponenter. Samma nivå av support gäller oavsett hur en komponent som används eller installeras i klustret. I följande lista beskrivs de vanligaste sätt att anpassade komponenter kan användas på HDInsight-kluster:

1. Jobböverföring - Hadoop eller andra typer av jobb som kör eller använda anpassade komponenter kan skickas till klustret.

2. Anpassning av kluster - när du skapar klustret som du kan ange ytterligare inställningar och anpassade komponenter som installeras på noderna i klustret.

3. Exempel – för populära anpassade komponenter, Microsoft och andra kan ge exempel på hur du kan använda dessa komponenter på HDInsight-kluster. De här exemplen tillhandahålls utan stöd.

## <a name="troubleshooting"></a>Felsökning

Du kan använda Ambari-webbgränssnittet för att visa information som loggas av skriptåtgärder. Om skriptet misslyckas när klustret skapas finns också loggarna standardkontot för lagring som är associerade med klustret. Det här avsnittet innehåller information om hur du hämtar loggarna med båda alternativen.

### <a name="using-the-apache-ambari-web-ui"></a>Med Apache Ambari-webbgränssnittet

1. Öppna webbläsaren och navigera till https://CLUSTERNAME.azurehdinsight.net. Ersätt CLUSTERNAME med namnet på ditt HDInsight-kluster.

    När du uppmanas, anger du namnet på administratörskontot (admin) och lösenord för klustret. Du kan behöva ange administratörsautentiseringsuppgifter i ett webbformulär.

2. I fältet högst upp på sidan väljer du den **ops** posten. En lista över aktuella och tidigare åtgärder som utförs på klustret via Ambari visas.

    ![Ambari web UI-fältet med åtgärder som har valts](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Hitta de poster som har **kör\_customscriptaction** i den **Operations** kolumn. Dessa poster skapas när åtgärderna som skriptet körs.

    ![Skärmbild av åtgärder](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Om du vill visa STDOUT och STDERR-utdata, Välj posten run\customscriptaction och öka detaljnivån i länkarna. Dessa utdata genereras när skriptet har körts och kan innehålla användbar information.

### <a name="access-logs-from-the-default-storage-account"></a>Åtkomst till loggar från standardkontot för lagring

Om det går inte att skapa kluster på grund av ett skriptfel, behålls loggarna i klustret storage-konto.

* Storage-loggar finns på `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Skärmbild av åtgärder](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Under den här katalogen organiseras loggarna separat för huvudnoden, workernode och zookeeper-noder. Några exempel är:

    * **Huvudnod** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Arbetsnod** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper-nod** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Alla stdout och stderr för motsvarande värden har överförts till lagringskontot. Det finns en sådan **utdata -\*.txt** och **fel -\*.txt** för respektive skriptåtgärd. Utdata-*.txt-filen innehåller information om URI: N för skript som fick köras på värden. Följande text är ett exempel på den här informationen:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Det är möjligt att du skapar ett kluster för skriptet åtgärd flera gånger med samma namn. I detta fall är kan du skilja relevanta loggarna baserat på mappnamnet datum. Till exempel ser mappstrukturen för ett kluster (det här klustret) som skapades vid olika tidpunkter ut som följande loggposter:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Om du skapar ett kluster för skript-åtgärd med samma namn på samma dag, kan du använda prefixet unikt identifiera relevanta loggfiler.

* Om du skapar ett kluster nära 12:00:00 (midnatt), är det möjligt att loggfilerna span över två dagar. I sådana fall kan se du två olika datum mappar för samma kluster.

* Ladda upp loggfiler till standardbehållaren kan ta upp till 5 minuter, särskilt för stora kluster. Så om du vill komma åt loggarna du bör inte omedelbart ta bort klustret om en skriptåtgärd misslyckas.

### <a name="ambari-watchdog"></a>Ambari watchdog

> [!WARNING]
> Ändra inte lösenordet för Ambari-Watchdog (hdinsightwatchdog) på Linux-baserade HDInsight-klustret. Ändra lösenordet för det här kontot delar möjlighet att köra nya skriptåtgärder på HDInsight-kluster.

### <a name="cant-import-name-blobservice"></a>Det går inte att importera namn BlobService

__Symptom__: skript åtgärden misslyckas. När du visar igen i Ambari visas texten som liknar följande fel:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Orsak__: det här felet uppstår om du uppgraderar Python Azure Storage-klienten som medföljer HDInsight-klustret. HDInsight förväntar sig att Azure Storage-klienten 0.20.0.

__Lösning__: för att lösa det här felet kan ansluta manuellt till varje nod med `ssh` och Använd följande kommando för att installera om rätt storage klientversion:

```bash
sudo pip install azure-storage==0.20.0
```

Information om hur du ansluter till klustret med SSH finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Historik visar inte skript som används när klustret skapas

Om klustret har skapats innan den 15 mars 2016 kan du inte se en post i historiken för skriptåtgärder. Ändra storlek på klustret gör att skripten ska visas i historiken för skriptåtgärder.

Det finns två undantag:

* Om klustret har skapats före den 1 September 2015. Det här datumet är när skriptåtgärder introducerades. Alla kluster som skapats före detta datum kan inte använda skriptåtgärder för att skapa kluster.

* Om du använde flera skriptåtgärder när klustret skapas och används samma namn för flera skript eller samma namn, samma URI, men olika parametrar för flera skript. I dessa fall kan få du följande fel:

    Inga nya skriptet åtgärder kan vara kördes på det här klustret på grund av motstridiga skriptet namnen i befintliga skript. Skriptnamn har angetts på klustret skapa måste vara alla unika. Befintliga skript kördes på storleksändring.

## <a name="next-steps"></a>Nästa steg

* [Utveckla script åtgärd-skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installera och använda Apache Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md)
* [Installera och använda Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md)
* [Lägga till ytterligare lagringsutrymme till ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Steg när klustret skapas"
