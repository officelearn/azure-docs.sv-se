---
title: Säkerhetsaspekter för SQL Server i Azure | Microsoft-dokument
description: Det här avsnittet innehåller allmän vägledning för att skydda SQL Server som körs i en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f5ea0ddff38532b119d8d984f2dabd6d898b44a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031364"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Säkerhetsöverväganden för SQL Server på Azure Virtual Machines

Det här avsnittet innehåller övergripande säkerhetsriktlinjer som hjälper till att upprätta säker åtkomst till SQL Server-instanser i en virtuell Azure-dator (VM).

Azure följer flera branschregler och standarder som gör att du kan skapa en kompatibel lösning med SQL Server som körs på en virtuell dator. Information om regelefterlevnad med Azure finns i [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Kontrollera åtkomsten till SQL VM

När du skapar den virtuella SQL Server-datorn bör du överväga hur du noggrant kan styra vem som har åtkomst till datorn och till SQL Server. I allmänhet bör du göra följande:

- Begränsa åtkomsten till SQL Server till endast de program och klienter som behöver den.
- Följ metodtipsen för att hantera användarkonton och lösenord.

Följande avsnitt ger förslag på att tänka igenom dessa punkter.

## <a name="secure-connections"></a>Säkra anslutningar

När du skapar en virtuell SQL Server-dator med en **galleriavbildning** kan du välja lokal **(inuti virtuell dator),** privat **(inom virtuellt nätverk)** eller Offentligt **(Internet)**.

![ANSLUTNING TILL SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

För bästa säkerhet väljer du det mest restriktiva alternativet för ditt scenario. Om du till exempel kör ett program som använder SQL Server på samma virtuella dator är **Lokal** det säkraste valet. Om du kör ett Azure-program som kräver åtkomst till SQL Server, säkrar **Privat** kommunikation till SQL Server endast inom det angivna [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Om du behöver **offentlig** (internet) tillgång till SQL Server VM, se till att följa andra metodtips i det här avsnittet för att minska din attack yta.

De valda alternativen i portalen använder inkommande säkerhetsregler på den virtuella datorns [nätverkssäkerhetsgrupp](../../../virtual-network/security-overview.md) (NSG) för att tillåta eller neka nätverkstrafik till din virtuella dator. Du kan ändra eller skapa nya inkommande NSG-regler så att trafik till SQL Server-porten (standard 1433). Du kan också ange specifika IP-adresser som får kommunicera via den här porten.

![Regler för nätverkssäkerhetsgrupp](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Förutom NSG-regler för att begränsa nätverkstrafiken kan du också använda Windows-brandväggen på den virtuella datorn.

Om du använder slutpunkter med den klassiska distributionsmodellen tar du bort alla slutpunkter på den virtuella datorn om du inte använder dem. Instruktioner om hur du använder ACL med slutpunkter finns i [Hantera åtkomstkontrollistan på en slutpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Detta är inte nödvändigt för virtuella datorer som använder Resurshanteraren.

Överväg slutligen att aktivera krypterade anslutningar för instansen av SQL Server Database Engine i din virtuella Azure-dator. Konfigurera SQL-serverinstans med ett signerat certifikat. Mer information finns i [Aktivera krypterade anslutningar till databasmotorn](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) och [anslutningssträngsyntaxen](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="encryption"></a>Kryptering

Hanterade diskar erbjuder kryptering på serversidan och Azure-diskkryptering. [Server side-kryptering](/azure/virtual-machines/windows/disk-encryption) ger kryptering i vila och skyddar dina data för att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) använder antingen Bitlocker eller DM-Crypt-teknik och integreras med Azure Key Vault för att kryptera både operativsystem och datadiskar. 

## <a name="use-a-non-default-port"></a>Använda en port som inte är standard

Som standard lyssnar SQL Server på en känd port, 1433. Konfigurera SQL Server för ökad säkerhet för att lyssna på en icke-standardport, till exempel 1401. Om du etablerar en SQL Server-galleriavbildning i Azure-portalen kan du ange den här porten i **SQL Server-inställningsbladet.**

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Om du vill konfigurera detta efter etablering har du två alternativ:

- För virtuella resurshanteraren-datorer kan du välja **Säkerhet** från [resursresursen för virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). Detta ger ett alternativ för att ändra porten.

  ![Ändring av TCP-port i portalen](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- För virtuella datorer med klassisk start eller sql server som inte har etablerats med portalen kan du konfigurera porten manuellt genom att fjärransluta till den virtuella datorn. Konfigurationsstegen finns i [Konfigurera en server för att lyssna på en specifik TCP-port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Om du använder den här manuella tekniken måste du också lägga till en Windows-brandväggsregel för att tillåta inkommande trafik på den TCP-porten.

> [!IMPORTANT]
> Att ange en port som inte är standard är en bra idé om SQL Server-porten är öppen för offentliga internetanslutningar.

När SQL Server lyssnar på en icke-standardport måste du ange porten när du ansluter. Tänk dig till exempel ett scenario där serverns IP-adress är 13.55.255.255 och SQL Server lyssnar på port 1401. Om du vill ansluta till `13.55.255.255,1401` SQL Server anger du i anslutningssträngen.

## <a name="manage-accounts"></a>Hantera konton

Du vill inte att angripare enkelt ska gissa kontonamn eller lösenord. Använd följande tips för att hjälpa till:

- Skapa ett unikt lokalt administratörskonto som inte heter **Administratör**.

- Använd komplexa starka lösenord för alla dina konton. Mer information om hur du skapar ett starkt lösenord finns i [Skapa en stark lösenordsartikel.](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password)

- Som standard väljer Azure Windows-autentisering under installationen av virtuella SQL Server-datorer. Därför inaktiveras **SA-inloggningen** och ett lösenord tilldelas av installationen. Vi rekommenderar att **SA-inloggningen** inte ska användas eller aktiveras. Om du måste ha en SQL-inloggning använder du någon av följande strategier:

  - Skapa ett SQL-konto med ett unikt namn som har **sysadmin-medlemskap.** Du kan göra detta från portalen genom att aktivera **SQL-autentisering** under etablering.

    > [!TIP] 
    > Om du inte aktiverar SQL-autentisering under etableringen måste du manuellt ändra autentiseringsläget till **SQL Server och Windows Authentication Mode**. Mer information finns i [Ändra serverautentiseringsläge](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Om du måste **SA** använda SA-inloggningen aktiverar du inloggningen efter etableringen och tilldelar ett nytt starkt lösenord.

## <a name="additional-best-practices"></a>Ytterligare metodtips

Utöver de metoder som beskrivs i det här avsnittet rekommenderar vi att du granskar och implementerar bästa säkerhetspraxis från både traditionella lokala säkerhetsrutiner och bästa praxis för säkerhet för virtuella datorer. 

Mer information om lokala säkerhetsrutiner finns i [Säkerhetshänsyn för en SQL Server-installation](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) och [säkerhetscentret](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Mer information om säkerhet för virtuella datorer finns i [säkerhetsöversikten för virtuella datorer](/azure/security/fundamentals/virtual-machines-overview).


## <a name="next-steps"></a>Efterföljande moment

Om du också är intresserad av metodtips kring prestanda läser du [Metodtips för bästa prestanda för SQL Server i Virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

Mer information om hur du kör SQL Server i virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

