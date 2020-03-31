---
title: Ansluta till en virtuell SQL Server-dator (Resource Manager) | Microsoft-dokument
description: Lär dig hur du ansluter till SQL Server som körs på en virtuell dator i Azure. Det här avsnittet använder den klassiska distributionsmodellen. Scenarierna varierar beroende på nätverkskonfigurationen och klientens plats.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: deb337d989a3658e909cefa7a9ab028e37792562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243177"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Ansluta till en virtuell SQL Server-dator på Azure

## <a name="overview"></a>Översikt

I det här avsnittet beskrivs hur du ansluter till SQL Server-instansen som körs på en virtuell Azure-dator. Den täcker några [allmänna anslutningsscenarier](#connection-scenarios) och innehåller sedan [steg i portalen för att ändra anslutningsinställningarna](#change). Om du behöver felsöka eller konfigurera anslutning utanför portalen läser du den [manuella konfigurationen](#manual) i slutet av det här avsnittet. 

Om du hellre vill ha en fullständig genomgång av både etablering och anslutning läser [du Etablera en virtuell SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scenarier för anslutning

Hur en klient ansluter till SQL Server som körs på en virtuell dator skiljer sig beroende på klientens plats och nätverkskonfigurationen.

Om du etablerar en virtuell SQL Server-dator i Azure-portalen kan du välja att ange vilken typ av **SQL-anslutning**som ska anges .

![Alternativet Offentlig SQL-anslutning vid etablering](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Dina alternativ för anslutning inkluderar:

| Alternativ | Beskrivning |
|---|---|
| **Offentlig** | Ansluta till SQL Server via internet |
| **Privat** | Ansluta till SQL Server i samma virtuella nätverk |
| **Lokala** | Ansluta till SQL Server lokalt på samma virtuella dator | 

I följande avsnitt beskrivs alternativen **för offentliga** och **privata** alternativ mer i detalj.

## <a name="connect-to-sql-server-over-the-internet"></a>Ansluta till SQL Server via Internet

Om du vill ansluta till SQL Server-databasmotorn från Internet väljer du **Offentlig** för **SQL-anslutningstypen** i portalen under etableringen. Portalen gör automatiskt följande:

* Aktiverar TCP/IP-protokollet för SQL Server.
* Konfigurerar en brandväggsregel för att öppna SQL Server TCP-porten (standard 1433).
* Aktiverar SQL Server-autentisering som krävs för offentlig åtkomst.
* Konfigurerar nätverkssäkerhetsgruppen på den virtuella datorn till all TCP-trafik på SQL Server-porten.

> [!IMPORTANT]
> Avbildningarna för den virtuella datorn för SQL Server Developer- och Express-utgåvorna aktiverar inte automatiskt TCP/IP-protokollet. För utvecklar- och Express-utgåvor måste du använda SQL Server Configuration Manager för att [manuellt aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Alla klienter med internetåtkomst kan ansluta till SQL Server-instansen genom att ange antingen den virtuella datorns offentliga IP-adress eller en DNS-etikett som tilldelats den IP-adressen. Om SQL Server-porten är 1433 behöver du inte ange den i anslutningssträngen. Följande anslutningssträng ansluter till en VIRTUELL SQL-dator med en DNS-etikett `sqlvmlabel.eastus.cloudapp.azure.com` som använder SQL-autentisering (du kan också använda den offentliga IP-adressen).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Även om detta möjliggör anslutning för klienter över internet, innebär detta inte att vem som helst kan ansluta till din SQL Server. Externa klienter måste rätt användarnamn och lösenord. Men för ytterligare säkerhet kan du undvika den välkända porten 1433. Om du till exempel har konfigurerat SQL Server för att lyssna på port 1500 och fastställt lämpliga brandväggs- och nätverkssäkerhetsgruppsregler kan du ansluta genom att lägga till portnumret till servernamnet. I följande exempel ändras det föregående genom att lägga till ett anpassat portnummer, **1500**, i servernamnet:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> När du frågar SQL Server i en virtuell dator via internet, är alla utgående data från Azure-data föremål för normal [prissättning vid utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Ansluta till SQL Server i ett virtuellt nätverk

När du väljer **Privat** för **SQL-anslutningstypen** i portalen konfigurerar Azure de flesta inställningar som är identiska med **Offentlig**. Den enda skillnaden är att det inte finns någon regel för nätverkssäkerhetsgrupp som tillåter extern trafik på SQL Server-porten (standard 1433).

> [!IMPORTANT]
> Avbildningarna för den virtuella datorn för SQL Server Developer- och Express-utgåvorna aktiverar inte automatiskt TCP/IP-protokollet. För utvecklar- och Express-utgåvor måste du använda SQL Server Configuration Manager för att [manuellt aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Privat anslutning används ofta tillsammans med [virtuellt nätverk](../../../virtual-network/virtual-networks-overview.md), vilket möjliggör flera scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om dessa virtuella datorer finns i olika resursgrupper. Och med en [plats-till-plats-VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)kan du skapa en hybridarkitektur som ansluter virtuella datorer med lokala nätverk och datorer.

Virtuella nätverk kan du också ansluta till dina virtuella Azure-datorer till en domän. Det här är det enda sättet att använda Windows-autentisering till SQL Server. De andra anslutningsscenarierna kräver SQL-autentisering med användarnamn och lösenord.

Om du antar att du har konfigurerat DNS i det virtuella nätverket kan du ansluta till SQL Server-instansen genom att ange sql server-datorns namn i anslutningssträngen. I följande exempel förutsätts också att Windows-autentisering också har konfigurerats och att användaren har beviljats åtkomst till SQL Server-instansen.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a>Ändra inställningar för SQL-anslutning

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Du kan ändra anslutningsinställningarna för den virtuella SQL Server-datorn i Azure-portalen.

1. Välj **virtuella SQL-datorer i Azure-portalen**.

2. Välj din virtuella SQL Server-dator.

3. Under **Inställningar**väljer du **Säkerhet**.

4. Ändra **SQL-anslutningsnivån** till önskad inställning. Du kan också använda det här området för att ändra SQL Server-porten eller SQL-autentiseringsinställningarna.

   ![Ändra SQL-anslutning](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Vänta flera minuter innan uppdateringen har slutförts.

   ![Sql VM-uppdateringsmeddelande](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a>Aktivera TCP/IP för utvecklar- och expressutgåvor

När du ändrar anslutningsinställningar för SQL Server aktiverar Azure inte automatiskt TCP/IP-protokollet för SQL Server Developer och Express-utgåvor. I anvisningarna nedan förklaras hur du aktiverar TCP/IP manuellt så att du kan fjärransluta via IP-adress.

Anslut först till SQL Server-datorn med fjärrskrivbord.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Aktivera sedan TCP/IP-protokollet med **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Anslut med SSMS

Följande steg visar hur du skapar en valfri DNS-etikett för din Virtuella Azure-dator och sedan ansluter till SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a>Manuell konfiguration och felsökning

Även om portalen innehåller alternativ för att automatiskt konfigurera anslutningen, är det bra att veta hur du konfigurerar anslutningen manuellt. Att förstå kraven kan också underlätta felsökning.

I följande tabell visas kraven för att ansluta till SQL Server som körs i en virtuell Azure-dator.

| Krav | Beskrivning |
|---|---|
| [Aktivera SQL Server-autentiseringsläge](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | SQL Server-autentisering krävs för att ansluta till den virtuella datorn på distans om du inte har konfigurerat Active Directory i ett virtuellt nätverk. |
| [Skapa en SQL-inloggning](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Om du använder SQL-autentisering behöver du en SQL-inloggning med ett användarnamn och lösenord som också har behörighet till din måldatabas. |
| [Aktivera TCP/IP-protokoll](#manualtcp) | SQL Server måste tillåta anslutningar via TCP. |
| [Aktivera brandväggsregel för SQL Server-porten](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Brandväggen på den virtuella datorn måste tillåta inkommande trafik på SQL Server-porten (standard 1433). |
| [Skapa en regel för nätverkssäkerhetsgrupp för TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Du måste tillåta att den virtuella datorn tar emot trafik på SQL Server-porten (standard 1433) om du vill ansluta via Internet. Lokala och virtuella anslutningar endast för nätverk kräver detta. Det här är det enda steget som krävs i Azure-portalen. |

> [!TIP]
> Stegen i tabellen ovan görs för dig när du konfigurerar anslutningen i portalen. Använd bara dessa steg för att bekräfta konfigurationen eller för att konfigurera anslutningen manuellt för SQL Server.

## <a name="next-steps"></a>Efterföljande moment

Information om hur du ser etableringsinstruktioner tillsammans med de här anslutningsstegen finns i [Etablera en virtuell SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

Mer information om hur du kör SQL Server i virtuella Azure-datorer finns i [SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).