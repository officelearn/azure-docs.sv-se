---
title: Ansluta till en virtuell dator för SQL Server (Resource Manager) | Microsoft Docs
description: Lär dig hur du ansluter till SQL Server som körs på en virtuell dator i Azure. Det här avsnittet använder den klassiska distributionsmodellen. Scenarier som skiljer sig åt beroende på nätverkskonfigurationen och platsen för klienten.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 522ece2528e43c1037dc6bb707201ecda8074dd9
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301394"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Ansluta till en SQL Server-dator på Azure

## <a name="overview"></a>Översikt

Det här avsnittet beskriver hur du ansluter till din SQL Server-instans som körs på en virtuell Azure-dator. Det täcker vissa [allmänna anslutningsproblem scenarier](#connection-scenarios) och ger [steg i portalen för att ändra inställningar för](#change). Om du behöver felsöka eller Konfigurera anslutning utanför portalen, finns det [manuell konfiguration](#manual) i slutet av det här avsnittet. 

Om du i stället måste en fullständig genomgång av både etablering och anslutningen, se [etablering av en SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>-Scenarier

Hur en klient ansluter till SQL Server som körs på en virtuell dator varierar beroende på platsen för klienten och nätverkskonfigurationen.

Om du etablerar en SQL Server-VM i Azure-portalen har du möjlighet att ange vilken typ av **SQL-anslutning**.

![Anslutningsmöjlighet för offentliga SQL under etableringen.](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Alternativen för anslutning är:

| Alternativ | Beskrivning |
|---|---|
| **Offentliga** | Ansluta till SQL Server via internet |
| **privata** | Ansluta till SQL Server i samma virtuella nätverk |
| **lokala** | Ansluta till SQL Server lokalt på samma virtuella dator | 

I följande avsnitt beskrivs de **offentliga** och **privata** alternativ i detalj.

## <a name="connect-to-sql-server-over-the-internet"></a>Ansluta till SQLServer via Internet

Om du vill ansluta till SQL Server-databasmotorn från Internet väljer **offentliga** för den **SQL-anslutning** typ i portalen under etableringen. Portalen utför automatiskt följande steg:

* Aktiverar TCP/IP-protokollet för SQL Server.
* Konfigurerar en brandväggsregel för att öppna SQL Server TCP-porten (standard 1433).
* Gör det möjligt för SQL Server-autentisering krävs för allmän åtkomst.
* Konfigurerar nätverkssäkerhetsgruppen för den virtuella datorn för TCP-trafik på port för SQL Server.

> [!IMPORTANT]
> Avbildningar av virtuella datorer för SQL Server Developer och Express-versioner aktiverar inte automatiskt TCP/IP-protokollet. För utvecklare och Express-versioner måste du använda SQL Server Configuration Manager till [manuellt Aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Alla klienter med åtkomst till internet kan ansluta till SQL Server-instansen genom att ange antingen offentliga IP-adressen för den virtuella datorn eller någon DNS-etikett som tilldelats att IP-adress. Om SQL Server-port 1433, behöver du inte ange i anslutningssträngen. Följande anslutningssträng ansluter till en SQL-VM med en DNS-etikett för `sqlvmlabel.eastus.cloudapp.azure.com` med SQL-autentisering (du kan också använda den offentliga IP-adressen).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Även om detta gör anslutningen för klienter på Internet, innebär detta inte att någon kan ansluta till SQL-servern. Utanför måste klienter rätt användarnamn och lösenord. För ytterligare säkerhet kan kan du undvika den välkända porten 1433. Till exempel om du har konfigurerat SQL Server för att lyssna på porten 1500 och upprätta rätt brandvägg och regler för nätverkssäkerhetsgrupper kan du ansluta genom att portnumret till namnet på servern. I följande exempel ändrar det föregående genom att lägga till ett anpassat portnummer **1500**, att namnet på servern:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> När du frågar SQL Server på en virtuell dator via internet alla utgående data från Azure-datacentret regleras normal [på utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Ansluta till SQL Server inom ett virtuellt nätverk

När du väljer **privata** för den **SQL-anslutning** typ i Azure-portalen konfigurerar de flesta inställningarna identiskt med **offentliga**. En skillnaden är att det finns ingen grupp nätverkssäkerhetsregeln som tillåter utanför trafik på SQL Server-porten (standard 1433).

> [!IMPORTANT]
> Avbildningar av virtuella datorer för SQL Server Developer och Express-versioner aktiverar inte automatiskt TCP/IP-protokollet. För utvecklare och Express-versioner måste du använda SQL Server Configuration Manager till [manuellt Aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Privata anslutningen används ofta tillsammans med [virtuellt nätverk](../../../virtual-network/virtual-networks-overview.md), vilket gör att flera scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om dessa virtuella datorer finns i olika resursgrupper. Och med en [plats-till-plats VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), kan du skapa en hybrid-arkitektur som ansluter virtuella datorer med lokala nätverk och datorer.

Virtuella nätverk kan du ansluta din virtuella Azure-datorer till en domän. Detta är det enda sättet att använda Windows-autentisering till SQL Server. De andra scenarierna kräver SQL-autentisering med användarnamn och lösenord.

Förutsatt att du har konfigurerat DNS i ditt virtuella nätverk kan ansluta du till SQL Server-instansen genom att ange namnet på SQL Server-VM-datorn i anslutningssträngen. I följande exempel förutsätter också att Windows-autentisering också har konfigurerats och att användaren har beviljats åtkomst till SQL Server-instansen.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Ändra inställningar för SQL-anslutning

Du kan ändra anslutningsinställningarna för din virtuella dator i SQL Server i Azure-portalen.

1. Välj i Azure-portalen **virtuella datorer**.

2. Välj din SQLServer-VM.

3. Under **inställningar**, klickar du på **SQL Server-konfigurationsfilen**.

4. Ändra den **SQL anslutningen nivån** till den obligatoriska inställningen. Du kan använda det här området om du vill ändra SQL Server-porten eller inställningarna för SQL-autentisering.

   ![Ändra SQL-anslutning](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Vänta några minuter att slutföra uppdateringen.

   ![SQL-VM uppdateringsmeddelande](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Aktivera TCP/IP för utvecklare och Express-versioner

När du ändrar inställningarna för SQL Server-anslutningen, aktiverar Azure automatiskt inte TCP/IP-protokollet för SQL Server Developer och Express Edition. I anvisningarna nedan förklaras hur du aktiverar TCP/IP manuellt så att du kan fjärransluta via IP-adress.

Först ansluta till SQL Server-datorn med fjärrskrivbord.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Därefter aktiverar du TCP/IP-protokollet med **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Anslut med SSMS

Följande steg visar hur du skapar en valfri DNS-etikett för din virtuella Azure-datorn och ansluter sedan med SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Manuell konfiguration och felsökning

Även om portalen innehåller alternativ för att automatiskt konfigurera anslutningen, är det bra att veta hur du konfigurerar anslutningen manuellt. Förstå kraven kan också underlätta felsökningen.

I följande tabell visas kraven för att ansluta till SQL Server som körs i en Azure VM.

| Krav | Beskrivning |
|---|---|
| [Aktivera läget för SQL Server-autentisering](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | SQL Server-autentisering behövs för att fjärransluta till den virtuella datorn om du har konfigurerat Active Directory på ett virtuellt nätverk. |
| [Skapa en SQL-inloggning](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Om du använder SQL-autentisering, måste en SQL-inloggning med ett användarnamn och lösenord som också har behörighet till måldatabasen. |
| [Aktivera TCP/IP-protokoll](#manualTCP) | SQL Server måste tillåta anslutningar via TCP. |
| [Aktivera brandväggsregel för SQL Server-porten](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Brandväggen på den virtuella datorn måste tillåta inkommande trafik på SQL Server-porten (standard 1433). |
| [Skapa en grupp för nätverkssäkerhetsregeln för TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Du måste tillåta att den virtuella datorn tar emot trafik på SQL Server-porten (standard 1433) om du vill ansluta via internet. Lokala och virtuella nätverk – endast anslutningar kräver inte detta. Det här är den enda steg som krävs i Azure-portalen. |

> [!TIP]
> Stegen i tabellen ovan är klar för dig när du konfigurerar anslutningen i portalen. Bara använda de här stegen för att bekräfta konfigurationen eller installera anslutningen manuellt för SQL Server.

## <a name="next-steps"></a>Nästa steg

Etablering instruktioner tillsammans med de här stegen för anslutning finns [etablering av en SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

Andra avsnitt relaterade till SQL Server som körs i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).