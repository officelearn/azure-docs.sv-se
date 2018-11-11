---
title: Lär dig använda en Hadoop-sandbox - emulatorn – Azure HDInsight
description: 'Om du vill börja lära dig om hur du använder Hadoop-ekosystemet, kan du ställa in en Hadoop-sandbox från Hortonworks på virtuella Azure-datorer. '
keywords: hadoop-emulatorn, begränsat hadoop-läge
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: add1833671b995e747af6a447c65114d1add76e9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013113"
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Kom igång med ett begränsat Hadoop-läge, ett emuleringsprogram på en virtuell dator

Lär dig hur du installerar Hadoop-sandbox från Hortonworks på en virtuell dator för att lära dig om Hadoop-ekosystemet. Sandbox-miljön tillhandahåller en lokal utvecklingsmiljö för att lära dig om Hadoop, Hadoop Distributed File System (HDFS) och jobböverföring. När du är bekant med Hadoop kan börja du använda Hadoop på Azure genom att skapa ett HDInsight-kluster. Mer information om hur du kommer igång finns i [Kom igång med Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Förutsättningar
* [Oracle VirtualBox](https://www.virtualbox.org/). Hämta och installera det från [här](https://www.virtualbox.org/wiki/Downloads).



## <a name="download-and-install-the-virtual-machine"></a>Ladda ned och installera den virtuella datorn
1. Bläddra till den [Hortonworks hämtar](http://hortonworks.com/downloads/#sandbox).

2. Klicka på **hämta för VIRTUALBOX** att ladda ned Hortonworks sandbox-miljön på en virtuell dator. Du uppmanas att registrera med Hortonworks innan hämtningen påbörjas. Det tar en till två timmar att hämta beroende på nätverkets hastighet.
   
    ![Länk-avbildningen för ladda ned begränsat Hortonworks-läge för VirtualBox](./media/apache-hadoop-emulator-get-started/download-sandbox.png)
3. Samma Webb-sidan klickar du på den **Import på virtuella** länken för att hämta en PDF-fil som innehåller installationsanvisningar för den virtuella datorn.

Om du vill hämta en äldre version sandbox-miljön för HDP Expandera arkivet:

![Hortonworks Sandbox-Arkiv](./media/apache-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>Starta den virtuella datorn

1. Öppna Oracle VM VirtualBox.
2. Från den **filen** -menyn klickar du på **Import installation**, och sedan ange avbildningen som begränsat Hortonworks-läge.
1. Välj Hortonworks sandbox-miljön, klicka på **starta**, och sedan **Normal Start**. När den virtuella datorn har slutförts startprocessen visar instruktioner för inloggning.
   
    ![Normal start](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Öppna en webbläsare och gå till den URL som visas (vanligtvis http://127.0.0.1:8888).

## <a name="set-sandbox-passwords"></a>Ange Sandbox-lösenord

1. Från den **börjar** steg i Hortonworks Sandbox-sida väljer **visa avancerade alternativ**. Använd informationen på den här sidan för att logga in på sandbox-miljön med hjälp av SSH. Använd namnet och lösenordet som angavs.
   
   > [!NOTE]
   > Om du inte har en SSH-klient installerad kan du kan använda den webbaserade SSH som anges i av den virtuella datorn på **http://localhost:4200/**.
   > 
   
    Första gången du ansluter med hjälp av SSH uppmanas du att ändra lösenordet för rotkontot. Ange ett nytt lösenord som du använder när du loggar in med SSH.

2. När du loggat in kan du ange följande kommando:
   
        ambari-admin-password-reset
   
    När du uppmanas ange ett lösenord för administratörskonto Ambari. Det här används när du har åtkomst till Ambari-Webbgränssnittet.

## <a name="use-hive-commands"></a>Använda Hive-kommandon

1. Använd följande kommando för att starta Hive-gränssnittet från en SSH-anslutning till sandbox:
   
        hive
2. När gränssnittet har startats kan du använda följande för att visa vilka tabeller som tillhandahålls med sandbox-miljön:
   
        show tables;
3. Använd följande för att hämta 10 raderna från den `sample_07` tabell:
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du använder Visual Studio med begränsat Hortonworks-läge](../hdinsight-hadoop-emulator-visual-studio.md)
* [Lära dig grunderna för Hortonworks sandbox-miljön](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop-självstudiekurs – komma igång med HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

