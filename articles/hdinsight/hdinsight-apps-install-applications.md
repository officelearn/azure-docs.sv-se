---
title: Installera Hadoop-program på HDInsight | Microsoft Docs
description: Ta reda på hur du installerar HDInsight-program på HDInsight-appar.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/14/2016
ms.author: jgao

---
# Installera HDInsight-program
Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv. Den här artikeln beskriver hur du installerar ett publicerat program. Information om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md). 

För närvarande finns det ett publicerat program:

* **Datameer**: [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) erbjuder analytiker ett interaktivt sätt att identifiera, analysera och visualisera resultat från stordata (Big Data). Hämta enkelt in fler datakällor för att identifiera nya relationer och snabbt få de svar du behöver.

> [!NOTE]
> Datameer stöds för närvarande endast i kluster med Azure HDInsight version 3.2.
> 
> 

I artikelns instruktioner används Azure-portalen. Du kan också exportera Azure Resource Manager-mallen från portalen eller skaffa en kopia av Resource Manager-mallen från leverantörer och distribuera mallen med Azure PowerShell och Azure CLI.  Mer information finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## Krav
Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## Installera program i befintliga kluster
I följande procedur beskriver vi hur du installerar HDInsight-program i ett befintligt HDInsight-kluster.

**Så här installerar du ett HDInsight-program**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Bläddra** och sedan på **HDInsight-kluster**.
3. Klicka på ett HDInsight-kluster.  Om du inte har något måste du skapa ett först.  Mer information finns i [Skapa kluster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. I bladet **Inställningar** klickar du på **Program** under kategorin **Allmänt**. På bladet **Installerade appar** visas alla installerade program. 
   
    ![menyn för hdinsight-program på portalen](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Klicka på **Lägg till** från bladets meny. 
   
    ![hdinsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Du bör se en lista över befintliga HDInsight-program.
   
    ![hdinsight-program – tillgängliga program](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Klicka på ett av programmen, godkänn de juridiska villkoren och klicka sedan på **Välj**.

Du kan se installationsstatusen i meddelandena på portalen (klicka på klockikonen överst på portalen). När programmet har installerats visas det på bladet Installerade appar.

## Installera program när du skapar ett kluster
Du kan välja att installera HDInsight-program när du skapar ett kluster. Under processen installeras HDInsight-program när klustret har skapats och är i körläge. Följande steg beskriver hur du installerar HDInsight-program när du skapar ett kluster.

**Så här installerar du ett HDInsight-program**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Nytt**, klicka på **Data + Analys** och sedan på **HDInsight**.
3. Ange **klusternamnet**: Det här namnet måste vara globalt unikt.
4. Klicka på **Prenumeration** och välj den Azure-prenumeration som du vill använda för klustret.
5. Klicka på **Välj typ av kluster** och välj sedan:
   
   * **Klustertyp**: Om du inte vet vad du ska välja väljer du **Hadoop**. Det är den mest populära klustertypen.
   * **Operativsystem**: Välj **Linux**.
   * **Version**: Använd standardversionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).
   * **Klusternivå**: Azure HDInsight tillhandahåller molntjänster för stordata i två kategorier: Premium- och standardnivå. Mer information finns i [Klusternivåer](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Klicka på **Program**, klicka på något av de publicerade programmen och sedan på **Välj**.
7. Klicka på **Autentiseringsuppgifter** och ange ett lösenord för administratörsanvändaren. Du måste också ange ett **SSH-användarnamn** och antingen ett **lösenord** eller en **offentliga nyckel**, som används för att autentisera SSH-användaren. Den rekommenderade metoden är att använda en offentlig nyckel. Spara konfigurationen av autentiseringsuppgifterna genom att klicka på **Välj** längst ned.
8. Klicka på **Datakälla**, välj ett befintligt lagringskonto eller skapa ett nytt lagringskonto som ska användas som standardlagringskonto för klustret.
9. Klicka på **Resursgrupp** om du vill välja en befintlig resursgrupp eller på **Nytt** om du vill skapa en ny resursgrupp.
10. På bladet **Nytt HDInsight-kluster** kontrollerar du att **Fäst på startsidan** är valt och klickar sedan på **Skapa**. 

## Visa en lista med installerade HDInsight-appar och deras egenskaper
På portalen kan du visa en lista över de installerade HDInsight-programmen för ett kluster och egenskaperna för varje installerat program.

**Så här visar du en lista med HDInsight-program och deras egenskaper**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.  Om du inte ser det klickar du på **Bläddra** och sedan på **HDInsight-kluster**.
3. Klicka på ett HDInsight-kluster.
4. I bladet **Inställningar** klickar du på **Program** under kategorin **Allmänt**. På bladet Installerade appar visas alla installerade program. 
   
    ![hdinsight-program – installerade appar](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Klicka på något av de installerade programmen för att visa egenskaperna. Egenskapsbladet innehåller:
   
   * Programnamn: programnamnet.
   * Status: programmets status. 
   * Webbsida: URL:en till den webbapp som du har distribuerat till gränsnoden om en sådan finns. Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret.
   * HTTP-slutpunkt: Autentiseringsuppgifterna är samma som HTTP-autentiseringsuppgifterna som du har konfigurerat för klustret. 
   * SSH-slutpunkt: Du kan använda [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till gränsnoden. SSH-autentiseringsuppgifterna är samma som SSH-autentiseringsuppgifterna som du har konfigurerat för klustret.
6. Om du vill ta bort ett program högerklickar du på programmet och klickar sedan på **Ta bort** på snabbmenyn.

## Ansluta till gränsnoden
Du kan ansluta till gränsnoden med HTTP och SSH. Du kan hitta information om slutpunkten på [portalen](#list-installed-hdinsight-apps-and-properties). Mer information om hur du använder SSH finns i [Använda SSH med Linux-baserad Hadoop i Linux, Unix eller OS X HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). 

Autentiseringsuppgifterna för HTTP-slutpunkten är HTTP-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret. Autentiseringsuppgifterna för SSH-slutpunkten är SSH-autentiseringsuppgifterna som du har konfigurerat för HDInsight-klustret.

## Felsöka
Läs [Felsöka installationen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## Nästa steg
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): information om hur du distribuerar ett opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.

<!--HONumber=Sep16_HO3-->


