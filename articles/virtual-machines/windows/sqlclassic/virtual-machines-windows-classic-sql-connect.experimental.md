---
title: Ansluta till en SQL Server-dator på Azure (klassisk) | Microsoft Docs
description: Lär dig hur du ansluter till SQL Server som körs på en virtuell dator i Azure. Det här avsnittet använder den klassiska distributionsmodellen. Scenarierna som varierar beroende på vilken nätverkskonfiguration och platsen för klienten.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 558606b6993aff2053dfbbf6adfd864cd827bbb1
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54328988"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Anslut en virtuell SQL Server-dator på Azure (Klassisk distribution)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klassisk](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Översikt
Det här avsnittet beskriver hur du ansluter till SQL Server-instansen som körs på virtuella Azure-datorer. Det omfattar tips [allmänna anslutningsproblem scenarier](#connection-scenarios) och ger [beskrivs stegen för att konfigurera SQL Server-anslutningen i en Azure VM](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du använder Resource Manager-VM, se [Anslut till en SQL Server-dator på Azure med hjälp av Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>-Scenarier
Det sätt som en klient ansluter till SQL Server som körs på en virtuell dator skiljer sig beroende på platsen för klienten och datorn/nätverkskonfigurationen. Några vanliga scenarier:

* [Anslut till SQL Server i samma molntjänst](#connect-to-sql-server-in-the-same-cloud-service)
* [Anslut till SQL Server via internet](#connect-to-sql-server-over-the-internet)
* [Anslut till SQL Server i samma virtuella nätverk](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Innan du ansluter med någon av dessa metoder, måste du följa den [steg i den här artikeln för att konfigurera anslutningen](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Anslut till SQL Server i samma molntjänst
Flera virtuella datorer kan skapas i samma molntjänst. Information om det här scenariot för virtuella datorer finns i [hur du ansluter virtuella datorer till virtuella nätverk eller molntjänster](../classic/connect-vms-classic.md#connect-vms-in-a-standalone-cloud-service). I detta scenario försöker en klient på en virtuell dator att ansluta till SQL Server som körs på en annan virtuell dator i samma molntjänst.

I det här scenariot kan du ansluta med hjälp av den virtuella datorn **namn** (visas också som **datornamn** eller **värdnamn** i portalen). Det här är det namn du angav för den virtuella datorn när du skapar. Exempel: Om du har gett din SQL-VM **mysqlvm**, en klient virtuell dator i samma molntjänst kan använda följande anslutningssträng för att ansluta:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Anslut till SQLServer via Internet
Om du vill ansluta till din SQL Server database engine från Internet, måste du skapa en virtuell dator-slutpunkten för inkommande TCP-kommunikation. I det här Azure-konfigurationssteget dirigeras inkommande trafik via TCP-porten till en TCP-port som är tillgänglig på den virtuella datorn.

För att ansluta via internet, måste du använda den Virtuella datorns DNS-namn och portnummer för VM-slutpunkt (konfigureras senare i den här artikeln). För att hitta DNS-namnet, gå till Azure-portalen och välj **virtuella datorer (klassiska)**. Välj sedan den virtuella datorn. Den **DNS-namnet** visas i den **översikt** avsnittet.

Anta exempelvis att en klassisk virtuell dator med namnet **mysqlvm** med DNS-namnet **mysqlvm7777.cloudapp.net** och en VM-slutpunkten för **57500**. Förutsatt att korrekt konfigurerade anslutningar, följande anslutningssträng kunde användas för åtkomst till den virtuella datorn från var som helst på internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Även om den här anslutningssträngen möjliggör anslutningar för klienter via internet, innebär detta inte att vem som helst kan ansluta till SQL-servern. Utanför måste klienter rätt användarnamn och lösenord. För ytterligare säkerhet, inte använda den välkända porten 1433 för offentliga VM-slutpunkten. Och om det är möjligt, Överväg att lägga till en ACL på slutpunkten för att begränsa trafik till klienterna du tillåta. Anvisningar om hur du använder ACL: er med slutpunkter finns i [hantera ACL på en slutpunkt](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> När du använder den här tekniken för att kommunicera med SQL Server, alla utgående data från Azure-datacentret lyder under normal [prissättning på utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Anslut till SQL Server i samma virtuella nätverk
[Virtuellt nätverk](../../../virtual-network/virtual-networks-overview.md) möjliggör ytterligare scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om de virtuella datorerna finns i olika molntjänster. Och med en [plats-till-plats VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), kan du skapa en hybrid-arkitektur som ansluter virtuella datorer med lokala nätverk och virtuella datorer.

Virtuella nätverk kan du ansluta dina virtuella Azure-datorer till en domän. Ansluter till en domän är det enda sättet att använda Windows-autentisering med SQL Server. Andra anslutningsscenarier kräver SQL-autentisering med användarnamn och lösenord.

Om du planerar att konfigurera en domänmiljö och Windows-autentisering, behöver du inte konfigurera den offentliga slutpunkten eller SQL-autentisering och inloggningar. I det här scenariot kan du ansluta till SQL Server-instansen genom att ange SQL Server-VM-namnet i anslutningssträngen. I följande exempel förutsätter att Windows-autentisering har konfigurerats och att användaren har beviljats åtkomst till SQL Server-instansen.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Stegen för att konfigurera SQL Server-anslutningen i en Azure VM
Följande steg visar hur du ansluter till SQL Server-instansen över internet med hjälp av SQL Server Management Studio (SSMS). Dock gäller samma steg för att göra din SQL Server-dator tillgängliga för ditt program som körs både lokalt och i Azure.

Innan du kan ansluta till instansen av SQL Server från en annan virtuell dator eller internet, måste du utföra följande uppgifter:

* [Skapa en TCP-slutpunkt för den virtuella datorn](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Öppna TCP-portar i Windows-brandväggen](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurera SQL Server för att lyssna på TCP-protokollet](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurera SQL Server för blandat läge-autentisering](#configure-sql-server-for-mixed-mode-authentication)
* [Skapa SQL Server-autentiseringsinloggningar](#create-sql-server-authentication-logins)
* [Bestämma DNS-namnet på den virtuella datorn](#determine-the-dns-name-of-the-virtual-machine)
* [Anslut till databasmotorn från en annan dator](#connect-to-the-database-engine-from-another-computer)

Anslutningssökvägen summeras som i följande diagram:

![Ansluta till en SQL Server-dator](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Nästa steg
Om du också tänker använda AlwaysOn-Tillgänglighetsgrupper för hög tillgänglighet och katastrofåterställning, bör du överväga att implementera en lyssnare. Databasen klienterna ansluter till lyssnaren i stället direkt till en SQL Server-instanserna. Lyssnaren dirigerar klienter till den primära repliken i tillgänglighetsgruppen. Mer information finns i [konfigurera en ILB-lyssnare för AlwaysOn-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Det är viktigt att granska alla de rekommenderade säkerhetsmetoderna för SQL Server på virtuella Azure-datorer. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md).

[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure. 

Andra ämnen som rör som kör SQL Server i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

