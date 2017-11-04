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
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: af75d63caca24f389345c964e2dc506a255bec19
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Konfigurera domänanslutna HDInsight-kluster (förhandsgranskning)

Lär dig hur du ställer in ett Azure HDInsight-kluster med Azure Active Directory (Azure AD) och [Apache Ranger](http://hortonworks.com/apache/ranger/) och dra nytta av stark autentisering och omfattande rollbaserad åtkomst principer för åtkomstkontroll (RBAC).  Domänanslutna HDInsight kan bara konfigureras på Linux-baserade kluster. Mer information finns i [införa domänanslutna HDInsight-kluster](apache-domain-joined-introduction.md).

> [!IMPORTANT]
> Oozie har inte aktiverats på domänanslutna HDInsight.

Den här artikeln är en serie första kursen:

* Skapa ett HDInsight-kluster som är anslutna till Azure AD (via funktionen för Azure Directory Domain Services) med Apache Ranger aktiverad.
* Skapa och tillämpa principer för Hive via Apache Ranger och ge användare (till exempel datavetare) kan ansluta till registreringsdatafilen med hjälp av ODBC-baserade verktyg, till exempel Excel, Tableau osv. Microsoft arbetar med att lägga till andra arbetsbelastningar, t.ex HBase, Spark och Storm, domänanslutna HDInsight snart.

Ett exempel på den sista topologin ser ut som följer:

![Domänanslutna HDInsight-topologi](./media/apache-domain-joined-configure/hdinsight-domain-joined-topology.png)

Eftersom Azure AD för närvarande stöder endast klassiska virtuella nätverk (Vnet) och Linux-baserade HDInsight-kluster endast stöder Azure Resource Manager baserade Vnet, HDInsight Azure AD-integration kräver två Vnet och en peering mellan dem. Jämförelse-information mellan de två distributionsmodellerna finns [Azure Resource Manager och klassisk distribution: Förstå distributionsmodeller och tillståndet för dina resurser](../../azure-resource-manager/resource-manager-deployment-model.md). Två Vnet måste vara i samma region som Azure AD DS.

Azure-Tjänstenamn måste vara globalt unika. Följande namn används i den här kursen. Contoso är ett fiktivt namn. Du måste ersätta *contoso* med ett annat namn när du går igenom kursen. 

**Namn:**

| Egenskap | Värde |
| --- | --- |
| Azure AD-VNet |contosoaadvnet |
| Azure AD-Vnet-resursgrupp |contosoaadrg |
| Azure AD-katalog |contosoaaddirectory |
| Azure AD-domännamn |Contoso (contoso.onmicrosoft.com) |
| HDInsight-VNet |contosohdivnet |
| HDInsight VNet resursgruppen. |contosohdirg |
| HDInsight-kluster |contosohdicluster |

Den här självstudiekursen innehåller steg för att konfigurera en domänansluten HDInsight-kluster. Varje avsnitt innehåller länkar till andra artiklar med mer information.

## <a name="prerequisite"></a>Förutsättning:
* Bekanta dig med [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) dess [priser](https://azure.microsoft.com/pricing/details/active-directory-ds/) struktur.
* Se till att prenumerationen är godkända för den här public preview. Du kan göra det genom att skicka ett e-postmeddelande till hdipreview@microsoft.com med ditt prenumerations-ID.
* Ett SSL-certifikat som signerats av en utfärdare för signering för din domän. Certifikatet som krävs genom att konfigurera säker LDAP. Självsignerade certifikat kan inte användas.

## <a name="procedures"></a>Procedurer
1. Skapa ett Azure klassiska virtuella nätverk för din Azure AD.  
2. Skapa och konfigurera Azure AD och Azure AD DS.
3. Skapa ett HDInsight-VNet i Azure-resurs management-läge.
4. Peer-två Vnet.
5. Skapa ett HDInsight-kluster.

> [!NOTE]
> Den här kursen förutsätter att du inte har en Azure AD. Om du har ett kan du hoppa över delen i steg 2.
> 
> 

## <a name="create-an-azure-virtual-network-classic"></a>Skapa ett Azure-nätverk (klassisk)
I det här avsnittet skapar du ett virtuellt nätverk (klassiskt) med Azure-portalen. I nästa avsnitt aktivera Azure AD DS för din Azure AD i det virtuella nätverket. Mer information om följande procedur och använda andra metoder för att skapa virtuella nätverk finns [skapa ett virtuellt nätverk (klassiskt) med hjälp av Azure portal](../../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

**Skapa ett klassiska VNet**

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Klicka på **nya** > **nätverk** > **virtuellt nätverk**.
3. I **Välj en distributionsmodell**väljer **klassiska**, och klicka sedan på **skapa**.
4. Ange eller välj följande värden:
   
   * **Namn på**: contosoaadvnet
   * **Adressutrymmet**: 10.1.0.0/16
   * **Undernätnamnet**: Undernät1
   * **Adressintervall för gatewayundernät**: 10.1.0.0/24
   * **Prenumerationen**: (Välj en prenumeration som används för att skapa detta virtuella nätverk).
   * **ResourceGroup**: contosoaadrg
   * **Plats**: (Välj en region för din HDInsight-kluster).
     
     > [!IMPORTANT]
     > Du måste välja en plats som stöder Azure AD DS. Mer information finns i [Produkttillgänglighet per region](https://azure.microsoft.com/en-us/regions/services/). 
     > 
     > Både klassiska VNet och grupp-VNet resursen måste vara i samma region som Azure AD DS.
     > 
     > 
5. Skapa VNet genom att klicka på **Skapa**.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Skapa och konfigurera Azure AD DS för din Azure AD
I det här avsnittet kommer du att:

1. Skapa en Azure AD.
2. Skapa Azure AD-användare. Dessa användare är domänanvändare. Du kan använda den första användaren för att konfigurera HDInsight-kluster med Azure AD.  De två användarna är valfria för den här självstudiekursen. De kommer att användas i [konfigurera Hive principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md) när du konfigurerar Apache Ranger principer.
3. Skapa AAD DC administratörsgruppen och Lägg till Azure AD-användare i gruppen. Du kan använda den här användaren för att skapa organisationsenheten.
4. Aktivera Azure AD Domain Services (Azure AD DS) för Azure AD.
5. Konfigurera LDAPS för Azure AD. Lightweight Directory Access Protocol (LDAP) används för att läsa och skriva till Azure AD.

Om du föredrar att använda en befintlig Azure AD kan du hoppa över steg 1 och 2.

**Skapa en Azure AD**

1. Från den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar du på **ny** > **Apptjänster** > **Active Directory** > **Directory** > **skapa anpassade**. 
2. Ange eller välj följande värden:
   
   * **Namn på**: contosoaaddirectory
   * **Domännamn**: contoso.  Det här namnet måste vara globalt unika.
   * **Land eller region**: Välj land eller region.
3. Klicka på **Complete** (Slutför).

**Skapa en Azure AD-användare**

1. Från den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar du på **Active Directory** -> **contosoaaddirectory**. 
2. Klicka på **användare** på den översta menyn.
3. Klicka på **lägga till användare**.
4. Ange **användarnamn**, och klicka sedan på **nästa**. 
5. Konfigurera användarprofiler; I **rollen**väljer **Global administratör**; och klicka sedan på **nästa**.  Rollen Global administratör krävs för att skapa organisationsenheter.
6. Klicka på **skapa** få ett tillfälligt lösenord.
7. Skapa en kopia av lösenordet och klicka sedan på **Slutför**. Senare i den här kursen använder användaren global administratör för att skapa HDInsight-klustret.

Följ samma steg för att skapa två fler användare med den **användaren** roll, hiveuser1 och hiveuser2. Följande användare kommer att användas i [konfigurera Hive principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).

**Skapar AAD DC administratörernas gruppen och lägger till en Azure AD-användare**

1. Från den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar du på **Active Directory** > **contosoaaddirectory**. 
2. Klicka på **grupper** på den översta menyn.
3. Klicka på **lägga till en grupp** eller **Lägg till grupp**.
4. Ange eller välj följande värden:
   
   * **Namn på**: AAD DC-administratörer.  Ändra gruppnamnet inte.
   * **Grupptyp**: säkerhet.
5. Klicka på **Complete** (Slutför).
6. Klicka på **AAD DC administratörer** att öppna gruppen.
7. Klicka på **lägga till medlemmar**.
8. Välj den första användare som du skapade i föregående steg och klicka sedan på **Slutför**.
9. Upprepa samma steg för att skapa en annan grupp med namnet **HiveUsers**, och lägga till de två Hive-användarna i gruppen.

Mer information finns i [Azure AD Domain Services (förhandsvisning) – skapa gruppen ”AAD DC-administratörer”](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Att aktivera Azure AD DS för din Azure AD**

1. Från den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar du på **Active Directory** > **contosoaaddirectory**. 
2. Klicka på **konfigurera** på den översta menyn.
3. Rulla ned till **Domain Services**, och ange följande värden:
   
   * **Aktivera domain services för den här katalogen**: Ja.
   * **DNS-domännamnet för domäntjänster**: här visas standard DNS-namnet på Azure-katalogen. Till exempel contoso.onmicrosoft.com.
   * **Anslut domäntjänster till det här virtuella nätverket**: Välj det klassiska virtuella nätverk som du skapade tidigare, d.v.s. **contosoaadvnet**.
4. Klicka på **spara** längst ned på sidan. Du kommer att se **väntande...**  bredvid **aktivera domain services för den här katalogen**.  
5. Vänta tills **väntande...**  försvinner och **IP-adress** hämtar fylls i. Två IP-adresser ska hämta fylls i. Dessa är de IP-adresserna för de domänkontrollanter som etablerats av Domain Services. Varje IP-adress kommer att vara synliga när motsvarande domänkontrollanten är allokerade och redo. Skriv ned två IP-adresser. Du behöver dem senare.

Mer information finns i [aktivera Azure Active Directory Domain Services med Azure-portalen](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Att synkronisera lösenord**

Om du använder en egen domän måste du synkronisera lösenordet. Se [aktivera Lösenordssynkronisering till Azure AD domain services för en molnbaserad Azure AD-katalog](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**Konfigurera LDAPS för Azure AD**

1. Hämta ett SSL-certifikat som signerats av en utfärdare för signering för din domän. Om du vill använda ett självsignerat certifikat kan du nå hdipreview@microsoft.com för ett undantag.
2. Från den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar du på **Active Directory** > **contosoaaddirectory**. 
3. Klicka på **konfigurera** på den översta menyn.
4. Bläddra till **domäntjänster**.
5. Klicka på **konfigurera certifikat**.
6. Följ anvisningarna för att ange certifikatfilen och lösenordet. Du kommer att se **väntande...**  bredvid **aktivera domain services för den här katalogen**.  
7. Vänta tills **väntande...**  försvinner och **säker LDAP-certifikat** har fyllts i.  Detta kan ta 10 minuter eller mer.

> [!NOTE]
> Om vissa bakgrundsaktiviteter körs på Azure AD DS, visas ett fel när du laddar upp certifikatet - <i>det finns en åtgärd som utförs för den här klienten. Försök igen senare</i>.  Om det här felet uppstår. Försök igen senare. Den andra domänkontrollantens IP kan ta upp till 3 timmar att etableras.
> 
> 

Mer information finns i [konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Skapa ett VNet för Resource Manager för HDInsight-kluster
I det här avsnittet skapar du en Azure Resource Manager-VNet som ska användas för HDInsight-kluster. Mer information om hur du skapar Azure VNET med andra metoder finns [skapa ett virtuellt nätverk](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

När du har skapat VNet, konfigurerar du VNet Resource Manager om du vill använda samma DNS-servrar för Azure AD-VNet. Om du har följt stegen i den här kursen hjälper dig att skapa klassiska virtuella nätverk och Azure AD, är DNS-servrar 10.1.0.4 och 10.1.0.5.

**Skapa ett VNet med Resource Manager**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **ny**, **nätverk**, och sedan **för virtuella nätverk**. 
3. I **Välj en distributionsmodell**väljer **Resource Manager**, och klicka sedan på **skapa**.
4. Ange eller välj följande värden:
   
   * **Namn på**: contosohdivnet
   * **Adressutrymmet**: 10.2.0.0/16. Kontrollera att adressintervallet inte får överlappa med IP-adressintervall för klassiska VNet.
   * **Undernätnamnet**: Undernät1
   * **Adressintervall för gatewayundernät**: 10.2.0.0/24
   * **Prenumerationen**: (Välj din Azure-prenumeration.)
   * **Resursgruppen**: contosohdirg
   * **Plats**: (Välj samma plats som Azure AD VNet, d.v.s. contosoaadvnet.)
5. Klicka på **Skapa**.

**Konfigurera DNS för Resource Manager-VNet**

1. Från den [Azure-portalen](https://portal.azure.com), klickar du på **fler tjänster** -> **virtuella nätverken**. Se till att inte klicka på **virtuella nätverk (klassiskt)**.
2. Klicka på **contosohdivnet**.
3. Klicka på **DNS-servrar** från vänster sida av det nya bladet.
4. Klicka på **anpassade**, och ange följande värden:
   
   * 10.1.0.4
   * 10.1.0.5
     
     Dessa IP-adresser för DNS-servrar måste matcha DNS-servrar i Azure AD virtuella nätverk (klassiska VNet).
5. Klicka på **Spara**.

## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Peer-Azure AD-VNet och HDInsight-VNet
**Att peer-koppla två virtuella nätverk**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **fler tjänster** i den vänstra menyn.
3. Klicka på **virtuella nätverk**. Klicka inte på **virtuella nätverk (klassiskt)**.
4. Klicka på **contosohdivnet**.  Detta är HDInsight VNet.
5. Klicka på **Peerkopplingar** på den vänstra menyn på bladet.
6. Klicka på **Lägg till** på den översta menyn. Öppnar den **lägga till peering** bladet.
7. På den **lägga till peering** bladet ange eller Välj följande värden:
   
   * **Namn på**: ContosoAADHDIVNetPeering
   * **Virtuellt nätverk distributionsmodell**: klassiska
   * **Prenumerationen**: Välj din prenumerationsnamn som används för vnet klassisk (Azure AD).
   * **Virtuellt nätverk**: contosoaadvnet.
   * **Tillåt åtkomst till virtuella nätverk**: (kontrollera)
   * **Tillåt vidarebefordrad trafik**: (kontrollera). Lämna de andra två kryssrutorna omarkerad.
8. Klicka på **OK**.

## <a name="create-hdinsight-cluster"></a>Skapa HDInsight-kluster
I det här avsnittet skapar du ett Linux-baserade Hadoop-kluster i HDInsight med antingen Azure-portalen eller [Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md). För andra metoder för att skapa kluster och förstå inställningarna, se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder Resource Manager-mall för att skapa Hadoop-kluster i HDInsight finns [skapa Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](../hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Så här skapar du en domänansluten HDInsight-kluster med hjälp av Azure portal**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **ny**, **Intelligence + analys**, och sedan **HDInsight**.
3. Från den **nya HDInsight-kluster** bladet ange eller Välj följande värden:
   
   * **Klusternamnet**: Ange ett nytt namn för klustret för domänanslutna HDInsight-kluster.
   * **Prenumerationen**: Välj en Azure-prenumeration används för att skapa det här klustret.
   * **Klusterkonfiguration**:
     
     * **Klustret typen**: Hadoop. Domänanslutna HDInsight är för närvarande endast stöds på Hadoop-kluster.
     * **Operativsystemet**: Linux.  Domänanslutna HDInsight stöds bara på Linux-baserade HDInsight-kluster.
     * **Version**: HDI 3,6. Domänanslutna HDInsight stöds bara på HDInsight-kluster av version 3,6.
     * **Klustret typen**: PREMIUM
       
       Klicka på **Välj** spara ändringarna.
   * **Autentiseringsuppgifter**: Konfigurera autentiseringsuppgifterna för både klustret användar- och SSH-användare.
   * **Datakällan**: skapa ett nytt lagringskonto eller använda ett befintligt lagringskonto som standardkontot för lagring för HDInsight-klustret. Platsen måste vara samma som de två Vnet.  Platsen är också platsen för HDInsight-klustret.
   * **Priser**: Välj antalet arbetarnoder i klustret.
   * **Avancerade konfigurationer**: 
     
     * **Domänanslutning & Vnet /-undernätet**: 
       
       * **Domäninställningar**: 
         
         * **Domännamn**: contoso.onmicrosoft.com
         * **Namnet på användaren**: Ange ett användarnamn för domänen. Den här domänen måste ha följande behörigheter: ansluta datorer till domänen och placerar dem på den organisationsenhet som du anger när klustret skapas; Skapa tjänstens huvudnamn i organisationsenheten som du anger när klustret skapas; Skapa omvänd DNS-poster. Den här domänanvändare blir administratören för den här datorn är domänansluten HDInsight-kluster.
         * **Domänlösenord**: Ange domänen användarens lösenord.
         * **Organisationsenheten**: Ange det unika namnet på den Organisationsenhet som du vill använda med HDInsight-kluster. Till exempel: OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com. Om Organisationsenheten inte finns, ska HDInsight-kluster försöka skapa Organisationsenheten. Kontrollera att OU: N finns redan eller domänkontot har behörighet att skapa en ny. Om du använder det domänkonto som ingår i AADDC administratörer får behörighet att skapa en Organisationsenhet.
         * **LDAPS URL**: ldaps://contoso.onmicrosoft.com:636
         * **Åtkomst-användargrupp**: Ange den säkerhetsgrupp vars användare som du vill synkronisera till klustret. Till exempel HiveUsers.
           
           Klicka på **Välj** spara ändringarna.
           
           ![Domänanslutna HDInsight portal konfigurera domäninställningen](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Virtuellt nätverk**: contosohdivnet
       * **Undernät**: Undernät1
         
         Klicka på **Välj** spara ändringarna.        
         Klicka på **Välj** spara ändringarna.
   * **Resursgruppen**: Markera den resursgrupp som används för HDInsight-VNet (contosohdirg).
4. Klicka på **Skapa**.  

Ett annat alternativ för att skapa domänanslutna HDInsight-kluster är att använda Azure Resource Manager-mall. Följande procedur visar hur du gör:

**Så här skapar du en domänansluten HDInsight-kluster med hjälp av en Resource Manager-mall**

1. Klicka på följande bild för att öppna en Resource Manager-mall i Azure-portalen. Resource Manager-mallen finns i en offentlig blob-behållare. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Från den **parametrar** bladet, ange följande värden:
   
   * **Prenumerationen**: (Välj din Azure-prenumeration).
   * **Resursgruppen**: Klicka på **använda befintliga**, och ange samma resursgrupp som du har använt.  Till exempel contosohdirg. 
   * **Plats**: Ange en resursgruppens plats.
   * **Klusternamn**: Ange ett namn för det Hadoop-kluster du vill skapa. Till exempel contosohdicluster.
   * **Klustret typen**: Välj en typ av kluster.  Standardvärdet är **hadoop**.
   * **Plats**: Välj en plats för klustret.  Standardkontot för lagring använder samma plats.
   * **Klustret Arbetsnoden antal**: Välj antalet arbetarnoder.
   * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard **admin**.
   * **SSH-användarnamn och lösenord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn. 
   * **Virtuella nätverks-Id**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >
   * **Virtuella undernät**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >/undernät/Undernät1
   * **Domännamn**: contoso.onmicrosoft.com
   * **Organisation enhet DN**: OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com
   * **Användare klustergrupp DNs**: [\"HiveUsers\"]
   * **LDAPUrls**: [”ldaps://contoso.onmicrosoft.com:636”]
   * **DomainAdminUserName**: (Ange namnet på användaren admin)
   * **DomainAdminPassword**: (Ange domänanvändarlösenord för admin)
   * **Jag samtycker till villkoren som anges ovan**: (kontrollera)
   * **Fäst på instrumentpanelen**: (kontrollera)
3. Klicka på **Köp**. En ny panel visas med rubriken **Skicka malldistribution**. Det tar cirka 20 minuter att skapa ett kluster. När klustret har skapats klickar du på klusterbladet i portalen för att öppna den.

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Instruktioner för att ta bort ett kluster finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* För att ansluta till domänanslutna HDInsight-kluster med hjälp av SSH finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux, Unix eller OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

