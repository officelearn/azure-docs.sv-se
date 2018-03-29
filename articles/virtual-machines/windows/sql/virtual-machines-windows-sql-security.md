---
title: Säkerhetsaspekter för SQLServer i Azure | Microsoft Docs
description: Det här avsnittet innehåller allmänna riktlinjer för att skydda SQL Server som körs i en virtuell dator i Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
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
ms.author: jroth
ms.openlocfilehash: b91638b6b76675711150323bee4aa9ad9d9a73d1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Säkerhetsöverväganden för SQL Server på Azure Virtual Machines

Det här avsnittet innehåller allmänna riktlinjer att upprätta säker åtkomst till SQL Server-instanser i en Azure virtuell dator (VM).

Azure uppfyller flera industrins föreskrifter och standarder som gör att du kan skapa en lösning som är kompatibel med SQL Server som körs på en virtuell dator. Information om regelefterlevnad med Azure finns [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Kontrollera åtkomst till SQL-VM

När du skapar den virtuella datorn för SQL Server, kan du överväga hur noggrant kontrollera vem som har åtkomst till datorn och SQL Server. I allmänhet bör du göra följande:

- Begränsa åtkomsten till SQL Server till program och klienter som behöver den.
- Följ Metodtips för hantering av användarkonton och lösenord.

Följande avsnitt innehåller förslag på tänka igenom dessa punkter.

## <a name="secure-connections"></a>Säkra anslutningar

När du skapar en virtuell dator med SQL Server med en avbildning av galleriet, den **SQL Server-anslutning** alternativet kan du välja **lokala (inuti VM)**, **privat (inom Virtual Network)**, eller **offentlig (Internet)**.

![SQL Server-anslutning](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

För bästa säkerhet mest restriktiva välja för ditt scenario. Till exempel om du kör ett program som har åtkomst till SQL Server på samma VM sedan **lokala** är säkrast. Om du kör ett Azure-program som kräver åtkomst till SQL Server, sedan **privata** skyddar kommunikation till SQL Server inom den angivna [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Om du behöver **offentliga** (internet) åtkomst till SQL Server-VM ska du se till att följa övriga rekommendationer i det här avsnittet för att minska din angreppsytan.

De valda alternativen i portalen använder inkommande säkerhetsregler på de virtuella datorerna [Nätverkssäkerhetsgruppen](../../../virtual-network/virtual-networks-nsg.md) (NSG) för att tillåta eller neka trafik till den virtuella datorn. Du kan ändra eller skapa nya regler för inkommande NSG för att tillåta trafik till SQL Server-porten (standard 1433). Du kan också ange specifika IP-adresser som ska kunna kommunicera via den här porten.

![Regler för nätverkssäkerhetsgrupp](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Förutom NSG-regler för att begränsa nätverkstrafik, kan du också använda Windows-brandväggen på den virtuella datorn.

Om du använder slutpunkter med den klassiska distributionsmodellen kan du ta bort alla slutpunkter på den virtuella datorn om du inte använder dem. Anvisningar om hur du använder ACL: er med slutpunkter finns [hantera ACL på en slutpunkt](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). Detta är inte nödvändigt för virtuella datorer som använder Resource Manager.

Slutligen bör du aktivera krypterade anslutningar för instansen av SQL Server Database Engine i ditt virtuella Azure-datorn. Konfigurera SQL server-instans med ett signerat certifikat. Mer information finns i [aktivera krypterade anslutningar till databasmotorn](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) och [anslutningssträngar](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Använda en icke-standardport

Som standard lyssnar SQL Server på en känd port 1433. Konfigurera SQL Server för att lyssna på en annan port än standardporten, till exempel 1401 för ökad säkerhet. Om du etablerar en SQL Server-galleriet avbildning i Azure-portalen kan du ange den här porten i den **SQL Server-inställningar** bladet.

Om du vill konfigurera det här när du har etablerat, har du två alternativ:

- För hanteraren för virtuella datorer, kan du välja **SQL Server-konfigurationsfilen** från bladet VM Översikt. Detta ger ett alternativ för att ändra porten.

  ![TCP-port ändra i portalen](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Du kan manuellt konfigurera porten genom att ansluta via en fjärranslutning till den virtuella datorn för klassiska virtuella datorer eller för SQL Server-datorer som inte har etablerats med portalen. Konfigurationsanvisningar finns [konfigurera en Server för att lyssna på en specifik TCP-Port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Om du använder den här manuella metoden måste du också lägga till en regel för Windows-brandväggen för att tillåta inkommande trafik på den TCP-porten.

> [!IMPORTANT]
> Ange en annan port än standardporten är en bra idé om SQL Server-porten är öppen för offentliga internet-anslutningar.

När SQL Server lyssnar på en icke-standardport måste du ange porten när du ansluter. Tänk dig ett scenario där serverns IP-adress är 13.55.255.255 och SQL Server lyssnar på port 1401. För att ansluta till SQL Server, anger du `13.55.255.255,1401` i anslutningssträngen.

## <a name="manage-accounts"></a>Hantera konton

Du vill inte att angripare kan lätt gissa kontonamn eller lösenord. Använd följande tips för att:

- Skapa en unik lokala administratörskontot som inte heter **administratör**.

- Använd komplexa starka lösenord för alla konton. Läs mer om hur du skapar ett starkt lösenord, [skapa ett starkt lösenord](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) artikel.

- Som standard väljer Windows-autentisering i Azure under installationen av SQL Server-dator. Därför kan den **SA** inloggning är inaktiverat och ett lösenord har tilldelats av installationsprogrammet. Vi rekommenderar att den **SA** inloggning inte ska användas eller aktiverat. Om du måste ha en SQL-inloggning med någon av följande strategier:

  - Skapa ett SQL-konto med ett unikt namn som har **sysadmin** medlemskap. Du kan göra detta från portalen genom att aktivera **SQL-autentisering** under etableringen.

    > [!TIP] 
    > Om du inte aktiverar SQL-autentisering under etableringen måste du manuellt ändra autentiseringsläget till **SQL Server och Windows-autentiseringsläge**. Mer information finns i [ändra Server-autentiseringsläge](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Om du måste använda den **SA** inloggning, aktivera inloggningen efter etablering och tilldela ett nytt starkt lösenord.

## <a name="follow-on-premises-best-practices"></a>Följ Metodtips för lokalt

Vi rekommenderar att du granskar och implementera de traditionella lokala säkerhetsmetoderna tillämpliga utöver de metoder som beskrivs i det här avsnittet. Mer information finns i [säkerhetsaspekter för en SQL Server-Installation](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Nästa steg

Om du är intresserad av bästa praxis för prestanda, se [prestandarelaterade Metodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Andra avsnitt relaterade till SQL Server som körs i virtuella Azure-datorer, se [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

