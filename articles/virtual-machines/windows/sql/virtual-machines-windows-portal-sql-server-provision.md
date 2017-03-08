---
title: Etablera en virtuell dator med SQL Server | Microsoft Docs
description: "Skapa och ansluta till en virtuell dator med SQL Server i Azure via portalen. I den här kursen används Resource Manager-läget."
services: virtual-machines-windows
documentationcenter: na
author: rothja
editor: 
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 5121b2f9d33ebf4749fae6d990feab3bf9244e93
ms.openlocfilehash: 6b99dbbacd9451285cca29de4a72ffb1473479fc
ms.lasthandoff: 02/28/2017


---
# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Etablera en virtuell dator med SQL Server på Azure Portal
> [!div class="op_single_selector"]
> * [Portalen](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
> 
> 

Den här heltäckande självstudiekursen beskriver hur du etablera en virtuell dator som kör SQL Server med hjälp av Azure Portal.

Galleriet med virtuella Azure-datorer (VM) innehåller flera avbildningar med Microsoft SQL Server. Med några få klick kan du välja en av VM-avbildningarna med SQL från galleriet och etablera den i Azure-miljön.

I den här kursen ska du:

* [Välja en VM-avbildning med SQL från galleriet](#select-a-sql-vm-image-from-the-gallery)
* [Konfigurera och skapa den virtuella datorn](#configure-the-vm)
* [Öppna den virtuella datorn med Fjärrskrivbord](#open-the-vm-with-remote-desktop)
* [Fjärransluta till SQL Server](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Välja en VM-avbildning med SQL från galleriet
1. Logga in på [Azure Portal](https://portal.azure.com) med ditt konto.
   
   > [!NOTE]
   > Om du inte har något Azure-konto besöker du sidan för [kostnadsfria utvärderingsversioner av Azure](https://azure.microsoft.com/pricing/free-trial/).
   > 
   > 
2. Klicka på **Nytt** på Azure Portal. Portalen öppnas på bladet **Nytt**. VM-resurserna med SQL Server finns i gruppen **Compute** på Marketplace.
3. I det **Nya** bladet klickar du på **Compute** och sedan på **Se alla**.
4. I textrutan **Filter** anger du ”SQL Server” och trycker på RETUR-tangenten.

   ![Blad för Azure Virtual Machines](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

5. Granska de tillgängliga SQL Server-mallarna. Varje mall identifierar en version av SQL Server och ett operativsystem. 
6. Välj mallen för SQL Server 2016 SP1 Developer i Windows Server 2016.

   > [!TIP]
   > Vi använder Developer-versionen i den här självstudiekursen eftersom det är en komplett version av SQL Server som är kostnadsfri i samband med utvecklingstester. Du betalar endast för kostnaden för den VM som körs.
   
   > [!NOTE]
   > Licenskostnaden för SQL Server ingår i minutpriset för den VM du skapar (förutom för versionerna Developer och Express). SQL Server Developer är kostnadsfri när den används till utveckling och testning (inte i samband med produktion) och SQL Express är kostnadsfri för enklare arbetsbelastningar (mindre än 1 GB minne och mindre än 10 GB lagringsutrymme). Ett annat alternativ är att använda en egen licens (BYOL, bring-your-own-license) och endast betala för den virtuella datorn. Dessa avbildningsnamn föregås av {BYOL}. Mer information om det här alternativet finns i [Komma igång med SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
   > 
   > 
7. Under **Välj en distributionsmodell** kontrollerar du att **Resource Manager** är valt. Resource Manager är den rekommenderade distributionsmodellen för nya virtuella datorer. Klicka på **Skapa**.
   
    ![Skapa den virtuella SQL-datorn med Resource Manager](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Konfigurera den virtuella datorn
Det finns fem tillgängliga blad för att konfigurera en virtuell dator med SQL Server.

| Steg | Beskrivning |
| --- | --- |
| **Grundläggande inställningar** |[Konfigurera grundläggande inställningar](#1-configure-basic-settings) |
| **Storlek** |[Välj den virtuella datorns storlek](#2-choose-virtual-machine-size) |
| **Inställningar** |[Konfigurera valfria funktioner](#3-configure-optional-features) |
| **SQL Server-inställningar** |[Konfigurera SQL Server-inställningar](#4-configure-sql-server-settings) |
| **Sammanfattning** |[Granska sammanfattningen](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Konfigurera grundläggande inställningar
Ange följande information i bladet **Grundläggande inställningar**:

* Ange ett unikt **namn** för den virtuella datorn.
* Ange ett **användarnamn** för det lokala administratörskontot på den virtuella datorn. Det här kontot läggs också till i den fasta SQL Server-serverrollen **sysadmin**.
* Ange ett starkt **lösenord**.
* Om du har flera prenumerationer kontrollerar du att prenumerationen är rätt för den nya virtuella datorn.
* I rutan **Resursgrupp** skriver du ett namn för en ny resursgrupp. Du kan även använda en befintlig resursgrupp genom att klicka på **Använd befintlig**. En resursgrupp är en samling relaterade resurser i Azure (virtuella datorer, lagringskonton, virtuella nätverk osv.).
  
  > [!NOTE]
  > En ny resursgrupp är praktiskt om du bara testar eller lär dig om SQL Server-distributioner i Azure. När du är klar med testet tar du bort resursgruppen. När du gör det tas den virtuella datorn och alla resurser som associeras med resursgruppen bort automatiskt. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).
  > 
  > 
* Välj en **plats** för den här distributionen.
* Spara inställningarna genom att klicka på **OK**.
  
    ![Grundläggande SQL-inställningar](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Välj den virtuella datorns storlek
I steget **Storlek** väljer du en storlek för den virtuella dator i bladet **Välj en storlek**. Bladet visar rekommenderade datorstorlekar baserat på den mall du valt. Du ser också den uppskattade månadskostnaden för att köra den virtuella datorn.

![Storleksalternativ för en virtuell dator med SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

För produktionsarbetsbelastningar rekommenderar vi att du väljer en storlek på den virtuella datorn som har stöd för [Premium Storage](../../../storage/storage-premium-storage.md). Om du inte behöver den prestandanivån använder du knappen **Visa alla**, som visar alla storleksalternativ för datorer. Du kan till exempel använda en mindre datorstorlek för en utvecklings- eller testmiljö.

> [!NOTE]
> Mer information om storlekar för virtuella datorer finns i [Storlekar för virtuella datorer](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Saker att tänka på när du väljer storlek för virtuella datorer med SQL Server finns i [Prestandarelaterade metodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).
> 
> 

Välj din datorstorlek och klicka på **Välj**.

## <a name="3-configure-optional-features"></a>3. Konfigurera valfria funktioner
I bladet **Inställningar** konfigurerar du lagring, nätverk och övervakning för den virtuella datorn.

* Under **Storage** anger du en **disktyp**, antingen Standard eller Premium (SSD). Premium Storage rekommenderas för produktionsarbetsbelastningar.

> [!NOTE]
> Om du väljer Premium (SSD) för en datorstorlek som inte stöder Premium Storage ändras datorstorleken automatiskt.  
> 
> 

* Under **Storage-konto**kan du acceptera det automatiskt etablerade lagringskontonamnet. Du kan också klicka på **Lagringskonto** och välja ett befintligt konto och konfigurera lagringskontotypen. Som standard skapar Azure ett nytt lagringskonto med lokalt redundant lagring. Mer information om lagringsalternativ finns i [Azure Storage-replikering](../../../storage/storage-redundancy.md).
* Under **Nätverk** kan du acceptera de automatiskt ifyllda värdena. Du kan också klicka på varje funktion för att manuellt konfigurera det **virtuella nätverket**, **undernätet**, den **offentliga IP-adressen** och **nätverkssäkerhetsgruppen**. Behåll standardinställningarna i den här kursen.
* Azure aktiverar **övervakning** som standard med samma lagringskonto som används för den virtuella datorn. Du kan ändra dessa inställningar här.
* Ange en tillgänglighetsuppsättning under **Tillgänglighetsuppsättning**. I den här kursen kan du välja **Ingen**. Om du planerar att konfigurera SQL AlwaysOn-tillgänglighetsgrupper konfigurerar du tillgängligheten för att undvika att behöva återskapa den virtuella datorn.  Mer information finns i [Hantera tillgängligheten för Virtual Machines](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

När du har konfigurerat dessa inställningar klickar du på **OK**.

## <a name="4-configure-sql-server-settings"></a>4. Konfigurera SQL Server-inställningar
I bladet **SQL Server-inställningar** konfigurerar du specifika inställningar och optimeringar för SQL Server. Du kan bland annat konfigurera följande inställningar för SQL Server.

| Inställning |
| --- |
| [Anslutning](#connectivity) |
| [Autentisering](#authentication) |
| [Storage-konfiguration](#storage-configuration) |
| [Automatisk uppdatering](#automated-patching) |
| [Automatisk säkerhetskopiering](#automated-backup) |
| [Azure Key Vault-integrering](#azure-key-vault-integration) |
| [R-tjänster](#r-services) |

### <a name="connectivity"></a>Anslutning
Under **SQL-anslutning** anger du vilken typ av åtkomst du vill ha till SQL Server-instansen på den här virtuella datorn. I den här kursen väljer du **Offentlig (Internet)** för att tillåta anslutningar till SQL Server från datorer eller tjänster på Internet. När det här alternativet är valt konfigurerar Azure automatiskt brandväggen och nätverkssäkerhetsgruppen så att trafik tillåts på port 1433.  

![SQL-anslutningsalternativ](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Om du vill ansluta till SQL Server via Internet måste du också aktivera SQL Server-autentisering, som beskrivs i nästa avsnitt.

> [!NOTE]
> Du kan lägga till fler begränsningar för nätverkskommunikationen till den virtuella datorn med SQL Server. Du kan göra det genom att redigera nätverkssäkerhetsgruppen när den virtuella datorn har skapats. Mer information finns i [Vad är en nätverkssäkerhetsgrupp?](../../../virtual-network/virtual-networks-nsg.md)
> 
> 

Om du föredrar att inte aktivera anslutningar till databasmotorn via Internet väljer du något av följande alternativ:

* **Lokalt (endast inuti VM)** om du bara vill tillåta anslutningar till SQL Server inifrån den virtuella datorn.
* **Privat (inom Virtual Network)** om du vill tillåta anslutningar till SQL Server från datorer eller tjänster i samma virtuella nätverk.

> [!NOTE]
> Virtuella datoravbildningar för SQL Server Express-versioner aktiverar inte TCP/IP-protokollet automatiskt. Detta gäller även för offentliga och privata anslutningsalternativ. För Express-versioner måste du använda SQL Server Configuration Manager för att [manuellt aktivera TCP/IP-protokollet](#configure-sql-server-to-listen-on-the-tcp-protocol) när du har skapat den virtuella datorn.
> 
> 

I allmänhet kan du förbättra säkerheten genom att välja den mest restriktiva anslutningen som ditt scenario medger. Men alla alternativ kan skyddas med regler för nätverkssäkerhetsgrupper och SQL/Windows-autentisering.

Standardinställningen för **Port** är 1433. Du kan ange ett annat portnummer.
Mer information finns i [Ansluta till en virtuell dator med SQL Server (Resource Manager) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Autentisering
Om du kräver SQL Server-autentisering klickar du på **Aktivera** under **SQL-autentisering**.

![SQL Server-autentisering](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Om du planerar att ansluta till SQL Server via Internet (dvs. alternativet för offentlig anslutning) måste du aktivera SQL-autentisering här. Offentlig åtkomst till SQL Server kräver användning av SQL-autentisering.
> 
> 

Om du aktiverar SQL Server-autentisering anger du ett **inloggningsnamn** och **lösenord**. Det här användarnamnet är konfigurerat som en inloggning med SQL Server-autentisering och som en medlem i den fasta serverrollen **sysadmin**. Mer information om autentiseringslägen finns i [Välja ett autentiseringsläge](http://msdn.microsoft.com/library/ms144284.aspx).

Om du inte aktiverar SQL Server-autentisering kan du använda det lokala administratörskontot på den virtuella datorn för att ansluta till SQL Server-instansen.

### <a name="storage-configuration"></a>Storage-konfiguration
Klicka på **Storage-konfiguration** för att ange lagringskraven.

![SQL Storage-konfiguration](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Det här alternativet är inte tillgängligt om du väljer standardlagring. Automatisk lagringsoptimering är endast tillgängligt för Premium Storage.
> 
> 

Du kan ange krav som I/O-åtgärder per sekund (IOPs), genomflöde i MB/s och totalt lagringsutrymme. Konfigurera dessa värden med hjälp av reglagen. Portalen beräknar automatiskt antalet diskar baserat på dessa krav.

Som standard optimerar Azure lagringen för 5 000 IOPs, 200 MB och 1 TB lagringsutrymme. Du kan ändra dessa lagringsinställningar baserat på arbetsbelastningen. Under **Storage optimerat för** väljer du något av följande alternativ:

* **Allmänt** är standardinställningen och har stöd för de flesta arbetsbelastningar.
* **Transaktionell** bearbetning optimerar lagringen för traditionella OLTP-arbetsbelastningar för databaser.
* **Datalagerhantering** optimerar lagringen för analys- och rapporteringsarbetsbelastningar.

> [!NOTE]
> Reglagens övre gräns varierar beroende på vilken storlek du valt för den virtuella datorn.
> 
> 

### <a name="automated-patching"></a>Automatisk uppdatering
**Automatisk uppdatering** är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Ange en dag i veckan, en tid och längden på en underhållsperiod. Azure utför uppdateringar under den här underhållsperioden. Den virtuella datorns lokala tid används för underhållsperiodens schema. Om du inte vill att Azure ska uppdatera SQL Server och operativsystemet automatiskt klickar du på **Inaktivera**.  

![Automatisk SQL-uppdatering](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Mer information finns i [Automatisk uppdatering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatisk säkerhetskopiering
Aktivera automatiska säkerhetskopieringar för alla databaser under **Automatisk säkerhetskopiering**. Automatisk säkerhetskopiering är inaktiverat som standard.

När du aktiverar automatisk SQL-säkerhetskopiering kan du konfigurera följande:

* Kvarhållningsperiod (dagar) för säkerhetskopieringar
* Lagringskonto som ska användas för säkerhetskopieringar
* Krypteringsalternativ och lösenord för säkerhetskopieringar
* Säkerhetskopiera systemdatabaser
* Konfigurera schema för säkerhetskopiering

Om du vill kryptera säkerhetskopian klickar du på **Aktivera**. Ange sedan **lösenordet**. Azure skapar ett certifikat för att kryptera säkerhetskopiorna och använder det angivna lösenordet för att skydda certifikatet.

![Automatisk SQL-säkerhetskopiering](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup2.png)

 Mer information finns i [Automatisk säkerhetskopiering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Azure Key Vault-integrering
Om du vill lagra säkerhetshemligheter i Azure för kryptering klickar du på **Azure Key Vault-integrering** och klickar sedan på **Aktivera**.

![SQL Azure Key Vault-integrering](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

Följande tabell innehåller de parametrar som krävs för att konfigurera Azure Key Vault-integrering.

| PARAMETER | BESKRIVNING | EXEMPEL |
| --- | --- | --- |
| **Key Vault-URL** |Platsen för nyckelvalvet. |https://contosokeyvault.vault.azure.net/ |
| **Huvudnamn** |Azure Active Directory-tjänstens huvudnamn. Det här namnet kallas också för klient-ID:t. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Huvudhemlighet** |Azure Active Directory-tjänstens huvudhemlighet. Den här hemligheten kallas även för klienthemligheten. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Namn på autentiseringsuppgifter** |**Namn på autentiseringsuppgifter**: AKV-integreringen skapar autentiseringsuppgifter på SQL-servern som gör att den virtuella datorn kan komma åt nyckelvalvet. Välj ett namn för autentiseringsuppgifterna. |mycred1 |

Mer information finns i [Konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines](virtual-machines-windows-ps-sql-keyvault.md).

När du har konfigurerat inställningarna för SQL Server klickar du på **OK**.

### <a name="r-services"></a>R Services
Du kan välja att aktivera [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx). Det ger dig möjlighet att använda avancerad analys med SQL Server 2016. Klicka på **Aktivera** på bladet **SQL Server-inställningar**.

![Aktivera SQL Server R Services](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)


## <a name="5-review-the-summary"></a>5. Granska sammanfattningen
I bladet **Sammanfattning** granskar du sammanfattningen och klickar på **OK** för att skapa SQL Server, resursgruppen och resurserna som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen.

> [!NOTE]
> För att ge dig en uppfattning om distributionstiden distribuerade jag en virtuell dator med SQL till östra USA med standardinställningar. Den här testdistributionen tog totalt 26 minuter. Distributionen kan dock gå snabbare eller långsammare beroende på din region och dina valda inställningar.
> 
> 

## <a name="open-the-vm-with-remote-desktop"></a>Öppna den virtuella datorn med Fjärrskrivbord
Använd följande steg för att ansluta till den virtuella datorn med Fjärrskrivbord:

1. När den virtuella Azure-datorn har skapats visas ikonen för den virtuella datorn på instrumentpanelen i Azure. Du kan också hitta den genom att bläddra bland dina befintliga virtuella datorer. Klicka på den nya virtuella SQL-datorn. Bladet **Virtuell dator** visar information om din virtuella dator.
2. Klicka på **Anslut** längst upp i bladet **Virtuell dator**.
3. Webbläsaren laddar ned RDP-filen för den virtuella datorn. Öppna RDP-filen.
    ![Fjärrskrivbord till virtuell SQL-dator](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
4. Anslutning till fjärrskrivbord meddelar dig att det inte går att identifiera utgivaren av fjärranslutningen. Anslut genom att klicka på **Anslut**.
5. Klicka på **Använd ett annat konto** i dialogrutan **Windows-säkerhet**.
6. För **Användarnamn** skriver du **\<användarnamn >**, där <user name> är det användarnamn som du angav när du konfigurerade den virtuella datorn. Du måste lägga till ett inledande omvänt snedstreck före namnet.
7. Skriv det **lösenord** som du konfigurerade tidigare för den här virtuella datorn och klicka sedan på **OK** för att ansluta.
8. Om en annan dialogruta av typen **Anslutning till fjärrskrivbord** frågar om du vill ansluta klickar du på **Ja**.

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Fjärransluta till SQL Server
I den här självstudiekursen valde vi **offentlig** åtkomst för den virtuella datorn och **SQL Server-autentisering**. Dessa inställningar konfigurerade automatiskt den virtuella datorn så att SQL Server-anslutningar tillåts från alla klienter över Internet (förutsatt att de har rätt SQL-inloggningsuppgifter).

> [!NOTE]
> Om du inte valde Offentlig vid etableringen krävs extra steg för att komma åt SQL Server-instansen över Internet. Mer information finns i [Ansluta till en virtuell SQL Server-dator](virtual-machines-windows-sql-connect.md).
> 
> 

Följande avsnitt visar hur du ansluter till SQL Server-instansen på den virtuella datorn från en annan dator via Internet.

> [!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]
> 
> 

## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder SQL Server i Azure finns i [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) och [Vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md).

Om du vill titta på en videoöversikt över SQL Server på Azure Virtual Machines tittar du på [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016) (Azure VM är den bästa plattformen för SQL Server 2016).

[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure.


