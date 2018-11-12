---
title: Installera publicerad program – Datameer - Azure HDInsight
description: Installera och använda Apache Hadoop-program Datameer från tredje part.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 97d99aa59c490cf2dcdd4a69f32411a051942d36
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037812"
---
# <a name="install-published-application---datameer"></a>Installera publicerad program – Datameer

Den här artikeln beskrivs hur du installerar och kör den [Datameer](https://www.datameer.com/) publicerade Apache Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattform och en lista över tillgängliga oberoende programvaruleverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>Om Datameer

Datameer är ett internt program för Hadoop-plattformen, utöka befintliga funktioner i Azure HDInsight och ger snabb integrering, förberedelse och analys av strukturerade och Ostrukturerade data. Datameer har åtkomst till fler än 70 olika källor och format: strukturerade, halvstrukturerade och Ostrukturerade. Du kan direkt överföra data eller använda deras unika data-länkar för att hämta data på begäran. Datameers självbetjäningsfunktioner och välbekanta kalkylbladsgränssnitt minskar komplexiteten med Big Data-teknik och accelererar tiden till insikt. Kalkylblad-gränssnittet tillhandahåller en enkel mekanism för att ange deklarativa formler som kan sedan identifieras översätts till optimerade Hadoop-jobb. Med Datameer och dina business intelligence (BI) och Excel-kunskaper kan använda du Hadoop i molnet snabbt. Mer information finns i den [Datameer dokumentation](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på ett nytt HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Kluster-nivå: Standard
* Typ av kluster: Hadoop
* Klusterversion: 3.4

## <a name="install-the-datameer-published-application"></a>Installera Datameer publicerad program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program finns [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Starta Datameer

1. Efter installationen kan du starta Datameer från ditt kluster i Azure-portalen genom att gå till den **inställningar** och sedan klicka på **program** under den **Allmänt** kategori . Fönstret installerade appar visar en lista över de installerade programmen.

    ![Installerade Datameer app](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. När du väljer Datameer, visas en länk till sidan och sökvägen för SSH-slutpunkten. Välj länken för WEBBSIDAN.

3. Det finns två licensalternativ vid första start: antingen en kostnadsfri 14 dagars utvärderingsversion, eller aktivera en befintlig licens.

    ![Licensalternativ](./media/hdinsight-apps-install-datameer/license.png)

4. När du har slutfört ditt valda Licensgrupperna alternativ visas med ett inloggningsformulär. Ange standardautentiseringsuppgifterna som visas innan inloggningsformuläret. Acceptera programvaruavtal fortsätta efter inloggningen.

    ![Inloggning](./media/hdinsight-apps-install-datameer/login.png)

Följande steg visar en ”Hello World”-demonstration.

1. [Ladda ned exemplet CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Klicka på den **+** loggar ovanpå Datameer instrumentpanelen och klickar på **Filuppladdning**.

    ![Filuppladdning](./media/hdinsight-apps-install-datameer/upload.png)

3. I dialogrutan för filöverföring Bläddra och välj den **Hello World.csv** fil som du hämtade. Kontrollera att den **filtyp** har angetts till CSV / TSV. Välj **Nästa**. Fortsätt att klicka på **nästa** tills du når slutet av guiden.

    ![Filuppladdning](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Ge filen namnet **Hello World** under en ny mapp. Byt namn på den nya mappen som ”Demo”. Välj **Spara**.

    ![Spara](./media/hdinsight-apps-install-datameer/save.png)

5. Klicka på den **+** en gång till och markera **arbetsboken** att skapa en ny arbetsbok för data.

    ![Lägg till arbetsbok](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Expandera den **Data** mappen **filöverföringar**, kommer **Demo** mapp som du skapade när du sparar filen ”Hello World”. Välj **Hello World** utgör listan över filer och klicka sedan på **lägga till Data**.

    ![Spara](./media/hdinsight-apps-install-datameer/select-file.png)

7. Du se de data som läses in i ett kalkylbladsgränssnitt. För att välja en delmängd av data, Välj den **Filter** i verktygsfältet.

    ![Knappen Filtrera](./media/hdinsight-apps-install-datameer/filter-button.png)

8. I dialogrutan tillämpa Filter väljer du den **Stad** kolumnen **är lika med** operator och skriv **Chicago** i textrutan filter. Kontrollera den **skapa filter i ett nytt blad** kryssrutan, välj sedan **skapa Filter**.

    ![Använd Filter](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Spara arbetsboken genom att klicka på **filen**, sedan **spara**. Ange ett namn, till exempel ”Hello World-arbetsbok”.

10. Visas med alternativ för hur och när ska köras i arbetsboken. För tillfället, lämnar du alla alternativ på sina standardvärden och markera den **Start beräkning processen omedelbart efter spara**, och välj **spara**.

    ![Spara arbetsboken](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer innehåller verktyg för kraftfull visualisering. Skapa en Informationsgrafik om du vill visa data. Välj den **+** logga på instrumentpanelen och välj sedan **Informationsgrafiken**.

    ![Lägg till Informationsgrafiken](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Dra en stapeldiagram-widget i listan med widgetar till vänster som visas i steg 1 i följande diagram. Därefter navigerar i mappen Data under data webbläsaren till höger, expandera din arbetsbok, och sedan kalkylbladet du lade till med filter (steg 2). Dra den **namn** kolumnen över stapeldiagram och släpp i den **etikett** mål att ställa in arbetsbokens namn på kolumnen som diagrammets Etikettfält (steg 3).

    ![Informationsgrafik](./media/hdinsight-apps-install-datameer/infographic.png)

13. Ange ålder som diagrammets Y-axeln genom att dra den **ålder** kolumn i diagrammets **Data** fält.

    ![Informationsgrafik](./media/hdinsight-apps-install-datameer/infographic-age.png)

Grattis! Du har skapat en visualisering av dina data utan att behöva skriva någon kod. Nu kan du utforska varianter och fler visualiseringar.

## <a name="next-steps"></a>Nästa steg

* [Datameer dokumentation](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använda tomma kantnoder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom edge-nod för att komma åt HDInsight-kluster och för testning och som är värd för HDInsight-program.
