---
title: Installera publicerade program - Datameer - Azure HDInsight | Microsoft Docs
description: Installera och använda Hadoop-programmet Datameer från tredje part.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 5008056ae2274d058706649f286b91b71feadc27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401317"
---
# <a name="install-published-application---datameer"></a>Installera programmet - Datameer

Den här artikeln beskriver hur du installerar och kör den [Datameer](https://www.datameer.com/) publicerade Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattformen och en lista över tillgängliga oberoende leverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>Om Datameer

Datameer är det ursprungliga programmet för Hadoop-plattformen, utöka befintliga Azure HDInsight funktioner och ger snabb integration, förberedelse och analys av strukturerade och Ostrukturerade data. Datameer kan komma åt mer än 70 källor och format: strukturerade, delvis strukturerade och Ostrukturerade. Du kan direkt överföra data eller använda deras unika datalänkar som hämtar data på begäran. Datameers självbetjäning funktioner och bekant kalkylblad gränssnittet minskar den Stordata teknik och accelererar tid till insikter. Kalkylbladet gränssnitt tillhandahåller en enkel mekanism för att ange deklarativ formler som sedan kan identifieras översätts till optimerad Hadoop-jobb. Med Datameer business intelligence (BI) och dina Excel-kunskaper, kan du använda Hadoop i molnet snabbt. Mer information finns i [Datameer dokumentationen](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på en ny HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Klustret nivå: Standard
* Kluster-typ: Hadoop
* Kluster av version: 3.4

## <a name="install-the-datameer-published-application"></a>Installera Datameer publicerat program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program läsa [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Starta Datameer

1. Efter installationen kan du starta Datameer från ditt kluster i Azure-portalen genom att gå till den **inställningar** fönstret klicka **program** under den **allmänna** kategori . Den installerade appar rutan visas de installerade programmen.

    ![Installerade Datameer app](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. När du väljer Datameer visas en länk till webbsidan och sökväg för SSH-slutpunkt. Välj länken för WEBBSIDAN.

3. På första start finns två licensalternativ: antingen en 14-dagars utvärderingsversion, eller aktivera en befintlig licens.

    ![Licensalternativ](./media/hdinsight-apps-install-datameer/license.png)

4. När du har slutfört din valda licens alternativet visas med en inloggningsformulär. Ange standardautentiseringsuppgifterna visas innan inloggningsformuläret. När du loggar in, accepterar du programvaruavtal att fortsätta.

    ![Inloggning](./media/hdinsight-apps-install-datameer/login.png)

Följande steg visar en demonstration ”Hello World”.

1. [Hämta exempel CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Klicka på den **+** loggar ovanpå Datameer instrumentpanelen och klickar på **filöverföring**.

    ![Filuppladdning](./media/hdinsight-apps-install-datameer/upload.png)

3. I dialogrutan överför Bläddra och välj den **Hello World.csv** fil som du hämtat. Kontrollera att den **filtyp** är inställd på CSV / TVS. Välj **Nästa**. Klicka **nästa** tills du når slutet av guiden.

    ![Filuppladdning](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Namn på filen **Hello World** under en ny mapp. Byt namn på den nya mappen som ”Demo”. Välj **Spara**.

    ![Spara](./media/hdinsight-apps-install-datameer/save.png)

5. Klicka på den **+** logga in en gång och välja **arbetsboken** att skapa en ny arbetsbok för data.

    ![Lägg till arbetsboken](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Expandera den **Data** mappen **filöverföringar**, sedan **Demo** mapp som du skapade när du sparar filen ”Hello World”. Välj **Hello World** formuläret lista över filer och klicka sedan på **lägga till Data**.

    ![Spara](./media/hdinsight-apps-install-datameer/select-file.png)

7. Du ser de data som läses in i ett kalkylblad-gränssnitt. Om du vill välja en delmängd av data i **Filter** i verktygsfältet.

    ![Knappen Filtrera](./media/hdinsight-apps-install-datameer/filter-button.png)

8. I dialogrutan Använd Filter väljer du den **Stad** kolumnen **är lika med** operatorn och skriv **Chicago** i filter-textrutan. Kontrollera den **skapa filter i det nya bladet** och välj sedan **skapa Filter**.

    ![Använda Filter](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Spara arbetsboken genom att klicka på **filen**, sedan **spara**. Ange ett namn, till exempel ”Hello World arbetsboken”.

10. Visas med alternativ för hur och när du ska köra arbetsboken. Nu lämnar du alla alternativ till sina standardvärden och markera den **Start beräkning processen omedelbart efter spara**, och välj **spara**.

    ![Spara arbetsboken](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer innehåller kraftfulla visualiseringsverktyg. Skapa en Infographic för att visa data. Välj den **+** logga ovanpå instrumentpanelen och välj sedan **Infographic**.

    ![Lägg till Infographic](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Dra en widget stapeldiagram från listan över widgetar till vänster, som visas i steg 1 i följande diagram. Därefter navigera i mappen Data under data webbläsaren till höger, expandera din arbetsbok och sedan kalkylbladet du har lagt till med filter (steg 2). Dra den **namn** kolumnen över stapeldiagrammet och släpp i den **etikett** mål för att ange den arbetsboken namnkolumnen som diagrammets Etikettfält (steg 3).

    ![Informationsgrafik](./media/hdinsight-apps-install-datameer/infographic.png)

13. Om du vill ange ålder som diagrammets Y-axeln, drar den **ålder** kolumn till diagrammets **Data** fältet.

    ![Informationsgrafik](./media/hdinsight-apps-install-datameer/infographic-age.png)

Grattis! Du har skapat en visualisering av data utan att skriva någon kod. Du kan nu utforska variationer och ytterligare grafik.

## <a name="next-steps"></a>Nästa steg

* [Datameer dokumentationen](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserat HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använd tom edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom kantnod för åtkomst till HDInsight-kluster och för att testa och värd för HDInsight-program.
