---
title: Publicera Azure HDInsight-program
description: Lär dig hur du skapar ett HDInsight-program och publicerar det sedan på Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64685329"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicera ett HDInsight-program på Azure Marketplace
Du kan installera ett Azure HDInsight-program på ett Linux-baserat HDInsight-kluster. I den här artikeln får du lära dig hur du publicerar ett HDInsight-program på Azure Marketplace. Allmän information om publicering på Azure Marketplace finns [i Publicera ett erbjudande på Azure Marketplace](../marketplace/marketplace-publishers-guide.md).

HDInsight-program använder *byol-modellen (Bring Your Own License).* I ett BYOL-scenario ansvarar en programleverantör för licensiering av programmet till appanvändare. Appanvändare debiteras endast för de Azure-resurser som de skapar, till exempel HDInsight-klustret och klustrets virtuella datorer och noder. Fakturering för själva programmet sker för närvarande inte i Azure.

Mer information finns i följande HDInsight-programrelaterade artiklar:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md). Lär dig hur du installerar ett HDInsight-program i klustren.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md). Lär dig hur du installerar och testar anpassade HDInsight-program.

## <a name="prerequisites"></a>Krav
Om du vill skicka in ditt anpassade program på Marketplace [skapar och testar du](hdinsight-apps-install-custom-applications.md)först ditt anpassade program .

Du måste också registrera ditt utvecklarkonto. Mer information finns [i Publicera ett erbjudande på Azure Marketplace](../marketplace/marketplace-publishers-guide.md) och Skapa ett Microsoft [Developer-konto](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Definiera programmet
Två steg är involverade i publiceringsprogram på Marketplace. Definiera först en *createUiDef.json-fil.* Filen createUiDef.json anger vilka kluster ditt program är kompatibelt med. Publicera sedan mallen från Azure-portalen. Här är ett exempel createUiDef.json fil:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Field | Beskrivning | Möjliga värden |
| --- | --- | --- |
| typer |De klustertyper programmet är kompatibelt med. |Hadoop, HBase, Storm, Spark (eller någon kombination av dessa) |
| versioner |De typer av HDInsight-kluster programmet är kompatibelt med. |3.4 |

## <a name="application-installation-script"></a>Skript för programinstallation
När ett program installeras i ett kluster (antingen i ett befintligt kluster eller på ett nytt) skapas en kantnod. Programinstallationsskriptet körs på kantnoden.

  > [!IMPORTANT]  
  > Namnet på programinstallationsskriptet måste vara unikt för ett visst kluster. Skriptnamnet måste ha följande format:
  > 
  > "name": "[concat('hue-install-v0','-',uniquestring('applicationName')]"
  > 
  > Skriptnamnet har tre delar:
  > 
  > * Ett skriptnamnsprefix som måste innehålla antingen programnamnet eller ett namn som är relevant för programmet.
  > * Ett bindestreck, för läsbarhet.
  > * En unik strängfunktion med programnamnet som parameter.
  > 
  > I den beständiga skriptåtgärdslistan visas föregående exempel som **hue-install-v0-4wkahss55hlas**. Se ett [exempel på JSON-nyttolast](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Installationsskriptet måste ha följande egenskaper:
* Skriptet är idempotent. Flera anrop till skriptet ger samma resultat.
* Skriptet är korrekt versions. Använd en annan plats för skriptet när du uppgraderar eller testar ändringar. Detta säkerställer att kunder som installerar programmet inte påverkas av dina uppdateringar eller tester. 
* Skriptet har tillräcklig loggning vid varje punkt. Vanligtvis är skriptloggar det enda sättet att felsöka programinstallationsproblem.
* Anrop till externa tjänster eller resurser har tillräckliga återförsök så att installationen inte påverkas av tillfälliga nätverksproblem.
* Om skriptet startar tjänster på noderna övervakas och konfigureras tjänsterna så att de startas automatiskt om en omstart av noden inträffar.

## <a name="package-the-application"></a>Paketera programmet
Skapa en ZIP-fil som innehåller alla filer som krävs för att installera HDInsight-programmet. Du kan använda ZIP-filen för att publicera programmet. Zip-filen innehåller följande filer:

* createUiDefinition.json
* mainTemplate.json (För ett exempel, se [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).)
* Alla skript som krävs

> [!NOTE]  
> Du kan vara värd för programfilerna (inklusive webbappfiler) på alla offentligt tillgängliga slutpunkter.

## <a name="publish-the-application"></a>Publicera programmet
Så här publicerar du ett HDInsight-program:

1. Logga in på [Azure Publishing](https://publish.windowsazure.com/).
2. Välj **Lösningsmallar**på den vänstra menyn .
3. Ange en rubrik och välj sedan **Skapa en ny lösningsmall**.
4. Om du inte redan har registrerat din organisation väljer du **Skapa Dev Center-konto och går med i Azure-programmet**.  Mer information finns i [Skapa ett Microsoft Developer-konto](../marketplace/marketplace-publishers-guide.md).
5. Välj **Definiera vissa topologier för att komma igång**. En lösningsmall är en "överordnad" till alla dess topologier. Du kan definiera flera topologier i en erbjudande- eller lösningsmall. När ett erbjudande skjuts till iscensättning, skjuts det med alla dess topologier. 
6. Ange ett topologinamn och **+** välj sedan .
7. Ange en ny version **+** och välj sedan .
8. Ladda upp ZIP-filen som du skapade när du paketerade programmet.  
9. Välj **Begär certifiering**. Microsofts certifieringsteam granskar filerna och certifierar topologin.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [installerar HDInsight-program](hdinsight-apps-install-applications.md) i dina kluster.
* Lär dig hur du [installerar anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md) och distribuerar ett opublicerat HDInsight-program till HDInsight.
* Lär dig hur du [använder Skriptåtgärd för att anpassa Linux-baserade HDInsight-kluster](hdinsight-hadoop-customize-cluster-linux.md) och lägga till fler program. 
* Lär dig hur du [skapar Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Lär dig hur du [använder en tom kantnod i HDInsight](hdinsight-apps-use-edge-node.md) för att komma åt HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

