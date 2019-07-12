---
title: Förbereda virtuella VMware-datorer som kör Linux för migrering till Azure med Azure Migrate servermigrering | Microsoft Docs
description: Beskriver hur du förbereder en Linux VM för migrering till Azure med Azure Migrate-servermigrering
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811796"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Förbereda Linux virtuella VMware-datorer för migrering till Azure 

Den här artikeln beskrivs hur förbereda virtuella VMware-datorer som kör Linux när du vill migrera dem till Azure med hjälp av [Azure Migrate](migrate-overview.md). 

> [!NOTE]
> Azure Migrate-servermigrering är för närvarande i offentlig förhandsversion. Du kan använda befintliga GA-versionen av Azure Migrate för att identifiera och utvärdera virtuella datorer för migrering, men den faktiska migreringen stöds inte i den befintliga GA-versionen.

Förbereda Linux-datorer på följande sätt:

1. Installera Hyper-V Linux Integration-tjänster. Nyare versioner av Linux-distributioner kan ha detta installeras som standard).
2. Återskapa Linux init avbildningen så att den innehåller nödvändiga Hyper-V-drivrutiner och så att den virtuella datorn startas i Azure (krävs för vissa distributioner).
3. Aktivera loggning för seriekonsol för felsökning. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. Uppdatera enheten mappningsfilen med enhetsnamnet för att volymen kopplingar, till att använda beständiga enhetsidentifierare.
5. Uppdatera posterna i fstab om du vill använda permanent volym-ID: n.
6. Ta bort alla udev-regler som inte reserverar gränssnittsnamn baserat på MAC-adress osv.
7. Uppdatera nätverksgränssnitt som tar emot DHCP IP-adresser.
8. Se till att ssh är aktiverat. Kontrollera att sshd-tjänsten är inställd att starta automatiskt vid omstart.
9. Se till att inkommande ssh anslutningsförfrågningar inte blockeras av operativsystemet brandväggen eller IP-regler.

[Läs mer](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) om att göra dessa ändringar på de mest populära Linux-distributionerna.

## <a name="sample-script"></a>Exempelskript

Det här skriptet (förbereda-för-azure.sh) innehåller ett exempel för att förbereda Linux-datorer. Skriptet kanske inte fungerar för alla distributioner och miljöer, men det är en användbar utgångspunkt.

Skriptet visar hur du: 

- Återskapa Linux init-avbildning med nödvändiga drivrutiner om det behövs.
- Uppdatera posterna i fstab om du vill använda permanent volym-ID: n.
- Omdirigera konsolloggarna till den seriella porten.
- Aktivera seriell åtkomst i Azure-konsolen
- Ta bort udev net regler
- Mata in en körning på Start-skript som körs när den virtuella datorn startas. Kontrollerar den om datorn körs i Azure. Om det är uppdaterar nätverkskonfigurationen på den virtuella datorn och anger det första ethernet-gränssnittet som du använder DHCP för att få en IP-adress.

### <a name="before-you-start"></a>Innan du börjar

- Exempelskriptet innehåller steg som exempel. Det bör inte köras på system i drift. Det kan skada eller skada den virtuella datorn som den körs.
- Vi rekommenderar att du kör den på en virtuell testdator. Innan du börjar bör du ta en säkerhetskopiering av virtuella datorer eller en ögonblicksbild så att du kan återställa den virtuella datorn om det behövs. 
- Skriptet fungerar när den virtuella datorn kör något av de här Linux-distributioner:
    - Red Hat Enterprise Linux 6.5+, 7.1+
    - Cent OS 6.5+, 7.1+
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Kör skriptet

1. Kopiera skriptet till Linux testa virtuell dator med sftp eller en scp-klient, till exempel FileZilla eller WinScp.
2. SSH till Linux-dator med ett administratörskonto.
3. Gå till katalogen för skriptet.
4. Om du vill göra skriptet till en körbar fil, kör **sudo chmod 777 förbereda-för-azure.sh**.
5. Kör skriptet med **./prepare-for-azure.sh**.

Här är hur skriptet körs:

![Linux-skript](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux-skript ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Linux-skript](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux-skript ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Linux-skript](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux-skript ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Linux-skript](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux-skript ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Linux-skript](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux-skript](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [datorberoende mappning](how-to-create-group-machine-dependencies.md) att skapa grupper med hög exakthet.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
