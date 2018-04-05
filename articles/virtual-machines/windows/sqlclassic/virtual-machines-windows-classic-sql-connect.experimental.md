---
title: Ansluta till en SQL Server-dator i Azure (klassisk) | Microsoft Docs
description: Lär dig hur du ansluter till SQL Server som körs på en virtuell dator i Azure. Det här avsnittet använder den klassiska distributionsmodellen. Scenarier som skiljer sig åt beroende på nätverkskonfigurationen och platsen för klienten.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: e986440edc0b683d1b1cd49207a355dac060c53d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Anslut en virtuell SQL Server-dator på Azure (Klassisk distribution)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klassisk](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Översikt
Det här avsnittet beskriver hur du ansluter till din SQL Server-instans som körs på en virtuell Azure-dator. Det täcker vissa [allmänna anslutningsproblem scenarier](#connection-scenarios) och ger [beskrivs stegen för att konfigurera SQL Server-anslutningen i en Azure VM](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du använder Hanteraren för virtuella datorer, se [Anslut till en SQL Server-dator på Azure med hjälp av hanteraren för filserverresurser](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>-Scenarier
Hur en klient ansluter till SQL Server som körs på en virtuell dator varierar beroende på platsen för klienten och datorn/nätverkskonfiguration. Några vanliga scenarier:

* [Ansluta till SQL Server i samma molntjänst](#connect-to-sql-server-in-the-same-cloud-service)
* [Ansluta till SQL Server via internet](#connect-to-sql-server-over-the-internet)
* [Ansluta till SQL Server i samma virtuella nätverk](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Innan du ansluter med någon av dessa metoder, måste du följa den [steg i den här artikeln för att konfigurera anslutningen](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Ansluta till SQL Server i samma molntjänst
Flera virtuella datorer kan skapas i samma molntjänst. Det här scenariot för virtuella datorer finns [så att ansluta virtuella datorer med virtuella nätverk eller molnet](../classic/connect-vms-classic.md#connect-vms-in-a-standalone-cloud-service). I det här scenariot försöker en klient på en virtuell dator att ansluta till SQL Server som körs på en annan virtuell dator i samma molntjänst.

I det här scenariot kan du ansluta med hjälp av den virtuella datorn **namn** (visas även som **datornamn** eller **värdnamn** i portalen). Detta är det namn du angett för den virtuella datorn när du skapar. Till exempel om du har gett din SQL-VM **mysqlvm**, en klient VM i samma molntjänst kan använda följande anslutningssträng för att ansluta:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Ansluta till SQLServer via Internet
Om du vill ansluta till SQL Server-databasmotorn från Internet, måste du skapa en virtuell dator-slutpunkt för inkommande TCP-kommunikation. I det här Azure-konfigurationssteget dirigeras inkommande trafik via TCP-porten till en TCP-port som är tillgänglig på den virtuella datorn.

För att ansluta via internet, måste du använda den Virtuella datorns DNS-namn och portnummer för VM slutpunkt (konfigurerat nedan). Navigera till Azure-portalen för att hitta DNS-namn och markera **virtuella datorer (klassisk)**. Välj sedan den virtuella datorn. Den **DNS-namnet** visas i den **översikt** avsnitt.

Anta till exempel att en klassisk virtuell dator med namnet **mysqlvm** med DNS-namnet **mysqlvm7777.cloudapp.net** och en VM-slutpunkten för **57500**. Under förutsättning att korrekt konfigurerad anslutning, följande anslutningssträng användas för att komma åt den virtuella datorn från var som helst på internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Även om den här anslutningssträngen gör anslutningen för klienter på Internet, innebär detta inte att någon kan ansluta till SQL-servern. Utanför måste klienter rätt användarnamn och lösenord. För ytterligare säkerhet kan inte använda välkända port 1433 för offentliga virtuella slutpunkten. Och om möjligt bör du lägga till en ACL på din slutpunkt för att begränsa trafik till klienterna du tillåter. Anvisningar om hur du använder ACL: er med slutpunkter finns [hantera ACL på en slutpunkt](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> När du använder den här tekniken för att kommunicera med SQL Server, alla utgående data från Azure-datacentret regleras normal [på utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Ansluta till SQL Server i samma virtuella nätverk
[Virtuellt nätverk](../../../virtual-network/virtual-networks-overview.md) möjliggör ytterligare scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om dessa virtuella datorer finns i olika molntjänster. Och med en [plats-till-plats VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), kan du skapa en hybrid-arkitektur som ansluter virtuella datorer med lokala nätverk och datorer.

Virtuella nätverk kan du ansluta din virtuella Azure-datorer till en domän. Ansluta till en domän är det enda sättet att använda Windows-autentisering med SQL Server. De andra scenarierna kräver SQL-autentisering med användarnamn och lösenord.

Om du planerar att konfigurera en domänmiljö och Windows-autentisering, behöver du inte konfigurera offentlig slutpunkt eller SQL-autentisering och inloggningar. I det här scenariot kan du ansluta till SQL Server-instansen genom att ange SQL Server-VM-namn i anslutningssträngen. I följande exempel förutsätter att Windows-autentisering har konfigurerats och att användaren har beviljats åtkomst till SQL Server-instansen.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Steg för att konfigurera SQL Server-anslutningen i en Azure VM
Följande steg visar hur du ansluter till SQL Server-instansen över internet med SQL Server Management Studio (SSMS). Dock gäller samma steg för att göra den virtuella datorn för SQL Server tillgängliga för ditt program som körs både lokalt och i Azure.

Innan du kan ansluta till instansen av SQL Server från en annan virtuell dator eller internet, måste du utföra följande uppgifter:

* [Skapa en TCP-slutpunkt för den virtuella datorn](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Öppna TCP-portar i Windows-brandväggen](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurera SQL Server för att lyssna på TCP-protokollet](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurera SQL Server för verifiering i blandat läge](#configure-sql-server-for-mixed-mode-authentication)
* [Skapa SQL Server-autentisering-inloggningar](#create-sql-server-authentication-logins)
* [Bestämma DNS-namnet på den virtuella datorn](#determine-the-dns-name-of-the-virtual-machine)
* [Anslut till databasmotorn från en annan dator](#connect-to-the-database-engine-from-another-computer)

Anslutningssökvägen sammanfattning av följande diagram:

![Ansluter till en virtuell dator med SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Nästa steg
Om du dessutom planerar att använda AlwaysOn-Tillgänglighetsgrupper för hög tillgänglighet och katastrofåterställning, bör du överväga att implementera en lyssnare. Databas-klienter ansluter till lyssnaren i stället för direkt till en SQL Server-instanser. Lyssnaren dirigerar klienter till den primära repliken i tillgänglighetsgruppen. Mer information finns i [konfigurera en ILB-lyssnare för AlwaysOn-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Det är viktigt att granska alla de rekommenderade säkerhetsmetoderna för SQL Server körs på en virtuell Azure-dator. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md).

[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure. 

Andra avsnitt relaterade till SQL Server som körs i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

