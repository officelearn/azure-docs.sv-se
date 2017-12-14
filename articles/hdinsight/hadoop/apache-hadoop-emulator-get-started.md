---
title: "Lär dig använda Hadoop-sandbox - emulatorn – Azure HDInsight | Microsoft Docs"
description: "Om du vill börja lära dig om att använda Hadoop-ekosystemet, kan du ställa in en Hadoop sandbox från Hortonworks på en virtuell Azure-dator. "
keywords: hadoop-emulatorn hadoop sandbox
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: d7df18a80470beb8dc25cf6add6b7a61f45dcfe7
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Kom igång med Hadoop-sandbox, en emulator på en virtuell dator

Lär dig hur du installerar sandlådan Hadoop från Hortonworks på en virtuell dator om du vill veta mer om Hadoop-ekosystemet. Sandbox tillhandahåller en lokal utvecklingsmiljö mer information om Hadoop, Hadoop Distributed File System (HDFS) och skicka jobbet. När du är bekant med Hadoop, kan du börja använda Hadoop i Azure genom att skapa ett HDInsight-kluster. Mer information om hur du kommer igång finns [Kom igång med Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Krav
* [Oracle VirtualBox](https://www.virtualbox.org/). Hämta och installera den från [här](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Hämta och installera den virtuella datorn
1. Bläddra till den [Hortonworks hämtar](http://hortonworks.com/downloads/#sandbox).

2. Klicka på **hämta VIRTUALBOX** att ladda ned den senaste Hortonworks Sandbox på en virtuell dator. Du uppmanas att registrera med Hortonworks innan hämtningen påbörjas. Det tar en till två timmar att hämta beroende på nätverkets hastighet.
   
    ![Länka bild för att ladda ned Hortonworks Sandbox för VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Samma webbsida, klicka på den **Import på virtuella** länk för att hämta en PDF-fil som innehåller instruktioner för installation för den virtuella datorn.

Om du vill hämta en äldre HDP version sandbox Expandera arkivet:

![Hortonworks Sandbox-Arkiv](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Starta den virtuella datorn

1. Öppna Oracle VM VirtualBox.
2. Från den **filen** -menyn klickar du på **importera installation**, och sedan ange avbildningen som Hortonworks Sandbox.
1. Välj sandlådan Hortonworks, klicka på **starta**, och sedan **Normal Start**. När den virtuella datorn har slutförts startprocessen visar instruktioner för inloggning.
   
    ![Normal start](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Öppna en webbläsare och gå till den URL som visas (vanligtvis http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Ange Sandbox lösenord

1. Från den **Kom igång** steg på sidan Hortonworks Sandbox väljer **visa avancerade alternativ**. Använd informationen på den här sidan för att logga in till sandbox via SSH. Använd namn och lösenord angavs.
   
   > [!NOTE]
   > Om du inte har en SSH-klienten installerad kan du använda den webbaserade SSH som anges i av den virtuella datorn på **http://localhost:4200 /**.
   > 
   
    Första gången du ansluter med SSH, uppmanas du att ändra lösenordet för rotkontot. Ange ett nytt lösenord som du använder när du loggar in via SSH.

2. Efter loggat in kan du ange följande kommando:
   
        ambari-admin-password-reset
   
    När du uppmanas ange ett lösenord för administratörskontot Ambari. Det här används när du använder Ambari-Webbgränssnittet.

## <a name="use-hive-commands"></a>Använda Hive-kommandon

1. Från en SSH-anslutning till sandbox använder du följande kommando för att starta Hive-gränssnittet:
   
        hive
2. När gränssnittet har börjat använda följande för att visa tabeller som tillhandahålls med sandbox:
   
        show tables;
3. Använd följande för att hämta 10 rader från den `sample_07` tabellen:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du använder Visual Studio med Hortonworks Sandbox](../hdinsight-hadoop-emulator-visual-studio.md)
* [Learning linor av sandlådan Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop-vägledning - komma igång med HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

