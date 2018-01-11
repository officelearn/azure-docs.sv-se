---
title: "Konfigurera domänanslutna HDInsight - kluster i Azure | Microsoft Docs"
description: "Lär dig hur du skapar och konfigurerar domänanslutna HDInsight-kluster"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/15/2017
ms.author: saurinsh
ms.openlocfilehash: 0a9ed1cad8b8d4c566a0da16ac78d096efe187a5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>Konfigurera domänanslutna HDInsight begränsat läge

Lär dig hur du ställer in ett Azure HDInsight-kluster med fristående Active Directory och [Apache Ranger](http://hortonworks.com/apache/ranger/) och dra nytta av stark autentisering och omfattande rollbaserad åtkomst principer för åtkomstkontroll (RBAC). Mer information finns i [införa domänanslutna HDInsight-kluster](apache-domain-joined-introduction.md).

Utan domänanslutna HDInsight-kluster, kan varje kluster bara ha ett konto för Hadoop HTTP-användare och en SSH-användarkontot.  Autentisering av flera användare kan uppnås med hjälp av:

-   En fristående Active Directory körs på Azure IaaS
-   Azure Active Directory
-   Active Directory som körs på kundens lokala miljö.

Använder en fristående Active Directory ingår körs på Azure IaaS i den här artikeln. Det är den enklaste arkitektur som en kund kan följa för att få stöd för flera användare på HDInsight. 

> [!IMPORTANT]
> Oozie har inte aktiverats på domänanslutna HDInsight.

## <a name="prerequisite"></a>Krav
* Azure-prenumeration

## <a name="create-an-active-directory"></a>Skapa en Active Directory

Azure Resource Manager-mall gör det enklare att skapa Azure-resurser. I det här avsnittet kan du använda en [Azure QuickStart mallen](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/) att skapa en ny skog och domän med två virtuella datorer. Två virtuella datorer fungera som en primär domänkontrollant och en sekundär domänkontrollant.

**Så här skapar du en domän med två domänkontrollanter**

1. Klicka på följande bild för att öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Det ser ut som mallen:

    ![HDInsight domänanslutna skapa skogens domän virtuella datorer](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. Ange följande värden:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resursgruppens namn**: Ange ett Resursgruppsnamn.  En resursgrupp används för att hantera Azure-resurser som är relaterade till ett projekt.
    - **Plats**: Välj en Azure-plats som är nära dig.
    - **Administratörsanvändarnamnet**: Detta är administratörsanvändarnamnet domän. Den här användaren är inte HTTP-användarkontot för ditt HDInsight-kluster. Detta är det konto som används under kursen.
    - **Administratörslösenordet**: Ange lösenordet för domänadministratör.
    - **Domännamn**: domännamnet måste vara ett tvådelat namn. Till exempel: contoso.com, eller contoso.local eller hdinsight.test.
    - **DNS-prefix**: Ange ett DNS-prefix
    - **PDC-RDP-porten**: (använda standardvärdet för den här självstudiekursen)
    - **BDC RDP-porten**: (använda standardvärdet för den här självstudiekursen)
    - **artefakter plats**: (använda standardvärdet för den här självstudiekursen)
    - **artefakter plats SAS-token**: (lämna det tomt för den här självstudiekursen.)

Det tar ungefär 20 minuter för att skapa resurser.

## <a name="setup-ldaps"></a>Installationsprogrammet LDAPS

Lightweight Directory Access Protocol (LDAP) används för att läsa och skriva till AD.

**Att ansluta till den primära domänkontrollanten med hjälp av fjärrskrivbord**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna resursgruppen och öppna sedan den primära domänkontrollant (PDC) virtuella datorn. PDC-standardnamnet är adPDC. 
3. Klicka på **Anslut** att ansluta till den primära domänkontrollanten med hjälp av fjärrskrivbord.

    ![HDInsight domänanslutna ansluta PDC fjärrskrivbord](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Att lägga till Active Directory Certificate services**

4. Öppna **Serverhanteraren** om den inte är öppen.
5. Klicka på **hantera**, och klicka sedan på **Lägg till roller och funktioner**.

    ![HDInsight domänanslutna Lägg till roller och funktioner](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. Klicka på ”innan du börjar”, **nästa**.
6. Välj **rollbaserad eller funktionsbaserad installation**, och klicka sedan på **nästa**.
7. Välj den primära domänkontrollanten och klicka sedan på **nästa**.  PDC-standardnamnet är adPDC.
8. Välj **Active Directory Certificate Services**.
9. Klicka på **Lägg till funktioner** från dialogrutan popup-fönster.
10. Följ guiden använder standardinställningarna för resten av proceduren.
11. Stäng guiden genom att klicka på **Stäng**.

**Så här konfigurerar du AD-certifikat**

1. I Serverhanteraren, klicka på meddelandeikonen gul och klicka sedan på **konfigurera Active Directory Certificate services**.

    ![HDInsight domänanslutna konfigurerar AD-certifikat](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. Klicka på ** rolltjänster till vänster, Välj **certifikatutfärdare**, och klicka sedan på **nästa**.
3. Följ guiden använder standardinställningarna för resten av proceduren (klicka på **konfigurera** på det sista steget).
4. Stäng guiden genom att klicka på **Stäng**.

## <a name="optional-create-ad-users-and-groups"></a>(Valfritt) Skapa AD-användare och grupper

**Att skapa användare och grupper i AD**
1. Ansluta till den primära domänkontrollanten med hjälp av fjärrskrivbord
1. Öppna **Active Directory-användare och datorer**.
2. Välj ditt domännamn i det vänstra fönstret.
3. Klicka på den **skapa en ny användare i den aktuella behållaren** ikon på den översta menyn.

    ![HDInsight domänanslutna skapa användare](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. Följ instruktionerna för att skapa några användare. Till exempel hiveuser1 och hiveuser2.
5. Klicka på den **skapa en ny grupp i den aktuella behållaren** ikon på den översta menyn.
6. Följ instruktionerna för att skapa en grupp med namnet **HDInsightUsers**.  Den här gruppen används när du skapar ett HDInsight-kluster senare i den här kursen.

> [!IMPORTANT]
> PDC-virtuella datorn måste startas om innan du skapar en domänansluten HDInsight-kluster.

## <a name="create-an-hdinsight-cluster-in-the-vnet"></a>Skapa ett HDInsight-kluster i virtuella nätverk

I det här avsnittet använder du Azure-portalen för att lägga till ett HDInsight-kluster till det virtuella nätverket som du skapat med hjälp av Resource Manager-mallen tidigare i kursen. Den här artikeln beskriver bara specifik information om domänanslutna klusterkonfigurationen.  Allmän information, se [skapa Linux-baserade kluster i HDInsight med hjälp av Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md).  

**Så här skapar du en domänansluten HDInsight-kluster**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna den resursgrupp som du skapat med hjälp av Resource Manager-mallen tidigare i kursen.
3. Lägg till ett HDInsight-kluster i resursgruppen.
4. Välj **anpassad** alternativ:

    ![Skapa HDInsight domänanslutna anpassade alternativ](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    Det finns sex avsnitt med alternativet Anpassad konfiguration: Grunderna, lagring, program, klustret storlek, avancerade inställningar och sammanfattning.
5. I den **grunderna** avsnitt:

    - Kluster-typ: Välj **Enterprise säkerhetspaketet**. Enterprise säkerhetspaketet kan för närvarande bara aktiveras för följande typer av klustret: Hadoop, interaktiva fråge- och Spark.

        ![HDInsight domän domänanslutna enterprise säkerhetspaketet](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - Klustret inloggning användarnamn: Detta är den Hadoop HTTP-användaren. Det här kontot skiljer sig från domänadministratörskontot.
    - Resursgrupp: Markera den resursgrupp som du skapat tidigare med Resource Manager-mallen.
    - Plats: Platsen måste vara samma som du använde när du skapar vnet och domänkontrollanter med hjälp av Resource Manager-mall.

6. I den **avancerade inställningar** avsnitt:

    - Domäninställningar för:

        ![HDInsight domänanslutna avancerade inställningar domän](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - Domännamn: Ange det domännamn som du använde i [skapa en Active Directory](#create-an-active-directory).
        - Användarnamn för domänen: Ange det AD-administratörsanvändarnamn som du använde i [skapa en Active Directory](#create-an-active-directory).
        - Organisationsenhet: Se skärmbilden ett exempel.
        - LDAPS URL: Skärmbild för ett exempel finns
        - Åtkomst-användargrupp: Ange användarnamnet för gruppen som du skapade i [skapa AD-användare och grupper](#optionally-createad-users-and-groups)
    - Virtuellt nätverk: Välj det virtuella nätverk som du skapade i [skapa en Active Directory](#create-an-active-directory). Standardnamnet som används i mallen är **adVNET**.
    - Undernät: Standardnamnet som används i mallen är **adSubnet**.



När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Instruktioner för att ta bort ett kluster finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* För att ansluta till domänanslutna HDInsight-kluster med hjälp av SSH finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

