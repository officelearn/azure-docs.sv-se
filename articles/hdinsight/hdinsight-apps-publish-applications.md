---
title: Publicera HDInsight-program | Microsoft Docs
description: "Ta reda på hur du skapar och publicerar HDInsight-program."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 35a6c06bc4850f3fcfc6221d62998465f3b38251


---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publicera HDInsight-program på Azure Marketplace
Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv. I den här artikeln får du lära dig hur du publicerar ett HDInsight-program på Azure Marketplace.  Allmän information om hur du publicerar på Azure Marketplace finns i [Publicera ett erbjudande på Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

För HDInsight-program används modellen med *Bring Your Own License (BYOL)*. Det innebär att programmets provider ansvarar för att licensiera programmet till slutanvändarna och slutanvändarna endast debiteras av Azure för de resurser som de skapar, till exempel HDInsight-klustret och dess virtuella datorer/noder. För tillfället sker debiteringen för programmet inte via Azure.

Ytterligare artikel om HDInsight-program:

* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Läs mer om hur du installerar ett HDInsight-program till dina kluster.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): information om hur du installerar och testar anpassade HDInsight-program.

## <a name="prerequisites"></a>Krav
Du måste ha skapat och testat det anpassade programmet innan du kan skicka det till Marketplace. Se följande artiklar:

* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): information om hur du installerar och testar anpassade HDInsight-program.

Du måste också ha registrerat ett utvecklarkonto. Se [Publicera ett erbjudande på Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) och [Skapa ett Microsoft utvecklarkonto](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Definiera programmet
Du måste genomföra två steg för att publicera program på Azure Marketplace.  Först definierar du en **createUiDef.json**-fil och anger vilka kluster programmet är kompatibelt med. Sedan publicerar du mallen från Azure Portal. Följande är ett exempel på en createUiDef.json-fil.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| Fält | Beskrivning | Möjliga värden |
| --- | --- | --- |
| typer |De klustertyper programmet är kompatibelt med. |Hadoop, HBase, Storm, Spark (eller en kombination av dem) |
| nivåer |De klusternivåer programmet är kompatibelt med. |Standard, Premium (eller båda) |
| versioner |De typer av HDInsight-kluster programmet är kompatibelt med. |3.4 |

## <a name="package-application"></a>Paketera programmet
Skapa en zip-fil som innehåller alla filer som krävs för att installera dina HDInsight-program. Du behöver zip-filen från [Publicera program](#publish-application).

* [createUiDefinition.json](#define-application).
* mainTemplate.json. Du hittar exempel i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).
  
  > [!IMPORTANT]
  > Namnet på installationsskriptet för programmet måste vara unikt för ett visst kluster och ha nedanstående format. Dessutom ska alla installations- och avinstallationsskriptåtgärder vara idempotenta, vilket innebär att skripten kan anropas upprepade gånger och samtidigt producera samma resultat.
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > Observera att skriptets namn består av tre delar:
  > 
  > 1. Ett skriptnamnsprefix, vilket antingen innehåller namnet på programmet eller ett namn som är relevant för programmet.
  > 2. Ett "-" för läsbarhet.
  > 3. En unik strängfunktion med namnet på programmet som parameter.
  > 
  > Ett exempel på ovanstående ser ut så här: hue-install-v0-4wkahss55hlas i listan med beständiga skriptåtgärder. Ett exempel på en JSON-nyttolast finns på [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 
  > 
* Alla nödvändiga skript.

> [!NOTE]
> Programfilerna (inklusive filer för webbappen om det finns några) kan placeras på valfri offentligt tillgänglig slutpunkt.
> 
> 

## <a name="publish-application"></a>Publicera programmet
Följ stegen nedan om du vill publicera ett HDInsight-program:

1. Logga in på [Azure Publishing portal](https://publish.windowsazure.com/) (Azures publiceringsportal).
2. Klicka på **Lösningsmallar** till vänster och skapa en ny lösningsmall.
3. Ange ett namn och klicka på **Skapa en ny lösningsmall**.
4. Klicka på **Create Dev Center account and join the Azure program** (Skapa konto för utvecklingscentret och koppla Azure-programmet) och registrera ditt företag om du inte redan gjort det.  Se [Skapa ett Microsoft-utvecklarkonto](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Klicka på **Define some Topologies to get Started** (Ange några topologier för att komma igång). En lösningsmall är "överordnad" alla dess topologier. Du kan definiera flera topologier i en erbjudande-/lösningsmall. När ett erbjudande pushas till mellanlagring pushas den med alla sina topologier. 
6. Ange ett topologinamn och klicka sedan på plustecknet.
7. Ange en ny version och klicka sedan på plustecknet.
8. Ladda upp zip-filen som skapades i [Paketera program](#package-application).  
9. Klicka på **Begär certifiering**. Microsofts certifieringsteam granskar filerna och certifierar topologin.

## <a name="next-steps"></a>Nästa steg
* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Läs mer om hur du installerar ett HDInsight-program till dina kluster.
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): information om hur du distribuerar ett opublicerat HDInsight-program till HDInsight.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Läs mer om hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.




<!--HONumber=Dec16_HO1-->


