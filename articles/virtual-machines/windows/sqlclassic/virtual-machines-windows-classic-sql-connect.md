---
title: Ansluta till en virtuell SQL Server-dator på Azure (Klassisk) | Microsoft-dokument
description: Lär dig hur du ansluter till SQL Server som körs på en virtuell dator i Azure. Det här avsnittet använder den klassiska distributionsmodellen. Scenarierna varierar beroende på nätverkskonfigurationen och klientens plats.
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
ms.openlocfilehash: 4627d9c4fa5c87e8e80ab80892062dabd77e9229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249716"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Anslut en virtuell SQL Server-dator på Azure (klassisk distribution)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klassisk](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Översikt
I det här avsnittet beskrivs hur du ansluter till SQL Server-instansen som körs på en virtuell Azure-dator. Den täcker några [allmänna anslutningsscenarier](#connection-scenarios) och innehåller sedan [detaljerade steg för att konfigurera SQL Server-anslutning i en Azure VM](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du använder virtuella resurser i Resource Manager läser du [Anslut till en virtuell SQL Server-dator på Azure med Hjälp av Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scenarier för anslutning
Hur en klient ansluter till SQL Server som körs på en virtuell dator skiljer sig beroende på var klienten och datorn/nätverkskonfigurationen finns. Några vanliga scenarier:

* [Ansluta till SQL Server i samma molntjänst](#connect-to-sql-server-in-the-same-cloud-service)
* [Ansluta till SQL Server via internet](#connect-to-sql-server-over-the-internet)
* [Ansluta till SQL Server i samma virtuella nätverk](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Innan du ansluter till någon av dessa metoder måste du följa stegen i den [här artikeln för att konfigurera anslutningen](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Ansluta till SQL Server i samma molntjänst
Flera virtuella datorer kan skapas i samma molntjänst. För att förstå det här scenariot för virtuella datorer finns i [Så här ansluter du virtuella datorer med ett virtuellt nätverk eller en molntjänst](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). Det här scenariot är när en klient på en virtuell dator försöker ansluta till SQL Server som körs på en annan virtuell dator i samma molntjänst.

I det här fallet kan du ansluta med **vm-namnet** (visas även som **datornamn** eller **värdnamn** i portalen). Det här är namnet som du angav för den virtuella datorn när den skapas. Om du till exempel har döpt din SQL VM **mysqlvm**kan en klient-VM i samma molntjänst använda följande anslutningssträng för att ansluta:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Ansluta till SQL Server via Internet
Om du vill ansluta till SQL Server-databasmotorn från Internet måste du skapa en slutpunkt för virtuella datorer för inkommande TCP-kommunikation. I det här Azure-konfigurationssteget dirigeras inkommande trafik via TCP-porten till en TCP-port som är tillgänglig på den virtuella datorn.

Om du vill ansluta via internet måste du använda den virtuella datorns DNS-namn och vm-slutpunktsportnumret (konfigurerat senare i den här artikeln). Om du vill hitta DNS-namnet navigerar du till Azure-portalen och väljer **Virtuella datorer (klassiska).** Välj sedan din virtuella dator. **DNS-namnet** visas i avsnittet **Översikt.**

Tänk dig till exempel en klassisk virtuell dator med namnet **mysqlvm** med ett DNS-namn **på mysqlvm7777.cloudapp.net** och en vm-slutpunkt **på 57500**. Om du antar korrekt konfigurerad anslutning kan följande anslutningssträng användas för att komma åt den virtuella datorn var som helst på internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Även om detta möjliggör anslutning för klienter över internet, innebär detta inte att vem som helst kan ansluta till din SQL Server. Externa klienter måste rätt användarnamn och lösenord. För ytterligare säkerhet ska du inte använda den välkända port 1433 för slutpunkten för den offentliga virtuella datorn. Och om möjligt, överväg att lägga till en åtkomstkontrollista på slutpunkten för att begränsa trafiken endast till de klienter du tillåter. Instruktioner om hur du använder ACL med slutpunkter finns i [Hantera åtkomstkontrollistan på en slutpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Det är viktigt att notera att när du använder den här tekniken för att kommunicera med SQL Server, är alla utgående data från Azure-data föremål för normal [prissättning vid utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Ansluta till SQL Server i samma virtuella nätverk
[Virtuellt nätverk](../../../virtual-network/virtual-networks-overview.md) möjliggör ytterligare scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om dessa virtuella datorer finns i olika molntjänster. Och med en [plats-till-plats-VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)kan du skapa en hybridarkitektur som ansluter virtuella datorer med lokala nätverk och datorer.

Med virtuella nätverk kan du också ansluta till dina virtuella Azure-datorer till en domän. Det här är det enda sättet att använda Windows-autentisering till SQL Server. De andra anslutningsscenarierna kräver SQL-autentisering med användarnamn och lösenord.

Om du ska konfigurera en domänmiljö och Windows-autentisering behöver du inte använda stegen i den här artikeln för att konfigurera den offentliga slutpunkten eller SQL-autentiseringen och inloggningarna. I det här fallet kan du ansluta till SQL Server-instansen genom att ange SQL Server VM-namnet i anslutningssträngen. I följande exempel förutsätts att Windows-autentisering också har konfigurerats och att användaren har beviljats åtkomst till SQL Server-instansen.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Steg för att konfigurera SQL Server-anslutning i en virtuell Azure-dator
Följande steg visar hur du ansluter till SQL Server-instansen via Internet med HJÄLP AV SQL Server Management Studio (SSMS). Samma steg gäller dock för att göra din virtuella SQL Server-dator tillgänglig för dina program, som körs både lokalt och i Azure.

Innan du kan ansluta till instansen av SQL Server från en annan virtuell dator eller internet måste du utföra följande uppgifter enligt beskrivningen i följande avsnitt:

* [Skapa en TCP-slutpunkt för den virtuella datorn](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Öppna TCP-portar i Windows-brandväggen](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurera SQL Server för att lyssna på TCP-protokollet](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurera SQL Server för blandat läge-autentisering](#configure-sql-server-for-mixed-mode-authentication)
* [Skapa SQL Server-autentiseringsinloggningar](#create-sql-server-authentication-logins)
* [Bestämma DNS-namnet på en virtuell dator](#determine-the-dns-name-of-the-virtual-machine)
* [Anslut till databasmotorn från en annan dator](#connect-to-the-database-engine-from-another-computer)

Anslutningssökvägen sammanfattas med följande diagram:

![Ansluta till en virtuell SQL Server-dator](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Efterföljande moment
Om du också planerar att använda AlwaysOn-tillgänglighetsgrupper för hög tillgänglighet och haveriberedskap bör du överväga att implementera en lyssnare. Databasklienter ansluter till lyssnaren i stället för direkt till en av SQL Server-instanserna. Lyssnaren dirigerar klienter till den primära repliken i tillgänglighetsgruppen. Mer information finns i [Konfigurera en ILB-lyssnare för AlwaysOn-tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

Det är viktigt att granska alla de bästa säkerhetsrutinerna för SQL Server som körs på en virtuell Azure-dator. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md).

[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure. 

Mer information om hur du kör SQL Server i virtuella Azure-datorer finns i [SQL Server på virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

