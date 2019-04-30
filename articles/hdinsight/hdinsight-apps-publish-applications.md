---
title: Publicera Azure HDInsight-program
description: Lär dig hur du skapar ett HDInsight-program och sedan publicera den på Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097415"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicera ett HDInsight-program på Azure Marketplace
Du kan installera ett Azure HDInsight-program på ett Linux-baserade HDInsight-kluster. I den här artikeln lär du dig hur du publicerar ett HDInsight-program på Azure Marketplace. Allmän information om hur du publicerar på Azure Marketplace finns i [publicera ett erbjudande på Azure Marketplace](../marketplace/marketplace-publishers-guide.md).

HDInsight-program använda den *Bring Your Own License (BYOL)* modellen. I ett scenario med BYOL ansvarar en programmets provider för att licensiera programmet till app-användare. App-användare debiteras endast för de Azure resurser som de skapar, till exempel HDInsight-kluster och klustrets virtuella datorer och noder. Faktureringen för själva programmet inträffar för närvarande inte i Azure.

Mer information finns i dessa HDInsight programrelaterade artiklar:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md). Lär dig hur du installerar ett HDInsight-program på dina kluster.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md). Lär dig hur du installerar och testar anpassade HDInsight-program.

## <a name="prerequisites"></a>Nödvändiga komponenter
Att skicka ditt anpassade program på Marketplace, först [skapa och testa ditt anpassade program](hdinsight-apps-install-custom-applications.md).

Du måste också registrera ditt utvecklarkonto. Mer information finns i [publicera ett erbjudande på Azure Marketplace](../marketplace/marketplace-publishers-guide.md) och [skapa ett Microsoft Developer-konto](../marketplace/marketplace-publishers-guide.md).

## <a name="define-the-application"></a>Definiera programmet
Två steg vid publicering av program på Marketplace. Definiera först en *createUiDef.json* fil. CreateUiDef.json-fil anger vilka kluster programmet är kompatibelt med. Sedan publicerar du mallen från Azure-portalen. Här är ett exempel createUiDef.json-fil:

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

| Fält | Beskrivning | Möjliga värden |
| --- | --- | --- |
| typer |De klustertyper programmet är kompatibelt med. |Hadoop, HBase, Storm, Spark (eller en kombination av dessa) |
| versioner |De typer av HDInsight-kluster programmet är kompatibelt med. |3.4 |

## <a name="application-installation-script"></a>Installationsskriptet för programmet
När ett program har installerats på ett kluster (antingen på ett befintligt kluster eller på en ny) skapas en kantnod. Installationsskriptet för program som körs på gränsnoden.

  > [!IMPORTANT]  
  > Namnet på installationsskriptet för programmet måste vara unikt för ett specifikt kluster. Namnet på skriptet måste ha följande format:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > Skriptets namn består av tre delar:
  > 
  > * Ett skript-namnprefix, som måste innehålla namnet på programmet eller ett namn som är relevant för programmet.
  > * Ett bindestreck för läsbarhet.
  > * En unik Strängfunktion, med namnet på programmet som parameter.
  > 
  > I åtgärdslistan bestående skript i föregående exempel visas som **hue-install-v0-4wkahss55hlas**. Se en [JSON-exempelnyttolast](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Installationsskriptet måste ha följande egenskaper:
* Skriptet är idempotent. Flera anrop till skriptet ger samma resultat.
* Skriptet skapas korrekt. Använd en annan plats för skriptet när du uppgraderar eller testa ändringar. Detta säkerställer att kunder som installerar programmet inte är berörda av dina uppdateringar eller testning. 
* Skriptet har tillräcklig loggning vid varje punkt. Skriptet loggarna är vanligtvis är det enda sättet att felsöka problem med installation av programmet.
* Anrop till externa tjänster eller resurser ha tillräcklig återförsök så att installationen inte påverkas av tillfälliga nätverksproblem.
* Om skriptet startar tjänster på noderna, övervakas och konfigurerad för att starta automatiskt om en omstart av nod inträffar tjänster.

## <a name="package-the-application"></a>Paketera programmet
Skapa en ZIP-fil som innehåller alla filer som krävs för att installera programmet HDInsight. Du kan använda .zip-filen för att publicera programmet. ZIP-filen innehåller följande filer:

* createUiDefinition.json
* mainTemplate.json (ett exempel finns [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).)
* Alla nödvändiga skript

> [!NOTE]  
> Du kan ha programfilerna (inklusive eventuella web app-filer) på valfri offentligt tillgänglig slutpunkt.

## <a name="publish-the-application"></a>Publicera programmet
Publicera ett HDInsight-program:

1. Logga in på [Azure-publicering](https://publish.windowsazure.com/).
2. I den vänstra menyn väljer du **lösningsmallar**.
3. Ange en rubrik och välj sedan **skapa en ny lösningsmall**.
4. Om du inte redan har registrerat din organisation väljer **skapa Dev Center-konto och delta i Azure program**.  Mer information finns i [skapa ett Microsoft Developer-konto](../marketplace/marketplace-publishers-guide.md).
5. Välj **definiera några topologier för att komma igång**. En lösningsmall är ”överordnad” till alla dess topologier. Du kan definiera flera topologier i en mall för erbjudandet eller lösningen. När ett erbjudande skickas till mellanlagringen, skickas det med alla dess topologier. 
6. Ange ett topologinamn och välj sedan **+**.
7. Ange en ny version och välj sedan **+**.
8. Ladda upp ZIP-filen som du skapade när du paketeras programmet.  
9. Välj **begär certifiering**. Microsofts certifieringsteam granskar filerna och certifierar topologin.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [installera HDInsight-program](hdinsight-apps-install-applications.md) i dina kluster.
* Lär dig hur du [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md) och distribuerar ett Opublicerat HDInsight-program till HDInsight.
* Lär dig hur du [använder skriptåtgärd för att anpassa Linux-baserade HDInsight-kluster](hdinsight-hadoop-customize-cluster-linux.md) och lägga till fler program. 
* Lär dig hur du [skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Lär dig hur du [använder en tom edge-nod i HDInsight](hdinsight-apps-use-edge-node.md) för att komma åt HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

