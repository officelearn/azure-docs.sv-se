---
title: Säkerhetsöverväganden för SQLServer i Azure | Microsoft Docs
description: Det här avsnittet innehåller allmänna riktlinjer för att skydda SQL Server som körs i en Azure virtuell dator.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 69b6bd07699d179fc87ac6c5364a7a34b23d14eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477561"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Säkerhetsöverväganden för SQL Server på Azure Virtual Machines

Det här avsnittet innehåller allmänna riktlinjer för säkerhet som hjälper att upprätta säker åtkomst till SQL Server-instanser i en Azure-dator (VM).

Azure följer flera branschstandarder och bestämmelser som gör det möjligt att skapa en lösning som är kompatibel med SQL Server som körs på en virtuell dator. Information om regelefterlevnad med Azure finns i [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Kontrollera åtkomst till SQL-VM

Överväg noggrant kontrollera vem som har åtkomst till datorn och till SQL Server när du skapar din SQL Server-dator. I allmänhet bör du göra följande:

- Begränsa åtkomsten till SQL Server till endast de program och klienter som behöver den.
- Följ de rekommenderade säkerhetsmetoderna för att hantera användarkonton och lösenord.

Följande avsnitt innehåller förslag på tänka igenom de här punkterna.

## <a name="secure-connections"></a>Säkra anslutningar

När du skapar en SQL Server-dator med en galleriavbildningen den **SQL Server-anslutning** alternativet kan du välja **lokala (inuti VM)**, **privat (inom virtuellt nätverk)**, eller **offentlig (Internet)**.

![SQL Server-anslutning](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

För bästa säkerhet, väljer du det mest restriktiva alternativet för ditt scenario. Till exempel om du kör ett program som ansluter till SQL Server på samma virtuella dator, sedan **lokala** är det säkraste alternativet. Om du kör ett Azure-program som kräver åtkomst till SQL Server kan sedan **privata** skyddar kommunikation till SQL Server inom den angivna [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Om du behöver **offentliga** (internet) åtkomst till SQL Server-dator och sedan se till att följa andra rekommendationer i det här avsnittet för att minska dina angreppsytan.

De valda alternativen i portalen använder inkommande säkerhetsregler på Virtuellt datorns [nätverkssäkerhetsgrupp](../../../virtual-network/security-overview.md) (NSG) för att tillåta eller neka nätverkstrafik till den virtuella datorn. Du kan ändra eller skapa nya inkommande NSG-regler för att tillåta trafik till SQL Server-porten (standard 1433). Du kan också ange specifika IP-adresser som ska kunna kommunicera via den här porten.

![Regler för nätverkssäkerhetsgrupp](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Förutom att NSG-regler för att begränsa nätverkstrafik, kan du också använda Windows-brandväggen på den virtuella datorn.

Om du använder slutpunkter med den klassiska distributionsmodellen kan du ta bort några slutpunkter på den virtuella datorn om du inte använder dem. Anvisningar om hur du använder ACL: er med slutpunkter finns i [hantera ACL på en slutpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Detta är inte nödvändigt för virtuella datorer som använder Resource Manager.

Slutligen bör du aktivera krypterade anslutningar för instansen av SQL Server Database Engine i din Azure-dator. Konfigurera SQL server-instans med ett signerat certifikat. Mer information finns i [aktivera krypterade anslutningar till databasmotorn](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) och [anslutningssträngar](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Använda en icke-standardport

Som standard lyssnar SQL Server på en känd port 1433. Konfigurera SQL Server för att lyssna på en icke-standardport, till exempel 1401 för ökad säkerhet. Om du etablerar en SQL Server-avbildning för galleriet i Azure-portalen kan du ange den här porten i den **SQL Server-inställningar** bladet.

Om du vill konfigurera detta efter etablering, har du två alternativ:

- För Resource Manager-VM, kan du välja **konfiguration av SQL Server** från översiktsbladet för virtuell dator. Detta ger ett alternativ för att ändra porten.

  ![TCP-port ändras i portalen](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Du kan manuellt konfigurera porten genom att ansluta via en fjärranslutning till den virtuella datorn för klassiska virtuella datorer eller för SQL Server-datorer som inte etablerats med portalen. Konfigurationsanvisningar finns i [konfigurera en Server så att den lyssnar på en specifik TCP-Port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Om du använder den här manuella metoden måste du också lägga till en Windows-brandväggsregel för att tillåta inkommande trafik på den TCP-porten.

> [!IMPORTANT]
> Att ange en annan port än standardporten är en bra idé om SQL Server-porten är öppen för offentliga internet-anslutningar.

När SQL Server lyssnar på en icke-standardport måste ange du porten när du ansluter. Tänk dig ett scenario där serverns IP-adress är 13.55.255.255 och SQL Server lyssnar på port 1401. För att ansluta till SQL Server, anger du `13.55.255.255,1401` i anslutningssträngen.

## <a name="manage-accounts"></a>Hantera konton

Du vill inte att angripare gissa enkelt kontonamn eller lösenord. Använd följande tips för att:

- Skapa ett unikt lokalt administratörskonto som inte heter **administratör**.

- Använd komplexa starka lösenord för alla dina konton. Läs mer om hur du skapar ett starkt lösenord, [skapar ett starkt lösenord](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) artikeln.

- Som standard väljer Azure Windows-autentisering under installationen av SQL Server-dator. Därför kan den **SA** inloggning har inaktiverats och ett lösenord har tilldelats av installationsprogrammet. Vi rekommenderar att den **SA** inloggning inte ska användas eller aktiverat. Om du måste ha en SQL-inloggning kan du använda en av följande strategier:

  - Skapa ett SQL-konto med ett unikt namn som har **sysadmin** medlemskap. Du kan göra detta från portalen genom att aktivera **SQL-autentisering** under etableringen.

    > [!TIP] 
    > Om du inte aktiverar SQL-autentisering under etableringen, måste du manuellt ändra autentiseringsläge till **SQL Server och Windows-autentiseringsläge**. Mer information finns i [ändra Server-autentiseringsläge](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Om du måste använda den **SA** inloggning, aktivera inloggningen efter etablering och tilldela ett nytt starkt lösenord.

## <a name="follow-on-premises-best-practices"></a>Följ de rekommenderade säkerhetsmetoderna för den lokala

Utöver de metoder som beskrivs i det här avsnittet rekommenderar vi att du granskar och implementera traditionella lokala säkerhetsrutiner om tillämpligt. Mer information finns i [säkerhetsöverväganden för en SQL Server-Installation](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Nästa steg

Om du är intresserad av bästa praxis när det gäller prestanda, se [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Andra ämnen som rör som kör SQL Server i virtuella Azure-datorer, se [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

