---
title: Publicera program med Azure HDInsight | Microsoft Docs
description: "Lär dig hur du skapar ett HDInsight-program och publicera den i Azure Marketplace."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: jgao
ms.openlocfilehash: 34550ed33cd81bcbf5b405a5e5c09d25adf5e6ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publicera ett HDInsight-program i Azure Marketplace
Du kan installera ett Azure HDInsight-program på en Linux-baserade HDInsight-kluster. I den här artikeln lär du dig hur du publicerar ett HDInsight-program i Azure Marketplace. Allmän information om hur du publicerar i Azure Marketplace finns [publicera ett erbjudande på Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

HDInsight-program används den *Bring Your Own License (BYOL)* modell. I ett scenario med BYOL ansvarar en programmets provider för att licensiera programmet till användarna. Användarna debiteras bara för Azure-resurser de skapar, till exempel HDInsight-kluster och klustrets virtuella datorer och noder. Fakturering för programmet inträffar för närvarande inte i Azure.

Mer information finns i dessa artiklar om HDInsight-program:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md). Lär dig hur du installerar ett HDInsight-program på ditt kluster.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md). Lär dig hur du installerar och testar anpassade HDInsight-program.

## <a name="prerequisites"></a>Krav
Att skicka det anpassade programmet på marknaden först [skapa och testa det anpassade programmet](hdinsight-apps-install-custom-applications.md).

Du måste också registrera ditt utvecklarkonto. Mer information finns i [publicera ett erbjudande på Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) och [skapa ett konto på Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-the-application"></a>Definiera programmet
Två steg ingår i publicering av program på Marketplace. Först definierar du en *createUiDef.json* fil. CreateUiDef.json-fil anger vilka kluster programmet är kompatibelt med. Sedan publicerar du mallen från Azure-portalen. Här är ett exempel createUiDef.json-fil:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "tiers": ["Standard", "Premium"],
        "versions": ["3.4"]
    }
}
```

| Fält | Beskrivning | Möjliga värden |
| --- | --- | --- |
| typer |De klustertyper programmet är kompatibelt med. |Hadoop, HBase, Storm, Spark (eller en kombination av dessa) |
| nivåer |De klusternivåer programmet är kompatibelt med. |Standard, Premium (eller båda) |
| versioner |De typer av HDInsight-kluster programmet är kompatibelt med. |3.4 |

## <a name="application-installation-script"></a>Installationsskriptet för programmet
När ett program har installerats på ett kluster (antingen på ett befintligt kluster, eller på en ny), skapas en kantnod. Installationsskriptet för programmet körs på kantnoden.

  > [!IMPORTANT]
  > Namnet på installationsskriptet för programmet måste vara unikt för ett specifikt kluster. Namnet på skriptkommandot måste ha följande format:
  > 
  > ”name” ”: [concat ('hue-install-v0 ','-', uniquestring('applicationName')]”
  > 
  > Skriptets namn består av tre delar:
  > 
  > * Ett skriptnamnsprefix som måste innehålla namnet på programmet eller ett namn som är relevant för programmet.
  > * Ett bindestreck för att läsa.
  > * En unik Strängfunktion, med namnet på programmet som parameter.
  > 
  > I listan med beständiga skriptåtgärder åtgärden visas i föregående exempel som **hue-install-v0-4wkahss55hlas**. Se en [exempel JSON-nyttolast](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Installationsskriptet måste ha följande egenskaper:
* Skriptet är idempotent. Flera anrop till skriptet ger samma resultat.
* Skriptet skapas korrekt. Använd en annan plats för skriptet när du uppgraderar eller testa ändringar. Detta säkerställer att kunder som installerar programmet inte är berörda av dina uppdateringar eller tester. 
* Skriptet har tillräcklig loggning på varje plats. Vanligtvis är skript loggarna det enda sättet att felsöka problem med installation av programmet.
* Anrop till externa tjänster eller resurser ha tillräcklig återförsök så att installationen inte påverkas av tillfälliga nätverksproblem.
* Om skriptet startas tjänster på noderna, tjänster övervakas och konfigurerad för att starta automatiskt om en omstart av nod inträffar.

## <a name="package-the-application"></a>Paketera programmet
Skapa en ZIP-fil som innehåller alla filer som krävs för att installera HDInsight-program. Du använder ZIP-filen till [publicera programmet](#publish-application). ZIP-filen innehåller följande filer:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (ett exempel finns [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).)
* Alla nödvändiga skript

> [!NOTE]
> Du kan vara värd programfilerna (inklusive eventuella web app-filer) på valfri offentligt tillgänglig slutpunkt.
> 

## <a name="publish-the-application"></a>Publicera programmet
För att publicera ett HDInsight-program:

1. Logga in på [Azure publicering](https://publish.windowsazure.com/).
2. Välj i den vänstra menyn **lösningsmallar**.
3. Ange en rubrik och välj sedan **skapa en ny lösningsmall**.
4. Om du inte redan har registrerat din organisation, Välj **skapa Dev Center-konto och Anslut till Azure programmet**.  Mer information finns i [skapa ett konto på Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Välj **ange några topologier för att komma igång**. En lösningsmall är en ”överordnad” alla dess topologier. Du kan definiera flera topologier i en mall för lösning eller erbjudandet. När ett erbjudande pushas till mellanlagring pushas den med alla sina topologier. 
6. Ange ett namn på topologin och välj sedan  **+** .
7. Ange en ny version och markera sedan  **+** .
8. Ladda upp ZIP-filen som du skapade när du [paketeras programmet](#package-application).  
9. Välj **begär certifiering**. Microsofts certifieringsteam granskar filerna och certifierar topologin.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [installera HDInsight-program](hdinsight-apps-install-applications.md) i ditt kluster.
* Lär dig hur du [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md) och distribuerar ett Opublicerat HDInsight-program till HDInsight.
* Lär dig hur du [använder skriptåtgärd till att anpassa Linux-baserade HDInsight-kluster](hdinsight-hadoop-customize-cluster-linux.md) och lägga till fler program. 
* Lär dig hur du [skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Lär dig hur du [använda en tom kantnod i HDInsight](hdinsight-apps-use-edge-node.md) testa HDInsight-program för att komma åt HDInsight-kluster och vara värd för HDInsight-program.

