---
title: "Anpassa HDInsight-kluster med skriptåtgärder - Azure | Microsoft Docs"
description: "Lägga till anpassade komponenter till Linux-baserade HDInsight-kluster med skriptåtgärder. Skriptåtgärder är Bash-skript som kan användas för att anpassa klusterkonfigurationen eller lägga till ytterligare tjänster och verktyg som Hue, Solr eller R."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: larryfr
ms.openlocfilehash: 5e4fe189a3fa7269a271b422116dc6838e7ef3cb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder

HDInsight tillhandahåller ett konfigurationsalternativ som kallas **skriptåtgärd** som anropar anpassade skript för att anpassa klustret. Dessa skript används för att installera ytterligare komponenter och ändra konfigurationsinställningarna. Skriptåtgärder kan användas under eller efter att klustret har skapats.

> [!IMPORTANT]
> Möjligheten att använda skriptåtgärder på ett kluster som redan körs är endast tillgängligt för Linux-baserat HDInsight-kluster.
>
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Skriptåtgärder kan också publiceras på Azure Marketplace som ett HDInsight-program. Vissa av exemplen i det här dokumentet visar hur du kan installera ett HDInsight-program med hjälp av åtgärden skriptkommandon från PowerShell och .NET SDK. Mer information om HDInsight-program finns [publicera HDInsight-program på Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Behörigheter

Om du använder en domänansluten HDInsight-kluster, finns det två Ambari-behörigheter som krävs vid användning av skriptåtgärder med klustret:

* **AMBARI. KÖR\_anpassad\_kommandot**: den Ambari administratörsroll har denna behörighet som standard.
* **KLUSTER. KÖR\_anpassad\_kommandot**: båda HDInsight-klustrets administratör och Ambari-administratören har denna behörighet som standard.

Mer information om hur du arbetar med behörigheter med domänanslutna HDInsight finns [hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Åtkomstkontroll

Om du inte är administratör/ägaren av din Azure-prenumeration, ditt konto måste ha minst **deltagare** åtkomst till den resursgrupp som innehåller HDInsight-klustret.

Dessutom, om du skapar ett HDInsight-kluster någon med minst **deltagare** åtkomst till Azure-prenumerationen måste redan har registrerat provider för HDInsight. Leverantörregistrering sker när en användare med deltagaråtkomst skapar en resurs för första gången på en prenumeration. Detta kan också inträffa utan att en resurs skapas, om en [leverantör registreras med REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Se följande dokument för mer information om arbete med åtkomst hantering:

* [Kom igång med åtkomsthantering i Azure-portalen](../active-directory/role-based-access-control-what-is.md)
* [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>Förstå skriptåtgärder

En skriptåtgärd är Bash-skript som du anger en URI för och parametrar för. Skriptet körs på noder i HDInsight-klustret. Följande är egenskaper och funktioner för skriptåtgärder.

* Måste vara lagrade på en URI som är tillgänglig från HDInsight-klustret. Följande är möjliga lagringsplatser:

    * En **Azure Data Lake Store** konto som är tillgänglig för HDInsight-klustret. Information om hur du använder Azure Data Lake Store med HDInsight finns i [skapar ett HDInsight-kluster med Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        När du använder ett skript som lagras i Data Lake Store URI-formatet är `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Tjänstens huvudnamn HDInsight använder för åtkomst till Data Lake Store måste ha läsbehörighet till skriptet.

    * En blobb i en **Azure Storage-konto** som antingen primära eller ytterligare lagringskontot för HDInsight-klustret. HDInsight beviljas åtkomst till båda typerna av lagringskonton när klustret skapas.

    * En offentlig fildelning tjänsten Azure Blob, GitHub, OneDrive, Dropbox, t.ex.

        Till exempel URI: er, finns det [exempelskript skript åtgärd](#example-script-action-scripts) avsnitt.

        > [!WARNING]
        > HDInsight stöder endast __allmänna__ Azure Storage-konton. För närvarande stöder inte den __Blob storage__ kontotyp.

* Kan vara begränsad till **körs på vissa nodtyper**, för exempel huvudnoderna eller arbetsnoderna.

* Kan vara **beständiga** eller **ad hoc-**.

    **Beständiga** skript används till arbetsnoder som lagts till i klustret när skriptet har körts. Till exempel när du ökar klustringen.

    Ett bestående skript kan även använda ändringar till en annan nod, till exempel en huvudnod.

  > [!IMPORTANT]
  > Beständiga skriptåtgärder måste ha ett unikt namn.

    **Ad hoc-** skript är inte beständiga. De tillämpas inte till arbetsnoder som lagts till i klustret när skriptet har körts. Du kan därefter befordra en ad hoc-skript för att ett bestående skript eller nedgradera ett bestående skript till en ad hoc-skript.

  > [!IMPORTANT]
  > Skriptåtgärder användas när klustret skapas sparas automatiskt.
  >
  > Skript som misslyckas inte är beständiga, även om du uttryckligen har angett att de ska.

* Kan acceptera **parametrar** som används vid körningen av skriptet.
* Kör med **rätt behörighet för rotmappen** på klusternoderna.
* Kan användas via den **Azure-portalen**, **Azure PowerShell**, **Azure CLI**, eller **HDInsight .NET SDK**

Klustret sparar en historik över alla skript som har varit kördes. Historiken är användbart när du vill söka efter ID för ett skript för befordran och degradering åtgärder.

> [!IMPORTANT]
> Det finns inget automatiskt sätt att ångra de ändringar som gjorts av en skriptåtgärd. Manuellt återställa ändringarna eller ange ett skript som kastar dem.


### <a name="script-action-in-the-cluster-creation-process"></a>Skriptåtgärder i klusterskapandeprocessen

Används när klustret skapas med skriptåtgärder avses skiljer sig från skriptet åtgärder som kördes på ett befintligt kluster:

* Skriptet **automatiskt beständiga**.
* En **fel** i skriptet kan orsaka klusterskapandeprocessen misslyckas.

Följande diagram illustrerar när skriptet körs under skapandeprocessen:

![HDInsight-kluster anpassning och faserna när klustret skapas][img-hdi-cluster-states]

Skriptet körs medan HDInsight konfigureras. I det här skedet skriptet körs parallellt på noderna i klustret och körs med rotprivilegier på noderna.

> [!NOTE]
> Eftersom skriptet körs med privilegium för roten på noderna i klustret, kan du utföra åtgärder som att stoppa och starta tjänster, inklusive Hadoop-relaterade tjänster. Om du stoppar tjänster måste du kontrollera att tjänsten Ambari och andra Hadoop-relaterade tjänster är igång innan körningen för skriptet. Dessa tjänster används för att avgöra korrekt hälsotillstånd och tillstånd för klustret medan det skapas.


När klustret skapas, kan du använda flera skriptåtgärder samtidigt. Dessa skript anropas i den ordning som de har angetts.

> [!IMPORTANT]
> Skriptåtgärder måste slutföras inom 60 minuter eller tidsgräns. Under klusteretablering, körs skriptet samtidigt med andra processer för installation och konfiguration. Konkurrens om resurser, till exempel CPU-tid eller nätverket bandbredd kan göra att skriptet tar längre tid att slutföra än i din utvecklingsmiljö.
>
> Undvik åtgärder som hämtar och sammanställa program från källan för att minimera den tid det tar för att köra skriptet. Före kompilera program och lagrar den binära filen i Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Skriptåtgärder på ett kluster som körs

Till skillnad från skriptet åtgärder som används när klustret skapas ett fel i ett skript kördes på ett kluster som redan körs orsakar automatiskt inte klustret för att ändra till ett felaktigt tillstånd. När ett skript är klar bör klustret återgå till tillståndet ”körs”.

> [!IMPORTANT]
> Även om klustret har tillståndet 'körs', har misslyckade skriptet delats saker. Ett skript kan till exempel ta bort filer som behövs i klustret.
>
> Åtgärder som skript köras med rotprivilegier, så bör du se till att du förstår vad vi gör ett skript innan den tillämpas på ditt kluster.

När du använder ett skript i ett kluster, klustertillstånd ändras från **kör** till **godkända**, sedan **HDInsight configuration**, och slutligen tillbaka till  **Kör** för lyckad skript. Skriptstatus är inloggad i historiken för skriptåtgärder och du kan använda den här informationen för att avgöra om skriptet har lyckats eller misslyckats. Till exempel den `Get-AzureRmHDInsightScriptActionHistory` PowerShell-cmdlet kan användas för att visa status för ett skript. Den returnerar information liknar följande:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> Om du har ändrat kluster-användarlösenord (admin) när klustret har skapats, misslyckas skriptet åtgärder har körts för det här klustret. Om du har några beständiga skriptåtgärder arbetsnoder som mål, misslyckas dessa skript när du skalar klustret.

## <a name="example-script-action-scripts"></a>Exempelskript skript åtgärd

Skriptet åtgärd skript kan användas via följande verktyg:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight tillhandahåller skript för att installera följande komponenter i HDInsight-kluster:

| Namn | Skript |
| --- | --- |
| **Lägg till ett Azure Storage-konto** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxaddstorageaccountv01/Add-Storage-Account-v01.SH. Se [lägga till ytterligare lagringsutrymme i ett HDInsight-kluster](hdinsight-hadoop-add-storage.md). |
| **Installera Hue** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-Hue-uber-v02.SH. Se [installerar och använder Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md). |
| **Installera Presto** |https://Raw.githubusercontent.com/hdinsight/Presto-hdinsight/Master/installpresto.SH. Se [installerar och använder Presto på HDInsight-kluster](hdinsight-hadoop-install-presto.md). |
| **Installera Solr** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxsolrconfigactionv01/solr-Installer-v01.SH. Se [installerar och använder Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md). |
| **Installera Giraph** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxgiraphconfigactionv01/giraph-Installer-v01.SH. Se [installerar och använder Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md). |
| **Läsa in Hive-bibliotek** |https://hdiconfigactions.BLOB.Core.Windows.NET/linuxsetupcustomhivelibsv01/Setup-customhivelibs-v01.SH. Se [lägga till Hive-bibliotek i HDInsight-kluster](hdinsight-hadoop-add-hive-libraries.md). |
| **Installera eller uppdatera Mono** | https://hdiconfigactions.BLOB.Core.Windows.NET/Install-Mono/Install-Mono.Bash. Se [installera eller uppdatera Mono på HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Använd en skriptåtgärd när klustret skapas

Det här avsnittet innehåller exempel på olika sätt som du kan använda script actions när du skapar ett HDInsight-kluster.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Använd en skriptåtgärd när klustret skapas i Azure Portal

1. Skapa ett kluster, enligt beskrivningen i [skapa Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Stoppa när du når den __klustret sammanfattning__ avsnitt.

2. Från den __klustret sammanfattning__ väljer den __redigera__ länka för __avancerade inställningar__.

    ![Avancerade inställningar länk](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Från den __avancerade inställningar__ väljer __skript åtgärder__. Från den __skript åtgärder__ väljer __+ skicka nya__

    ![Skicka en ny skriptåtgärd](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Använd den __väljer du ett skript__ post att välja en fördefinierad skript. Om du vill använda ett anpassat skript __anpassade__ och ange sedan den __namn__ och __Bash skript URI__ för skriptet.

    ![Lägga till ett skript i formuläret väljer skript](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    I följande tabell beskrivs element i formuläret:

    | Egenskap | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ett eget skript __anpassad__. Annars väljer du något av de angivna skript. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash-skript URI |Ange URI till det skript som anropas för att anpassa klustret. |
    | Zookeeper-HEAD/Worker |Ange noderna (**Head**, **Worker**, eller **ZooKeeper**) som anpassning skriptet körs. |
    | Parametrar |Ange parametrar, om det krävs av skriptet. |

    Använd den __spara den här skriptåtgärden__ så att skriptet ska användas vid skalning åtgärder.

5. Välj __skapa__ spara skriptet. Du kan sedan använda __+ skicka nya__ att lägga till ett annat skript.

    ![Flera skriptåtgärder](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    När du är klar att lägga till skript som använder den __Välj__ knappen, och sedan den __nästa__ vill gå tillbaka till den __klustret sammanfattning__ avsnitt.

3. Om du vill skapa klustret __skapa__ från den __klustret sammanfattning__ val.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Använd en skriptåtgärd från Azure Resource Manager-mallar

Skriptåtgärder kan användas med Azure Resource Manager-mallar. Ett exempel finns [https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/](https://azure.microsoft.com/en-us/resources/templates/hdinsight-linux-run-script-action/).

I det här exemplet skriptåtgärden lades till med följande kod:

    "scriptActions": [
        {
            "name": "setenvironmentvariable",
            "uri": "[parameters('scriptActionUri')]",
            "parameters": "headnode"
        }
    ]

Information om hur du distribuerar en mall finns i följande dokument:

* [Distribuera resurser med mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Distribuera resurser med mallar och Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Använd en skriptåtgärd när klustret skapas från Azure PowerShell

I det här avsnittet kan du använda den [Lägg till AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) för att anropa skript för att anpassa ett kluster. Innan du fortsätter bör du kontrollera att du har installerat och konfigurerat Azure PowerShell. Information om hur du konfigurerar en arbetsstation för att köra HDInsight PowerShell-cmdlets finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Följande skript visar hur du använder en skriptåtgärd när du skapar ett kluster med hjälp av PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Det kan ta flera minuter innan klustret har skapats.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Använd en skriptåtgärd när klustret skapas från HDInsight .NET SDK

HDInsight .NET SDK innehåller klientbibliotek som gör det enklare att arbeta med HDInsight från ett .NET-program. Kodexempel är finns [skapa Linux-baserade kluster i HDInsight med hjälp av .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Tillämpa en skriptåtgärd till ett kluster som körs

I det här avsnittet lär du dig hur du vidtar skriptåtgärder på ett kluster som körs.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Tillämpa en skriptåtgärd till ett kluster som körs i Azure Portal

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt HDInsight-kluster.

2. Översikt för HDInsight-kluster, Välj den **skriptåtgärder** panelen.

    ![Skriptet åtgärder sida vid sida](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Du kan också välja **alla inställningar** och välj sedan **skriptåtgärder** från avsnittet Inställningar.

3. Upp i avsnittet script actions, Välj **skicka nya**.

    ![Lägga till ett skript i ett kluster som körs](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Använd den __väljer du ett skript__ post att välja en fördefinierad skript. Om du vill använda ett anpassat skript __anpassade__ och ange sedan den __namn__ och __Bash skript URI__ för skriptet.

    ![Lägga till ett skript i formuläret väljer skript](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    I följande tabell beskrivs element i formuläret:

    | Egenskap | Värde |
    | --- | --- |
    | Välj ett skript | Om du vill använda ett eget skript __anpassade__. Välj annars en skriptet. |
    | Namn |Ange ett namn för skriptåtgärden. |
    | Bash-skript URI |Ange URI till det skript som anropas för att anpassa klustret. |
    | Zookeeper-HEAD/Worker |Ange noderna (**Head**, **Worker**, eller **ZooKeeper**) som anpassning skriptet körs. |
    | Parametrar |Ange parametrar, om det krävs av skriptet. |

    Använd den __spara den här skriptåtgärden__ post att kontrollera att skriptet tillämpas under skalning åtgärder.

5. Använd slutligen den **skapa** för att tillämpa skriptet till klustret.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Tillämpa en skriptåtgärd till ett kluster som körs från Azure PowerShell

Innan du fortsätter bör du kontrollera att du har installerat och konfigurerat Azure PowerShell. Information om hur du konfigurerar en arbetsstation för att köra HDInsight PowerShell-cmdlets finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Exemplet nedan visar hur du använder en skriptåtgärd till ett kluster som körs:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

När åtgärden har slutförts får du information som liknar följande:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Tillämpa en skriptåtgärd till ett kluster som körs från Azure CLI

Innan du fortsätter bör du kontrollera att du har installerat och konfigurerat Azure CLI. Mer information finns i [installerar Azure CLI](../cli-install-nodejs.md).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Om du vill växla till läget Azure Resource Manager, använder du följande kommando på kommandoraden:

        azure config mode arm

2. Använd följande för att autentisera till Azure-prenumeration.

        azure login

3. Använd följande kommando för att tillämpa en skriptåtgärd till ett kluster som körs

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Om du utelämnar parametrar för kommandot uppmanas för dessa. Om skriptet som du anger med `-u` accepterar parametrar, så kan du ange dem med hjälp av den `-p` parameter.

    Giltig nodtyper är `headnode`, `workernode`, och `zookeeper`. Om skriptet ska kopplas till flera nodtyper kan ange vilka typer som avgränsas med ett ';'. Till exempel `-n headnode;workernode`.

    För att bevara skriptet för att lägga till den `--persistOnSuccess`. Du kan också spara skriptet senare med hjälp av `azure hdinsight script-action persisted set`.

    När jobbet har slutförts visas utdata som liknar följande:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Tillämpa en skriptåtgärd till ett kluster som körs med hjälp av REST API

Se [kör skriptåtgärder på ett kluster som körs](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Tillämpa en skriptåtgärd till ett kluster som körs från HDInsight .NET SDK

Ett exempel på med .NET SDK för att använda skript till ett kluster, se [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Visa historik, uppgradera och degradera skriptåtgärder

### <a name="using-the-azure-portal"></a>Använda Azure Portal

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt HDInsight-kluster.

2. Översikt för HDInsight-kluster, Välj den **skriptåtgärder** panelen.

    ![Skriptet åtgärder sida vid sida](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Du kan också välja **alla inställningar** och välj sedan **skriptåtgärder** från avsnittet Inställningar.

4. En historik över skript för det här klustret visas i avsnittet skript åtgärder. Den här informationen innehåller en lista över bestående skript. I skärmbilden nedan ser du att Solr skriptet har körts på detta kluster. Skärmbilden visar inte alla beständiga skript.

    ![Skriptet Åtgärdsavsnitt](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Om du väljer ett skript från historiken visas avsnittet Egenskaper för det här skriptet. Du kan köra skriptet eller befordra högst upp på skärmen.

    ![Åtgärder skriptegenskaper](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Du kan också använda den **...**  till höger om poster i avsnittet skript åtgärder att utföra åtgärder.

    ![Script åtgärder... användning](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

| Använd följande... | Att... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Hämta information om beständiga skriptåtgärder |
| Get-AzureRmHDInsightScriptActionHistory |Hämta en historik över skriptåtgärder tillämpas på klustret eller information för ett visst skript |
| Set-AzureRmHDInsightPersistedScriptAction |Främjar beständiga skriptåtgärder ad hoc-skriptåtgärd |
| Remove-AzureRmHDInsightPersistedScriptAction |Flyttar ett beständiga skriptåtgärder till en ad hoc-åtgärd |

> [!IMPORTANT]
> Med hjälp av `Remove-AzureRmHDInsightPersistedScriptAction` inte återställa de åtgärder som utförs av ett skript. Den här cmdleten tar bara bort flaggan beständiga.

Följande exempelskript visar med hjälp av cmdlets befordra och sedan nedgradera ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Använda Azure CLI

| Använd följande... | Att... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Hämta en lista över beständiga skriptåtgärder |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Hämta information om en specifik beständiga skriptåtgärder |
| `azure hdinsight script-action history list <clustername>` |Hämta en historik över skriptåtgärder tillämpas på klustret |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Hämta information om en specifik skriptåtgärd |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Främjar beständiga skriptåtgärder ad hoc-skriptåtgärd |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Flyttar ett beständiga skriptåtgärder till en ad hoc-åtgärd |

> [!IMPORTANT]
> Med hjälp av `azure hdinsight script-action persisted delete` inte återställa de åtgärder som utförs av ett skript. Den här cmdleten tar bara bort flaggan beständiga.

### <a name="using-the-hdinsight-net-sdk"></a>Med HDInsight .NET SDK

Ett exempel på med .NET SDK för att hämta skriptet historik från ett kluster, uppgradera eller degradera skript, se [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Det här exemplet visar även hur du installerar ett HDInsight-program med .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för öppen källkod programvara som används i HDInsight-kluster

Tjänsten Microsoft Azure HDInsight använder ett ekosystem med öppen källkod tekniker formaterat runt Hadoop. Microsoft Azure tillhandahåller en allmän supportnivå för öppen källkod tekniker. Mer information finns i **stöd Scope** avsnitt i den [Azure Support FAQ webbplats](https://azure.microsoft.com/support/faq/). HDInsight-tjänst ger ytterligare en säkerhetsnivå för stöd för inbyggda komponenter.

Det finns två typer av komponenter som öppen källkod som är tillgängliga i HDInsight-tjänst:

* **Inbyggda komponenter** -komponenterna är förinstallerat på HDInsight-kluster och tillhandahåller huvudfunktionerna i klustret. Till exempel YARN ResourceManager Hive-frågespråket (HiveQL) och Mahout biblioteket som hör till den här kategorin. En fullständig lista över komponenter i serverkluster finns i [vad är nytt i Hadoop-klusterversioner som tillhandahålls av HDInsight](hdinsight-component-versioning.md).
* **Anpassade komponenter** -kan du som användare i klustret, installera eller använda i din arbetsbelastning någon komponent som är tillgängliga i communityn eller skapades av du.

> [!WARNING]
> Komponenter som ingår i HDInsight-kluster stöds fullt ut. Microsoft Support hjälper till att identifiera och lösa problem relaterade till komponenterna.
>
> Anpassade komponenter få kommersiellt rimliga stöd för att hjälpa dig att felsöka problemet ytterligare. Microsoft-supporten kanske kan lösa problemet eller de be dig att delta tillgängliga kanaler för öppen källkod där djup expertis för att teknik finns. Det finns till exempel många community-webbplatser som kan användas, t.ex: [MSDN-forum för HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Apache-projekt har också project-webbplatser [http://apache.org](http://apache.org), till exempel: [Hadoop](http://hadoop.apache.org/).

HDInsight-tjänst finns flera sätt att använda anpassade komponenter. Samma nivå av stöd gäller oavsett hur en komponent används eller installeras i klustret. I följande lista beskrivs de vanligaste sätt att anpassade komponenter kan användas på HDInsight-kluster:

1. Jobbet - Hadoop och andra typer av jobb som kör eller använda anpassade komponenter kan skickas till klustret.

2. Anpassning av kluster - när klustret skapas som du kan ange ytterligare inställningar och anpassade komponenter som är installerade på klusternoderna.

3. Exempel - för populära anpassade komponenter, Microsoft och andra kan ge exempel på hur du kan använda de här komponenterna i HDInsight-kluster. De här exemplen tillhandahålls utan stöd.

## <a name="troubleshooting"></a>Felsökning

Du kan använda Ambari-webbgränssnittet för att visa information som loggas av skriptåtgärder. Om skriptet misslyckas när klustret skapas finns även loggarna standardkontot för lagring som är associerade med klustret. Det här avsnittet innehåller information om hur du hämtar loggarna använder båda dessa alternativ.

### <a name="using-the-ambari-web-ui"></a>Använda Ambari-webbgränssnittet

1. Navigera till https://CLUSTERNAME.azurehdinsight.net i din webbläsare. Ersätt KLUSTERNAMN med namnet på ditt HDInsight-kluster.

    När du uppmanas, anger du kontonamnet för admin (admin) och lösenord för klustret. Du kan behöva ange administratörsautentiseringsuppgifter i ett webbformulär.

2. I menyraden överst på sidan Välj den **ops** post. En lista över aktuella och tidigare åtgärder utföras på klustret via Ambari visas.

    ![Ambari web UI-fältet med ops som valts](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Poster som har **kör\_customscriptaction** i den **Operations** kolumn. Dessa poster skapas när åtgärderna som skript körs.

    ![Skärmbild av åtgärder](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Om du vill visa resultatet STDOUT och STDERR Välj posten run\customscriptaction och detaljvisning länkarna. Den här utdata skapas när skriptet körs, och kan innehålla användbar information.

### <a name="access-logs-from-the-default-storage-account"></a>Åtkomstloggar från standardkontot för lagring

Om klustret har skapats misslyckas på grund av ett skriptfel, behålls loggarna i klustret storage-konto.

* Storage-loggar finns på `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Skärmbild av åtgärder](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Under den här katalogen ordnas loggarna separat för headnode, workernode och zookeeper-noder. Några exempel är:

    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Arbetsnoden** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper-nod** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Alla stdout och stderr för motsvarande värden har överförts till lagringskontot. Det finns en **utdata -\*.txt** och **fel -\*.txt** för varje skriptåtgärd. Utdata *.txt-filen innehåller information om URI: N för skript som får köras på värden. Exempel

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Det är möjligt att du skapar ett skript åtgärd kluster flera gånger med samma namn. I så fall kan du skilja relevanta loggar baserat på mappnamnet datum. Mappstrukturen för ett kluster (det här klustret) som skapats på olika datum visas till exempel liknar följande loggposter:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Om du skapar ett skript åtgärd kluster med samma namn på samma dag, kan du använda unikt prefix för att identifiera de relevanta loggfilerna.

* Om du skapar ett kluster nära 12:00:00 (midnatt), är det möjligt att fördela loggfilerna mellan två dagar. I sådana fall måste se du två olika datum mappar för samma kluster.

* Ladda upp loggfilerna till standardbehållaren kan ta upp till 5 minuter, särskilt för stora kluster. Så om du vill komma åt loggarna bör du inte omedelbart bort klustret om en skriptåtgärd misslyckas.

### <a name="ambari-watchdog"></a>Ambari watchdog

> [!WARNING]
> Ändra inte lösenordet för Ambari Watchdog (hdinsightwatchdog) på Linux-baserade HDInsight-kluster. Ändra lösenordet för det här kontot bryts möjlighet att köra nya skriptåtgärder i HDInsight-klustret.

### <a name="cant-import-name-blobservice"></a>Det går inte att importera namn BlobService

__Symptom__: skript åtgärden misslyckas. När du visar igen i Ambari visas texten som liknar följande fel:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Orsak__: det här felet uppstår om du uppgraderar Python Azure Storage-klienten som ingår i HDInsight-klustret. HDInsight förväntar Azure Storage client 0.20.0.

__Lösning__: åtgärda felet manuellt ansluta till varje nod med `ssh` och använda följande kommando för att installera om rätt lagring klientversionen:

```
sudo pip install azure-storage==0.20.0
```

Information om hur du ansluter till klustret med SSH finns [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Historik visar inte skript som ska användas när klustret skapas

Om klustret har skapats innan den 15 mars 2016 kan du inte se en post i historiken för skriptåtgärder. Ändra storlek på klustret gör skript ska visas i historiken för skriptåtgärder.

Det finns två undantag:

* Om klustret har skapats före den 1 September 2015. Det här datumet är när skriptåtgärder introducerades. Ett kluster som skapats före det här datumet kunde inte har använt skriptåtgärder för klustret skapas.

* Om du använde flera skriptåtgärder när klustret skapas och används samma namn för flera skript eller samma namn, samma URI, men olika parametrar för flera skript. I dessa fall visas följande felmeddelande:

    Nya åtgärder kan vara skript kördes på det här klustret på grund av skriptnamn i konflikt i befintliga skript. Skriptnamn på klustret skapa måste vara alla unika. Befintliga skript kördes på ändring av storlek.

## <a name="next-steps"></a>Nästa steg

* [Utveckla skript åtgärd skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installera och använda Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md)
* [Installera och använda Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md)
* [Lägga till ytterligare lagringsutrymme i ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Steg när klustret skapas"
