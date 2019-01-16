---
title: Ansluta till en SQL Server-dator (Resource Manager) | Microsoft Docs
description: Lär dig hur du ansluter till SQL Server som körs på en virtuell dator i Azure. Det här avsnittet använder den klassiska distributionsmodellen. Scenarierna som varierar beroende på vilken nätverkskonfiguration och platsen för klienten.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3baa4a9b91e76b9072714229b6a46e9fca69bcdd
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331392"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Ansluta till en SQL Server-dator på Azure

## <a name="overview"></a>Översikt

Det här avsnittet beskriver hur du ansluter till SQL Server-instansen som körs på virtuella Azure-datorer. Det omfattar tips [allmänna anslutningsproblem scenarier](#connection-scenarios) och ger [steg i portalen för att ändra anslutningsinställningarna](#change). Om du behöver felsöka eller konfigurera anslutningen utanför portalen kan du läsa den [manuell konfiguration](#manual) i slutet av det här avsnittet. 

Om du vill hellre ha en fullständig genomgång av både etablering och anslutning, se [etablera en SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>-Scenarier

Det skiljer sig beroende på platsen för klienten och nätverkskonfigurationen på sätt som en klient ansluter till SQL Server som körs på en virtuell dator.

Om du etablerar en SQL Server-VM i Azure portal har du möjlighet att ange vilken typ av **SQL-anslutning**.

![Offentliga SQL anslutningsalternativet under etableringen](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Alternativen för anslutning är:

| Alternativ | Beskrivning |
|---|---|
| **Offentlig** | Anslut till SQL Server via internet |
| **Privat** | Anslut till SQL Server i samma virtuella nätverk |
| **lokala** | Ansluta till SQL Server lokalt på samma virtuella dator | 

I följande avsnitt beskrivs de **offentliga** och **privata** alternativ i detalj.

## <a name="connect-to-sql-server-over-the-internet"></a>Anslut till SQLServer via Internet

Om du vill ansluta till din SQL Server database engine från Internet väljer **offentliga** för den **SQL-anslutning** typ i portalen under etableringen. Portalen gör automatiskt följande steg:

* Aktiverar TCP/IP-protokollet för SQL Server.
* Konfigurerar en brandväggsregel för att öppna SQL Server TCP-porten (standard 1433).
* Gör det möjligt för SQL Server-autentisering som krävs för offentlig åtkomst.
* Konfigurerar nätverkssäkerhetsgruppen på den virtuella datorn för alla TCP-trafik på port för SQL Server.

> [!IMPORTANT]
> Virtuella datoravbildningar för SQL Server Developer och Express-versioner aktiverar inte TCP/IP-protokollet automatiskt. För versionerna Developer och Express, måste du använda SQL Server Configuration Manager till [manuellt Aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Alla klienter med åtkomst till internet kan ansluta till SQL Server-instansen genom att ange antingen offentliga IP-adressen för den virtuella datorn eller någon DNS-etikett som har tilldelats IP-adress. Om SQL Server-port 1433, behöver du inte anger den i anslutningssträngen. Följande anslutningssträng som ansluter till en SQL-VM med en DNS-etikett för `sqlvmlabel.eastus.cloudapp.azure.com` med SQL-autentisering (du kan också använda offentliga IP-adress).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Detta möjliggör anslutningar för klienter via internet innebär detta inte att vem som helst kan ansluta till SQL-servern. Utanför måste klienter rätt användarnamn och lösenord. För ytterligare säkerhet, kan du undvika den välkända porten 1433. Till exempel om du har konfigurerat SQL Server för att lyssna på port 1500 och fastställt rätt brandväggs- och reglerna för nätverkssäkerhetsgrupper kan du ansluta genom att lägga till portnumret till namnet på servern. I följande exempel ändrar det föregående genom att lägga till ett anpassat portnummer **1500**, för servernamnet:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> När du frågar SQL Server på en virtuell dator via internet, alla utgående data från Azure-datacentret lyder under normal [prissättning på utgående dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Anslut till SQL Server i ett virtuellt nätverk

När du väljer **privata** för den **SQL-anslutning** typ i Azure-portalen konfigurerar de flesta av inställningarna som är identisk med **offentliga**. En skillnaden är att det finns ingen regel för nätverkssäkerhetsgrupp som tillåter extern trafik på SQL Server-porten (standard 1433).

> [!IMPORTANT]
> Virtuella datoravbildningar för SQL Server Developer och Express-versioner aktiverar inte TCP/IP-protokollet automatiskt. För versionerna Developer och Express, måste du använda SQL Server Configuration Manager till [manuellt Aktivera TCP/IP-protokollet](#manualtcp) när du har skapat den virtuella datorn.

Privata anslutningar används ofta tillsammans med [virtuellt nätverk](../../../virtual-network/virtual-networks-overview.md), vilket gör att flera scenarier. Du kan ansluta virtuella datorer i samma virtuella nätverk, även om de virtuella datorerna finns i olika resursgrupper. Och med en [plats-till-plats VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), kan du skapa en hybrid-arkitektur som ansluter virtuella datorer med lokala nätverk och virtuella datorer.

Virtuella nätverk kan du ansluta dina virtuella Azure-datorer till en domän. Det här är det enda sättet att använda Windows-autentisering till SQL Server. Andra anslutningsscenarier kräver SQL-autentisering med användarnamn och lösenord.

Om vi antar att du har konfigurerat DNS i ditt virtuella nätverk kan ansluta du till SQL Server-instansen genom att ange namnet på SQL Server-VM-datorn i anslutningssträngen. I följande exempel förutsätter också att Windows-autentisering också har konfigurerats och att användaren har beviljats åtkomst till SQL Server-instansen.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Ändra SQL-anslutningsinställningarna

Du kan ändra anslutningsinställningarna för din SQL Server-dator i Azure-portalen.

1. I Azure-portalen väljer du **virtuella datorer**.

2. Välj din SQLServer-dator.

3. Under **inställningar**, klickar du på **konfiguration av SQL Server**.

4. Ändra den **SQL-anslutningsnivå** till den obligatoriska inställningen. Du kan också använda det här området ändra SQL Server-porten eller inställningarna för SQL-autentisering.

   ![Ändra SQL-anslutning](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Vänta några minuter att slutföra uppdateringen.

   ![Uppdateringsavisering för SQL VM](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Aktivera TCP/IP för versionerna Developer och Express

När du ändrar inställningarna för SQL Server-anslutningen aktiveras Azure inte automatiskt TCP/IP-protokollet för SQL Server Developer och Express. I anvisningarna nedan förklaras hur du aktiverar TCP/IP manuellt så att du kan fjärransluta via IP-adress.

Först ansluta till SQL Server-datorn med fjärrskrivbord.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Därefter aktiverar du TCP/IP-protokollet med **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Anslut med SSMS

Följande steg visar hur du skapar en valfri DNS-etikett för Azure-VM och sedan ansluta med SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Manuell konfiguration och felsökning

Portalen innehåller alternativ för att automatiskt konfigurera anslutningen, är det bra att veta hur du konfigurerar anslutningen manuellt. Förstå kraven kan också underlätta felsökning.

I följande tabell visas kraven för att ansluta till SQL Server som körs i en Azure VM.

| Krav | Beskrivning |
|---|---|
| [Aktivera SQL Server-autentiseringsläget](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | SQL Server-autentisering behövs för att ansluta till den virtuella datorn via en fjärranslutning om du har konfigurerat Active Directory i ett virtuellt nätverk. |
| [Skapa en SQL-inloggning](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Om du använder SQL-autentisering, måste en SQL-inloggning med ett användarnamn och lösenord som också har behörighet till måldatabasen. |
| [Aktivera TCP/IP-protokollet](#manualTCP) | SQL Server måste tillåta anslutningar via TCP. |
| [Aktivera brandväggsregel för SQL Server-port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Brandväggen på den virtuella datorn måste tillåta inkommande trafik på SQL Server-porten (standard 1433). |
| [Skapa en nätverkssäkerhetsgruppregel för TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Du måste tillåta den virtuella datorn ska ta emot trafik på SQL Server-porten (standard 1433) om du vill ansluta via internet. Lokala och virtuella nätverk-endast anslutningar kräver inte detta. Det här är det enda steg som krävs i Azure-portalen. |

> [!TIP]
> Stegen i tabellen ovan är klar för dig när du konfigurerar anslutningen i portalen. Endast använda de här stegen att bekräfta din konfiguration eller Ställ in anslutning manuellt för SQL Server.

## <a name="next-steps"></a>Nästa steg

Etablering anvisningar tillsammans med de här stegen för anslutning finns i [etablera en SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

Andra ämnen som rör som kör SQL Server i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).