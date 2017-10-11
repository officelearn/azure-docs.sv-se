---
title: "Konfigurera domänanslutna HDInsight-kluster med hjälp av PowerShell - Azure | Microsoft Docs"
description: "Lär dig hur du skapar och konfigurerar domänanslutna HDInsight-kluster med Azure PowerShell"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 9da76bb5f649817cd2f027f3d0eb46d58a996b4f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Konfigurera domänanslutna HDInsight-kluster (förhandsversion) med Azure PowerShell
Lär dig hur du ställer in ett Azure HDInsight-kluster med Azure Active Directory (Azure AD) och [Apache Ranger](http://hortonworks.com/apache/ranger/) med hjälp av Azure PowerShell. Ett Azure PowerShell-skript har angetts så att konfigurationen snabbare och mindre fel felbenägna. Domänanslutna HDInsight kan bara konfigureras på Linux-baserade kluster. Mer information finns i [införa domänanslutna HDInsight-kluster](hdinsight-domain-joined-introduction.md).

> [!IMPORTANT]
> Oozie har inte aktiverats på domänanslutna HDInsight.

En typisk konfiguration för domänanslutna HDInsight-kluster omfattar följande steg:

1. Skapa ett Azure klassiska virtuella nätverk för din Azure AD.  
2. Skapa och konfigurera Azure AD och Azure AD DS.
3. Lägga till en virtuell dator i det klassiska virtuella nätverket för att skapa organisationsenhet. 
4. Skapa en organisationsenhet för Azure AD DS.
5. Skapa ett HDInsight-VNet i Azure-resurs management-läge.
6. Konfigurera omvänd DNS-zoner för Azure AD DS.
7. Peer-två Vnet.
8. Skapa ett HDInsight-kluster.

PowerShell-skriptet som utför steg 3 till 7. Du måste gå igenom steg 1 och 2 manuellt.  Om du vill använda Azure PowerShell, se [konfigurera domänanslutna HDInsight-kluster](hdinsight-domain-joined-configure.md). 

Ett exempel på den sista topologin ser ut som följer:

![Domänanslutna HDInsight-topologi](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

Eftersom Azure AD för närvarande stöder endast klassiska virtuella nätverk (Vnet) och Linux-baserade HDInsight-kluster endast stöder Azure Resource Manager baserade Vnet, HDInsight Azure AD-integration kräver två Vnet och en peering mellan dem. Jämförelse-information mellan de två distributionsmodellerna finns [Azure Resource Manager och klassisk distribution: Förstå distributionsmodeller och tillståndet för dina resurser](../azure-resource-manager/resource-manager-deployment-model.md). Två Vnet måste vara i samma region som Azure AD DS.

> [!NOTE]
> Den här kursen förutsätter att du inte har en Azure AD. Om du har ett kan du hoppa över delen i steg 2.
> 
> 

## <a name="prerequisites"></a>Krav
Du måste ha följande för att gå igenom den här kursen:

* Bekanta dig med [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) dess [priser](https://azure.microsoft.com/pricing/details/active-directory-ds/) struktur.
* Se till att prenumerationen är godkända för den här public preview. Du kan göra det genom att skicka ett e-postmeddelande till hdipreview@microsoft.com med ditt prenumerations-ID.
* Ett SSL-certifikat som signerats av en utfärdare för signering för din domän. Certifikatet som krävs genom att konfigurera säker LDAP. Självsignerade certifikat kan inte användas.
* Azure PowerShell.  Se [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Skapa ett Azure klassiska virtuella nätverk för din Azure AD.
Instruktioner finns i avsnittet [här](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic).

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Skapa och konfigurera Azure AD och Azure AD DS.
Instruktioner finns i avsnittet [här](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).

## <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet
PowerShell-skriptet kan hämtas från [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight). Extrahera zip-filen och spara filen lokalt.

**Så här redigerar du PowerShell-skript**

1. Öppna run.ps1 med hjälp av Windows PowerShell ISE eller valfri textredigerare.
2. Fyll i värdena för följande variabler:
   
   * **$SubscriptionName** – namnet på den Azure-prenumeration där du vill skapa ditt HDInsight-kluster. Du har redan skapat ett klassiskt virtuellt nätverk i den här prenumerationen och kommer att skapa ett virtuellt nätverk med Azure Resource Manager för HDInsight-kluster för prenumerationen.
   * **$ClassicVNetName** -klassiska virtuella nätverk som innehåller Azure AD DS. Det här virtuella nätverket måste vara i samma prenumeration som anges ovan. Det här virtuella nätverket måste skapas med hjälp av Azure portal och inte använder klassiska portalen. Om du följer anvisningarna i [konfigurera domänanslutna HDInsight-kluster (förhandsgranskning)](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic), standardnamnet är contosoaadvnet.
   * **$ClassicResourceGroupName** – i Resource Manager gruppnamn för det klassiska virtuella nätverk som nämns ovan. Till exempel contosoaadrg. 
   * **$ArmResourceGroupName** – resursgruppens namn inom vilken, som du vill skapa HDInsight-klustret. Du kan använda samma resursgrupp som $ArmResourceGroupName.  Om resursgruppen inte finns, skapar skriptet resursgruppen.
   * **$ArmVNetName** -namn för virtuellt nätverk i Resource Manager inom vilken du vill skapa HDInsight-klustret. Det här virtuella nätverket placeras i $ArmResourceGroupName.  Om det virtuella nätverket inte finns, skapar PowerShell-skriptet den. Om den finns, måste det tillhöra den resursgrupp som du anger ovan.
   * **$AddressVnetAddressSpace** – adressutrymme för nätverk för det virtuella nätverket Resource Manager. Se till att det här adressutrymmet är tillgänglig. Det här adressutrymmet får inte överlappa det klassiska virtuella nätverkets adressutrymme. Till exempel ”10.1.0.0/16”
   * **$ArmVnetSubnetName** -av Resource Manager virtuella undernät nätverksnamnet inom vilken du vill placera virtuella datorer för HDInsight-kluster. Om undernätet inte finns, skapas det PowerShell-skript. Om den finns, ska den vara en del av det virtuella nätverket som du anger ovan.
   * **$AddressSubnetAddressSpace** – nätverksadressintervallet för det virtuella nätverket för Resource Manager. HDInsight-klustret VM IP-adresser kommer att från den här adressintervallet i undernätet. Till exempel ”10.1.0.0/24”.
   * **$ActiveDirectoryDomainName** – Azure AD-domännamn som du vill ansluta till HDInsight-kluster virtuella datorerna till. Till exempel ”contoso.onmicrosoft.com”
   * **$ClusterUsersGroups** – namnet på säkerhetsgrupper från din AD som du vill synkronisera till HDInsight-klustret. Användare i den här säkerhetsgruppen kommer att kunna logga in på instrumentpanelen kluster med hjälp av autentiseringsuppgifterna för active directory-domän. Dessa säkerhetsgrupper måste finnas i active directory. Till exempel ”hiveusers” eller ”clusteroperatorusers”.
   * **$OrganizationalUnitName** -organisationsenheten i domänen där du vill placera till HDInsight-klustrets virtuella datorer och i tjänstens huvudnamn som används av klustret. PowerShell-skriptet skapar Organisationsenheten om det inte finns. Till exempel ”HDInsightOU”.
3. Spara ändringarna.

**Att köra skriptet**

1. Kör **Windows PowerShell** som administratör.
2. Bläddra till mappen run.ps1. 
3. Kör skriptet genom att skriva namnet på filen och tryck **RETUR**.  Den visas 3 inloggning dialogrutor:
   
   1. **Logga in på den klassiska Azure-portalen** – ange dina autentiseringsuppgifter som du använder för att logga in på den klassiska Azure-portalen. Du måste ha skapat Azure AD och Azure AD DS med hjälp av autentiseringsuppgifterna.
   2. **Logga in på Azure Resource Manager-portalen** – ange dina autentiseringsuppgifter som du använder för att logga in på Azure Resource Manager-portalen.
   3. **Namnet på användaren** – ange autentiseringsuppgifterna för användaren domännamnet som du vill vara administratör för HDInsight-klustret. Om du har skapat en Azure AD från början måste du har skapat den här användaren med hjälp av den här dokumentationen. 
      
      > [!IMPORTANT]
      > Ange användarnamnet i formatet: 
      > 
      > Domännamn\användarnamn (till exempel contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      Användaren måste ha behörighet för 3: att ansluta datorer till den angivna Active Directory-domänen att skapa tjänstens huvudnamn och datorobjekt inom den angivna organisationsenheten; och lägga till regler för omvänd DNS-proxy.

När du skapar omvänd DNS-zoner uppmanas skriptet du att ange ett nätverks-ID. Nätverks-ID måste vara Resource Manager virtuella nätverkets adressprefix. Till exempel om Resource Manager för virtuella nätverkets undernät adressutrymme är 10.2.0.0/24 ange 10.2.0.0/24 när verktyget efterfrågar nätverks-ID. 

## <a name="create-hdinsight-cluster"></a>Skapa HDInsight-kluster
I det här avsnittet skapar du ett Linux-baserade Hadoop-kluster i HDInsight med antingen Azure-portalen eller [Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md). För andra metoder för att skapa kluster och förstå inställningarna, se [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder Resource Manager-mall för att skapa Hadoop-kluster i HDInsight finns [skapa Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Så här skapar du en domänansluten HDInsight-kluster med hjälp av Azure portal**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **ny**, **Intelligence + analys**, och sedan **HDInsight**.
3. Från den **nya HDInsight-kluster** bladet ange eller Välj följande värden:
   
   * **Klusternamnet**: Ange ett nytt namn för klustret för domänanslutna HDInsight-kluster.
   * **Prenumerationen**: Välj en Azure-prenumeration används för att skapa det här klustret.
   * **Klusterkonfiguration**:
     
     * **Klustret typen**: Hadoop. Domänanslutna HDInsight är för närvarande endast stöds på Hadoop-kluster.
     * **Operativsystemet**: Linux.  Domänanslutna HDInsight stöds bara på Linux-baserade HDInsight-kluster.
     * **Version**: Hadoop 2.7.3 (HDI 3.5). Domänanslutna HDInsight stöds bara på HDInsight-kluster av version 3.5.
     * **Klustret typen**: PREMIUM
       
       Klicka på **Välj** spara ändringarna.
   * **Autentiseringsuppgifter**: Konfigurera autentiseringsuppgifterna för både klustret användar- och SSH-användare.
   * **Datakällan**: skapa ett nytt lagringskonto eller använda ett befintligt lagringskonto som standardkontot för lagring för HDInsight-klustret. Platsen måste vara samma som de två Vnet.  Platsen är också platsen för HDInsight-klustret.
   * **Priser**: Välj antalet arbetarnoder i klustret.
   * **Avancerade konfigurationer**: 
     
     * **Domänanslutning & Vnet /-undernätet**: 
       
       * **Domäninställningar**: 
         
         * **Domännamn**: contoso.onmicrosoft.com
         * **Namnet på användaren**: Ange ett användarnamn för domänen. Den här domänen måste ha följande behörigheter: ansluta datorer till domänen och placerar dem på den organisationsenhet som du konfigurerat tidigare. Skapa tjänstens huvudnamn i organisationsenheten som du konfigurerat tidigare. Skapa omvänd DNS-poster. Den här domänanvändare blir administratören för den här datorn är domänansluten HDInsight-kluster.
         * **Domänlösenord**: Ange domänen användarens lösenord.
         * **Organisationsenheten**: Ange det unika namnet på den Organisationsenhet som du tidigare har konfigurerat. Till exempel: OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com
         * **LDAPS URL**: ldaps://contoso.onmicrosoft.com:636
         * **Åtkomst-användargrupp**: ange säkerhetsgruppen vars användare som du vill synkronisera till klustret. Till exempel HiveUsers.
           
           Klicka på **Välj** spara ändringarna.
           
           ![Domänanslutna HDInsight portal konfigurera domäninställningen](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Virtuellt nätverk**: contosohdivnet
       * **Undernät**: Undernät1
         
         Klicka på **Välj** spara ändringarna.        
         Klicka på **Välj** spara ändringarna.
   * **Resursgruppen**: Markera den resursgrupp som används för HDInsight-VNet (contosohdirg).
4. Klicka på **Skapa**.  

Ett annat alternativ för att skapa domänanslutna HDInsight-kluster är att använda Azure Resource Manager-mall. Följande procedur visar hur du gör:

**Så här skapar du en domänansluten HDInsight-kluster med hjälp av en Resource Manager-mall**

1. Klicka på följande bild för att öppna en Resource Manager-mall i Azure-portalen. Resource Manager-mallen finns i en offentlig blob-behållare. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
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
   * **Användare klustergrupp D Ns**”:\"CN = HiveUsers, OU = AADDC Users, DC =<DomainName>, DC = onmicrosoft, DC = com\"”
   * **LDAPUrls**: [”ldaps://contoso.onmicrosoft.com:636”]
   * **DomainAdminUserName**: (Ange namnet på användaren admin)
   * **DomainAdminPassword**: (Ange domänanvändarlösenord för admin)
   * **Jag samtycker till villkoren som anges ovan**: (kontrollera)
   * **Fäst på instrumentpanelen**: (kontrollera)
3. Klicka på **Köp**. En ny panel visas med rubriken **Skicka malldistribution**. Det tar cirka 20 minuter att skapa ett kluster. När klustret har skapats klickar du på klusterbladet i portalen för att öppna den.

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Instruktioner för att ta bort ett kluster finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Nästa steg

* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](hdinsight-domain-joined-run-hive.md).
* För att ansluta till domänanslutna HDInsight-kluster med hjälp av SSH finns [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

