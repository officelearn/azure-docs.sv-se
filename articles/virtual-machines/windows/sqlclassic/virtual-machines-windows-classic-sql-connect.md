---
title: Ansluta till en SQL Server virtuell dator på Azure (klassisk) | Microsoft Docs
description: Lär dig hur du ansluter till SQL Server som körs på en virtuell dator i Azure. I det här avsnittet används den klassiska distributions modellen. Scenarierna varierar beroende på nätverks konfigurationen och klientens plats.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 0717f6f75b6bd8bb7ba4d53f8240414b5169540d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014797"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Anslut en virtuell SQL Server-dator på Azure (klassisk distribution)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)
> * [Klassisk](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Översikt
I det här avsnittet beskrivs hur du ansluter till din SQL Server-instans som körs på en virtuell Azure-dator. Det täcker några [allmänna anslutnings scenarier](#connection-scenarios) och innehåller sedan [detaljerade steg för att konfigurera SQL Server anslutning i en virtuell Azure-dator](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du använder virtuella Resource Manager-datorer, se [ansluta till en SQL Server virtuell dator på Azure med Resource Manager](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md).

## <a name="connection-scenarios"></a>Anslutnings scenarier
Hur en klient ansluter till SQL Server som körs på en virtuell dator varierar beroende på klientens plats och konfigurationen av dator/nätverk. Några vanliga scenarier:

* [Anslut till SQL Server i samma moln tjänst](#connect-to-sql-server-in-the-same-cloud-service)
* [Ansluta till SQL Server via Internet](#connect-to-sql-server-over-the-internet)
* [Anslut till SQL Server i samma virtuella nätverk](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Innan du ansluter till någon av dessa metoder måste du följa [stegen i den här artikeln för att konfigurera anslutningen](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Anslut till SQL Server i samma moln tjänst
Flera virtuella datorer kan skapas i samma moln tjänst. Information om det här scenariot för virtuella datorer finns i [så här ansluter du virtuella datorer med ett virtuellt nätverk eller en moln tjänst](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). Det här scenariot är när en klient på en virtuell dator försöker ansluta till SQL Server som körs på en annan virtuell dator i samma moln tjänst.

I det här scenariot kan du ansluta med **namnet** på den virtuella datorn (visas också som **dator namn** eller **värdnamn** i portalen). Detta är det namn som du angav för den virtuella datorn när du skapade. Om du till exempel har namngett din SQL VM- **mysqlvm**kan en virtuell klient dator i samma moln tjänst använda följande anslutnings sträng för att ansluta:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Ansluta till SQL Server via Internet
Om du vill ansluta till din SQL Server-databasmotor från Internet måste du skapa en virtuell dator slut punkt för inkommande TCP-kommunikation. I det här Azure-konfigurationssteget dirigeras inkommande trafik via TCP-porten till en TCP-port som är tillgänglig på den virtuella datorn.

Om du vill ansluta via Internet måste du använda den virtuella datorns DNS-namn och port nummer för den virtuella datorns slut punkt (konfigureras senare i den här artikeln). Du hittar DNS-namnet genom att navigera till Azure Portal och välja **virtuella datorer (klassisk)**. Välj sedan den virtuella datorn. **DNS-namnet** visas i **översikts** avsnittet.

Anta till exempel att du har en klassisk virtuell dator med namnet **mysqlvm** med DNS-namnet **MYSQLVM7777.CLOUDAPP.net** och en VM-slutpunkt på **57500**. Om du antar korrekt konfigurerad anslutning kan följande anslutnings sträng användas för att få åtkomst till den virtuella datorn från var som helst på Internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Även om detta möjliggör anslutning för klienter via Internet innebär detta inte att vem som helst kan ansluta till din SQL Server. Externa klienter har rätt användar namn och lösen ord. För ytterligare säkerhet ska du inte använda den välkända porten 1433 för den offentliga virtuella dator slut punkten. Om möjligt bör du överväga att lägga till en ACL på slut punkten för att begränsa trafiken enbart till de klienter som du tillåter. Instruktioner för hur du använder ACL: er med slut punkter finns i [Hantera ACL för en slut punkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Det är viktigt att Observera att när du använder den här metoden för att kommunicera med SQL Server, är alla utgående data från Azure-datacentret föremål för normal [prissättning på utgående data överföringar](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Anslut till SQL Server i samma virtuella nätverk
[Virtual Network](../../../virtual-network/virtual-networks-overview.md) aktiverar ytterligare scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om de virtuella datorerna finns i olika moln tjänster. Med en [plats-till-plats-VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)kan du skapa en hybrid arkitektur som ansluter virtuella datorer med lokala nätverk och datorer.

Med virtuella nätverk kan du också ansluta dina virtuella Azure-datorer till en domän. Detta är det enda sättet att använda Windows-autentisering för att SQL Server. De andra anslutnings scenarierna kräver SQL-autentisering med användar namn och lösen ord.

Om du ska konfigurera en domän miljö och Windows-autentisering behöver du inte använda stegen i den här artikeln för att konfigurera den offentliga slut punkten eller SQL-autentisering och inloggningar. I det här scenariot kan du ansluta till din SQL Server-instans genom att ange SQL Server VM namnet i anslutnings strängen. I följande exempel förutsätts att Windows-autentisering också har kon figurer ATS och att användaren har beviljats åtkomst till SQL Server-instansen.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Steg för att konfigurera SQL Server anslutning på en virtuell Azure-dator
Följande steg visar hur du ansluter till SQL Server-instansen via Internet med SQL Server Management Studio (SSMS). Samma steg gäller dock för att göra din SQL Server virtuella dator tillgänglig för dina program, som körs både lokalt och i Azure.

Innan du kan ansluta till instansen av SQL Server från en annan virtuell dator eller Internet, måste du utföra följande uppgifter enligt beskrivningen i följande avsnitt:

* [Skapa en TCP-slutpunkt för den virtuella datorn](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Öppna TCP-portar i Windows-brandväggen](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurera SQL Server för att lyssna på TCP-protokollet](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurera SQL Server för blandat läge-autentisering](#configure-sql-server-for-mixed-mode-authentication)
* [Skapa SQL Server-autentiseringsinloggningar](#create-sql-server-authentication-logins)
* [Bestämma DNS-namnet på en virtuell dator](#determine-the-dns-name-of-the-virtual-machine)
* [Anslut till databasmotorn från en annan dator](#connect-to-the-database-engine-from-another-computer)

Anslutnings Sök vägen sammanfattas i följande diagram:

![Ansluta till en SQL Server virtuell dator](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Efterföljande moment
Om du också planerar att använda AlwaysOn-tillgänglighetsgrupper för hög tillgänglighet och haveri beredskap bör du överväga att implementera en lyssnare. Databas klienter ansluter till lyssnaren i stället för direkt till en av SQL Server instanserna. Lyssnaren dirigerar klienter till den primära repliken i tillgänglighets gruppen. Mer information finns i [Konfigurera en ILB-lyssnare för AlwaysOn-tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Det är viktigt att du går igenom alla rekommenderade säkerhets metoder för SQL Server som körs på en virtuell Azure-dator. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/security-considerations-best-practices.md).

[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure. 

Andra avsnitt om att köra SQL Server i virtuella Azure-datorer finns [SQL Server på Azure-Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

