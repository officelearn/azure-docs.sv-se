---
title: Etablering guide för Windows SQL Server-datorer i Azure portal | Microsoft Docs
description: Den här guiden beskriver dina alternativ för att skapa Windows SQL Server 2017-datorer i Azure-portalen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bb051d37f3a1dd82d7d46bfe8b22c2ba1251be85
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259209"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Hur du etablerar en Windows SQL Server-dator i Azure portal

Den här guiden innehåller information om de olika tillgängliga alternativen när du skapar en virtuell Windows SQL Server-dator i Azure-portalen. Den här artikeln beskriver ytterligare konfigurationsalternativ än de [Snabbstart för SQL Server-VM](quickstart-sql-vm-create-portal.md), som är mer via ett möjligt etablering uppgift. 

Använd den här guiden för att skapa din egen SQL Server-dator. Eller använda den som en referens för de tillgängliga alternativen i Azure-portalen.

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a id="select"></a> Galleriavbildningar för SQL Server-dator

När du skapar en SQL Server-dator kan välja du en av flera förkonfigurerade avbildningar från galleriet för virtuella datorer. Följande steg visar hur du väljer du något av SQL Server 2017-avbildningar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt konto.

1. Klicka på **Skapa en resurs** i Azure Portal. Fönstret **Nytt** öppnas.

1. I fönstret **Nytt** klickar du på **Compute** och sedan på **Visa alla**.

1. Skriv**SQL Server 2017** i sökfältet och tryck på RETUR.

1. I filtret listrutorna väljer _Windows Server 2016_ för den **operativsystemet** och välj _Microsoft_ som den **Publisher**. 

     ![Nytt Compute-fönster](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Granska de tillgängliga SQL Server-avbildningarna. Varje avbildning identifierar en version av SQL Server och ett operativsystem.

1. Välj avbildningen med namnet **kostnadsfri SQL Server-licens: SQL Server 2017 Developer på Windows Server 2016**.

   > [!TIP]
   > Utvecklarversionen används i den här genomgången eftersom det är en komplett, kostnadsfri version av SQL Server för Utvecklartestning. Du betalar endast för kostnaden för den VM som körs. Men kan du välja vilken avbildning du vill använda i den här genomgången. En beskrivning av tillgängliga avbildningar finns i den [översikt över SQL Server Windows-datorer](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Licensieringskostnaderna för SQL Server ingår i priset per sekund för den virtuella datorn som du skapar och varierar genom edition och kärnor. SQL Server Developer edition är kostnadsfri för utveckling och testning (inte produktion) och SQL Express är kostnadsfri för enklare arbetsbelastningar (mindre än 1 GB minne med mindre än 10 GB lagringsutrymme). Du kan också bring-your-own-license (BYOL) och betala bara för den virtuella datorn. Dessa avbildningsnamn föregås av {BYOL}. 
   >
   > Mer information om alternativen finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. Under **Välj en distributionsmodell** kontrollerar du att **Resource Manager** är valt. Resource Manager är den rekommenderade distributionsmodellen för nya virtuella datorer. 

1. Välj **Skapa**.


## <a id="configure"></a> Konfigurationsalternativ

Det finns flera flikar för att konfigurera en SQL Server-dator. I den här handboken fokuserar vi på följande: 

| Steg | Beskrivning |
| --- | --- |
| **Grundläggande inställningar** |[Konfigurera grundläggande inställningar](#1-configure-basic-settings) |
| **Valfria funktioner** |[Konfigurera valfria funktioner](#2-configure-optional-features) |
| **SQL Server-inställningar** |[Konfigurera SQL Server-inställningar](#3-configure-sql-server-settings) |
| **Granska + skapa** | [Granska sammanfattningen](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Konfigurera grundläggande inställningar


På den **grunderna** och ange följande information:

* Under **projektinformation**, kontrollera att korrekt prenumeration har valts. 
*  I den **resursgrupp** avsnittet, Välj antingen en befintlig resurs gruppen i listan eller välj **Skapa nytt** att skapa en ny resursgrupp. En resursgrupp är en samling relaterade resurser i Azure (virtuella datorer, lagringskonton, virtuella nätverk osv.). 

    ![Prenumeration](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > En ny resursgrupp är praktiskt om du bara testar eller lär dig om SQL Server-distributioner i Azure. När du är klar med testet tar du bort resursgruppen. När du gör det tas den virtuella datorn och alla resurser som associeras med resursgruppen bort automatiskt. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* Under **instans information**:
    1. Ange ett unikt **virtuellt datornamn**.  
    1. Välj en plats för din **Region**. 
    1. I den här handboken, lämna **tillgänglighetsalternativ** inställd _ingen redundans för infrastruktur som krävs för_. Du hittar mer information om alternativ för tillgänglighet [Azure-regioner och tillgänglighet](../../windows/regions-and-availability.md). 
    1. I den **bild** väljer _kostnadsfri SQL Server-licens: SQL Server 2017 Developer på Windows Server 2016_.  
    1. Välja att **ändra storleken på** för den **storlek** av den virtuella datorn och välj den **A2 grundläggande** erbjuder. Glöm inte att rensa dina resurser när du är klar med att förebygga eventuella oväntade avgifter. Vad gäller produktionsarbetsbelastningar hittar du rekommendationer för datorstorlek och konfiguration i [Prestandametodtips för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

    ![Instansinformation](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Den uppskattade månadskostnaden som visas på sidan **Välj en storlek** omfattar inte SQL Server-licenskostnaden. Den här beräkningen är kostnaden för den virtuella datorn som är fristående. Den här beräkningen är den totala uppskattade kostnaden för Express och Developer-versioner av SQL Server. För andra utgåvor kan du se [sidan med priser för Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and och välja din utgåva av SQL Server. Se även de [Pricing guidance för SQL Server Azure VM](virtual-machines-windows-sql-server-pricing-guidance.md) och [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **administratörskontot**, ange ett användarnamn och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administratörskonto](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Under **regler för inkommande portar**, Välj **valda portar** och välj sedan **RDP (port 3389)** från listrutan. 

   ![Regler för inkommande portar](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurera valfria funktioner

### <a name="disks"></a>Diskar

På den **diskar** konfigurerar alternativ för diskar. 

* Under **OS disktyp**, Välj typ av disk som du vill använda för ditt operativsystem från listrutan. Premium rekommenderas för produktionssystem men är inte tillgänglig för en grundläggande virtuell dator. Ändra storleken på virtuella datorn om du vill använda Premium SSD. 
* Under **Avancerat**väljer **Ja** under användning **Managed Disks**.

   > [!NOTE]
   > Microsoft rekommenderar Managed Disks för SQL Server. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Azure översikten över Managed Disks] [... / managed-diskar – overview.md). Mer information om hanterade diskar i en tillgänglighetsuppsättning finns i [Använd hanterade diskar för virtuella datorer i tillgänglighetsuppsättning] (.. /Manage-Availability.MD.

![Inställningar för SQL VM-Disk](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Nätverk

På den **nätverk** konfigurerar alternativ för nätverksfunktioner. 

* Skapa en ny **virtuellt nätverk**, eller Använd ett befintligt vNet för din SQL Server-VM. Ange en **undernät** samt. 

* Under **NIC säkerhetsgrupp**, väljer du en grundläggande säkerhetsgrupp eller gruppen avancerad säkerhet. Välja det grundläggande alternativet kan du välja ingående portar för SQL Server-dator (samma värden som har konfigurerats på den **grundläggande** fliken). Det avancerade alternativet kan du välja en befintlig nätverkssäkerhetsgrupp eller skapa en ny. 

* Du kan göra andra ändringar i inställningar för nätverk, eller Behåll standardvärdena.

![Nätverksinställningar för SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Övervakning

På den **övervakning** konfigurerar övervakning och automatisk avstängning. 

* Azure aktiverar **Start övervakning** som standard med samma lagringskonto som angetts för den virtuella datorn. Du kan ändra dessa inställningar här, samt hur du aktiverar **OS gästen diagnostik**. 
* Du kan aktivera **System tilldelade hanterad identitet** och **automatisk avstängning** på den här fliken samt. 

![Inställningar för SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurera SQL Server-inställningar

På den **SQL Server-inställningar** konfigurerar specifika inställningar och optimeringar för SQL Server. Följande är de inställningar som du kan konfigurera för SQL Server:



| Inställning |
| --- |
| [Anslutning](#connectivity) |
| [Authentication](#authentication) |
| [Azure Key Vault-integrering](#azure-key-vault-integration) |
| [Storage-konfiguration](#storage-configuration) |
| [Automatisk korrigering](#automated-patching) |
| [Automatisk säkerhetskopiering](#automated-backup) |
| [R Services (avancerad analys)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Anslutning

Under **SQL-anslutning** anger du vilken typ av åtkomst du vill ha till SQL Server-instansen på den här virtuella datorn. För den här genomgången väljer **offentlig (internet)** att tillåta anslutningar till SQL Server från datorer eller tjänster på internet. Det här alternativet konfigurerar Azure automatiskt brandväggen och nätverkssäkerhetsgruppen som tillåter trafik på port som valts.

> [!TIP]
> Som standard lyssnar SQL Server på en känd port, **1433**. För ökad säkerhet kan du ändra porten så att den lyssnar på en icke-standardport, till exempel 1401, i den föregående dialogrutan. Om du ändrar porten, måste du ansluta via den porten från alla klientverktyg, till exempel SSMS.

![SQL VM-säkerhet](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Om du vill ansluta till SQL Server via Internet måste du också aktivera SQL Server-autentisering, som beskrivs i nästa avsnitt.

Om du föredrar att inte aktivera anslutningar till databasmotorn via Internet väljer du något av följande alternativ:

* **Lokalt (endast inuti VM)** om du bara vill tillåta anslutningar till SQL Server inifrån den virtuella datorn.
* **Privat (inom Virtual Network)** om du vill tillåta anslutningar till SQL Server från datorer eller tjänster i samma virtuella nätverk.

I allmänhet kan du förbättra säkerheten genom att välja den mest restriktiva anslutningen som ditt scenario medger. Men alla alternativ kan skyddas med regler för nätverkssäkerhetsgrupper och SQL/Windows-autentisering. Du kan redigera nätverkssäkerhetsgruppen när den virtuella datorn har skapats. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Authentication

Om du kräver SQL Server-autentisering klickar du på **Aktivera** under **SQL-autentisering**.

![SQL Server-autentisering](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Om du planerar att ansluta till SQL Server via internet (alternativet för offentlig anslutning) måste du aktivera SQL-autentisering här. Offentlig åtkomst till SQL Server kräver användning av SQL-autentisering.

Om du aktiverar SQL Server-autentisering anger du ett **inloggningsnamn** och **lösenord**. Det här inloggningsnamnet är konfigurerad som en SQL Server-autentiseringsinloggning och medlem i den **sysadmin** fasta serverrollen. Mer information om autentiseringslägen finns i [Välja ett autentiseringsläge](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Om du inte aktiverar SQL Server-autentisering kan du använda det lokala administratörskontot på den virtuella datorn för att ansluta till SQL Server-instansen.

![SQL Server-autentisering](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

### <a name="azure-key-vault-integration"></a>Azure Key Vault-integrering

Om du vill lagra säkerhetshemligheter i Azure för kryptering klickar du på **Azure Key Vault-integrering** och klickar sedan på **Aktivera**.

![Azure Key Vault-integrering](media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Följande tabell innehåller de parametrar som krävs för att konfigurera Azure Key Vault-integrering.

| PARAMETER | BESKRIVNING | EXEMPEL |
| --- | --- | --- |
| **Key Vault-URL** |Platsen för nyckelvalvet. |https:\//contosokeyvault.vault.azure.net/ |
| **Huvudnamn** |Azure Active Directory-tjänstens huvudnamn. Det här namnet kallas också för klient-ID:t. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Huvudhemlighet** |Azure Active Directory-tjänstens huvudhemlighet. Den här hemligheten kallas även för klienthemligheten. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Namn på autentiseringsuppgifter** |**Namn på autentiseringsuppgift**: AKV-integreringen skapar autentiseringsuppgifter på SQL Server så att den virtuella datorn ska ha åtkomst till nyckelvalvet. Välj ett namn för autentiseringsuppgifterna. |mycred1 |

Mer information finns i [Konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Storage-konfiguration

Under **lagringskonfiguration**väljer **ändra konfiguration** att ange lagringskraven.


> [!NOTE]
> Det här alternativet är inte tillgängligt om du manuellt har konfigurerat den virtuella datorn för användning av standardlagring. Automatisk lagringsoptimering är endast tillgängligt för Premium Storage.

> [!TIP]
> Antalet stopp och den övre gränsen för varje skjutreglage beror på den valda storleken för virtuell dator. En större och kraftfullare virtuell dator kan skalas upp mer.

Du kan ange krav som I/O-åtgärder per sekund (IOPs), genomflöde i MB/s och totalt lagringsutrymme. Konfigurera dessa värden med hjälp av reglagen. Du kan ändra dessa lagringsinställningar baserat på arbetsbelastningen. Portalen beräknar automatiskt antalet diskar som ska anslutas och konfigureras baserat på de här kraven.

Under **Storage optimerat för** väljer du något av följande alternativ:

* **Allmänt** är standardinställningen och har stöd för de flesta arbetsbelastningar.
* **Transaktionell** bearbetning optimerar lagringen för traditionella OLTP-arbetsbelastningar för databaser.
* **Datalagerhantering** optimerar lagringen för analys- och rapporteringsarbetsbelastningar.

![Lagringskonfiguration för SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>SQL Server-licens
Om du är en kund med Software Assurance kan du använda den [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) att använda din egen SQL Server-licens och spara på resurser. 

![SQL VM License](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisk uppdatering

**Automatisk uppdatering** är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Ange en dag i veckan, en tid och längden på en underhållsperiod. Azure utför uppdateringar under den här underhållsperioden. Den virtuella datorns lokala tid används för underhållsperiodens schema. Om du inte vill att Azure ska uppdatera SQL Server och operativsystemet automatiskt klickar du på **Inaktivera**.  

![SQL VM automatisk uppdatering](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Mer information finns i [Automatisk uppdatering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatisk säkerhetskopiering

Aktivera automatiska säkerhetskopieringar för alla databaser under **Automatisk säkerhetskopiering**. Automatisk säkerhetskopiering är inaktiverat som standard.

När du aktiverar automatisk SQL-säkerhetskopiering kan du konfigurera följande inställningar:

* Kvarhållningsperiod (dagar) för säkerhetskopieringar
* Lagringskonto som ska användas för säkerhetskopieringar
* Krypteringsalternativ och lösenord för säkerhetskopieringar
* Säkerhetskopiera systemdatabaser
* Konfigurera schema för säkerhetskopiering

Om du vill kryptera säkerhetskopian klickar du på **Aktivera**. Ange sedan **lösenordet**. Azure skapar ett certifikat för att kryptera säkerhetskopiorna och använder det angivna lösenordet för att skydda certifikatet.

Mer information finns i [Automatisk säkerhetskopiering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="r-services-advanced-analytics"></a>R Services (avancerad analys)

Har du möjlighet att aktivera [SQL Server R Services (Advanced Analytics)](/sql/advanced-analytics/r/sql-server-r-services/). Det här alternativet kan du använda avancerad analys med SQL Server 2017. Klicka på **Aktivera** i fönstret **SQL Server-inställningar**.


## <a name="4-review--create"></a>4. Granska + skapa

På den **granska + skapa** , granskar du sammanfattningen och sedan **skapa** att skapa SQL Server, resursgrupp och resurser som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen.

> [!NOTE]
> För att ge dig en uppfattning om distributionstiden distribuerade jag en virtuell dator med SQL till östra USA med standardinställningar. Det här testdistributionen tog cirka 12 minuter att slutföra. Distributionen kan dock gå snabbare eller långsammare beroende på din region och dina valda inställningar.

## <a id="remotedesktop"></a> Öppna den virtuella datorn med Fjärrskrivbord

Använd följande anvisningar för att ansluta till den virtuella SQL Server-datorn med Fjärrskrivbord:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a id="connect"></a> Fjärransluta till SQL Server

I den här genomgången ska du har valt **offentliga** åtkomst för den virtuella datorn och **SQL Server-autentisering**. Dessa inställningar konfigurerade automatiskt den virtuella datorn så att SQL Server-anslutningar tillåts från alla klienter över Internet (förutsatt att de har rätt SQL-inloggningsuppgifter).

> [!NOTE]
> Om du inte valde Offentlig under etableringen kan du ändra SQL-anslutningsinställningarna via portalen efter etableringen. Mer information hittar du i [Ändra SQL-anslutningsinställningarna](virtual-machines-windows-sql-connect.md#change).

I följande avsnitt visas hur du ansluter via internet till din SQL Server-VM-instans.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Det här exemplet används common-port 1433. Men måste det här värdet ändras om en annan port (till exempel 1401) har angetts under distributionen av SQL Server-dator. 


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder SQL Server i Azure finns i [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) och [Vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md).