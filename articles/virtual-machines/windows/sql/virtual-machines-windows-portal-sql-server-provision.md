---
title: Etablerings guide för Windows SQL Server virtuella datorer i Azure Portal | Microsoft Docs
description: I den här instruktions guiden beskrivs dina alternativ för att skapa virtuella Windows SQL Server 2017-datorer i Azure Portal.
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
ms.openlocfilehash: b1fa24f919888e4454096e1ef84d2ba2948b865a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774299"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Hur du etablerar en virtuell Windows SQL Server-dator i Azure Portal

Den här guiden innehåller information om de olika alternativen som är tillgängliga när du skapar en virtuell Windows SQL Server-dator i Azure Portal. Den här artikeln beskriver fler konfigurations alternativ än [SQL Server VM snabb start](quickstart-sql-vm-create-portal.md), som går igenom en möjlig etablerings aktivitet. 

Använd den här guiden för att skapa en egen SQL Server VM. Eller Använd den som referens för de tillgängliga alternativen i Azure Portal.

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a id="select"></a>SQL Server Galleri avbildningar för virtuella datorer

När du skapar en SQL Server virtuell dator kan du välja en av flera förkonfigurerade avbildningar från galleriet för virtuella datorer. Följande steg visar hur du väljer en av SQL Server 2017-avbildningarna.

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt konto.

1. Klicka på **Skapa en resurs** i Azure Portal. Fönstret **Nytt** öppnas.

1. I fönstret **Nytt** klickar du på **Compute** och sedan på **Visa alla**.

1. Skriv**SQL Server 2017** i sökfältet och tryck på RETUR.

1. I list rutan filter väljer du _Windows Server 2016_ för **operativ systemet** och väljer _Microsoft_ som **utgivare**. 

     ![Nytt Compute-fönster](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Granska de tillgängliga SQL Server-avbildningarna. Varje avbildning identifierar en version av SQL Server och ett operativsystem.

1. Välj avbildningen med **namnet Free SQL Server licens: SQL Server 2017-utvecklare på Windows Server**2016.

   > [!TIP]
   > Developer Edition används i den här genom gången eftersom det är en kostnads fri version av SQL Server för utvecklings testning. Du betalar endast för kostnaden för den VM som körs. Du kan dock välja vilken som helst av de avbildningar som ska användas i den här genom gången. En beskrivning av tillgängliga avbildningar finns i [Översikt över SQL Server virtuella Windows-datorer](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Licens kostnaderna för SQL Server ingår i priset per sekund för den virtuella dator som du skapar och varierar beroende på utgåva och kärnor. SQL Server Developer Edition är dock kostnads fritt för utveckling/testning (inte produktion) och SQL Express är kostnads fritt för lätta arbets belastningar (mindre än 1 GB minne, mindre än 10 GB lagrings utrymme). Du kan också hämta din egen licens (BYOL) och bara betala för den virtuella datorn. Dessa avbildningsnamn föregås av {BYOL}. 
   >
   > Mer information om alternativen finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. Under **Välj en distributionsmodell** kontrollerar du att **Resource Manager** är valt. Resource Manager är den rekommenderade distributionsmodellen för nya virtuella datorer. 

1. Välj **Skapa**.


## <a id="configure"></a>Konfigurations alternativ

Det finns flera flikar för att konfigurera en SQL Server virtuell dator. I den här hand boken kommer vi att fokusera på följande: 

| Steg | Beskrivning |
| --- | --- |
| **Grundläggande inställningar** |[Konfigurera grundläggande inställningar](#1-configure-basic-settings) |
| **Valfria funktioner** |[Konfigurera valfria funktioner](#2-configure-optional-features) |
| **SQL Server-inställningar** |[Konfigurera SQL Server-inställningar](#3-configure-sql-server-settings) |
| **Granska + skapa** | [Granska sammanfattningen](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Konfigurera grundläggande inställningar


Ange följande information på fliken **grundläggande** :

* Under **projekt information**kontrollerar du att rätt prenumeration har valts. 
*  I avsnittet **resurs grupp** väljer du antingen en befintlig resurs grupp i listan eller så skapar du en ny resurs grupp genom att välja **Skapa ny** . En resursgrupp är en samling relaterade resurser i Azure (virtuella datorer, lagringskonton, virtuella nätverk osv.). 

    ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > En ny resursgrupp är praktiskt om du bara testar eller lär dig om SQL Server-distributioner i Azure. När du är klar med testet tar du bort resursgruppen. När du gör det tas den virtuella datorn och alla resurser som associeras med resursgruppen bort automatiskt. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* Under **instans information**:
    1. Ange ett unikt **namn för den virtuella datorn**.  
    1. Välj en plats för din **region**. 
    1. I den här hand boken lämnar du **tillgänglighets alternativ** inställda på _ingen infrastrukturs-redundans krävs_. Om du vill veta mer om tillgänglighets alternativ, se [tillgänglighet](../../windows/availability.md). 
    1. I listan **avbildning** väljer _du gratis SQL Server licens: SQL Server 2017-utvecklare på Windows Server_2016.  
    1. Välj att **ändra storleken** på den virtuella datorn och välj **a2 Basic** -erbjudandet. Se till att rensa dina resurser när du är klar med dem för att förhindra eventuella oväntade kostnader. Vad gäller produktionsarbetsbelastningar hittar du rekommendationer för datorstorlek och konfiguration i [Prestandametodtips för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

    ![Instansinformation](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Den uppskattade månadskostnaden som visas på sidan **Välj en storlek** omfattar inte SQL Server-licenskostnaden. Den här uppskattningen är endast kostnaden för den virtuella datorn. För Express-och Developer-utgåvorna av SQL Server är denna uppskattning den totala beräknade kostnaden. För andra utgåvor kan du se [sidan med priser för Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and och välja din utgåva av SQL Server. Se även [pris vägledningen för SQL Server virtuella Azure-datorer](virtual-machines-windows-sql-server-pricing-guidance.md) och [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **administratörs konto**anger du ett användar namn och ett lösen ord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administratörskonto](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Under **regler för inkommande port**väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)** i list rutan. 

   ![Regler för inkommande portar](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurera valfria funktioner

### <a name="disks"></a>Diskar

Konfigurera disk alternativen på fliken **diskar** . 

* Under **typ av operativ system disk**väljer du den typ av disk som du vill använda för ditt operativ system i list rutan. Premium rekommenderas för produktions system men är inte tillgängligt för en grundläggande virtuell dator. Om du vill använda Premium SSD ändrar du storleken på den virtuella datorn. 
* Under **Avancerat**väljer du **ja** under Använd **Managed disks**.

   > [!NOTE]
   > Microsoft rekommenderar Managed Disks för SQL Server. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Azure Managed Disks Overview] [.. /managed-disks-overview.md). Information om hanterade diskar i en tillgänglighets uppsättning finns i [använda hanterade diskar för virtuella datorer i tillgänglighets uppsättningen] (.. /manage-availability.md.

![Disk inställningar för virtuell SQL-dator](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Nätverk

Konfigurera nätverks alternativen på fliken **nätverk** . 

* Skapa ett nytt **virtuellt nätverk**eller Använd ett befintligt vNet för SQL Server VM. Ange även ett **undernät** . 

* Under **nätverkskort nätverks säkerhets grupp**väljer du antingen en grundläggande säkerhets grupp eller den avancerade säkerhets gruppen. Om du väljer alternativet grundläggande kan du välja inkommande portar för SQL Server VM (samma värden som har kon figurer ATS på fliken **grundläggande** ). Genom att välja alternativet Avancerat kan du välja en befintlig nätverks säkerhets grupp eller skapa en ny. 

* Du kan göra andra ändringar i nätverks inställningarna eller behålla standardvärdena.

![Nätverks inställningar för SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Övervakning

Konfigurera övervakning och automatisk avstängning på fliken **övervakning** . 

* Azure aktiverar **startdiagnostik** som standard med samma lagrings konto som har angetts för den virtuella datorn. Du kan ändra dessa inställningar här, samt aktivera diagnostik för **operativ systemets gäst**. 
* Du kan även aktivera systemtilldelad **hanterad identitet** och automatisk **avstängning** på den här fliken. 

![Hanterings inställningar för virtuella SQL-datorer](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurera SQL Server inställningar

Konfigurera vissa inställningar och optimeringar för SQL Server på fliken **SQL Server inställningar** . De inställningar som du kan konfigurera för SQL Server innehåller följande:



| Inställning |
| --- |
| [Anslutning](#connectivity) |
| [Autentisering](#authentication) |
| [Azure Key Vault-integrering](#azure-key-vault-integration) |
| [Storage-konfiguration](#storage-configuration) |
| [Automatisk uppdatering](#automated-patching) |
| [Automatisk säkerhetskopiering](#automated-backup) |
| [R Services (avancerad analys)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Anslutningar

Under **SQL-anslutning** anger du vilken typ av åtkomst du vill ha till SQL Server-instansen på den här virtuella datorn. I den här genom gången väljer du **offentlig (Internet)** för att tillåta att anslutningar SQL Server från datorer eller tjänster på Internet. När det här alternativet är markerat konfigurerar Azure automatiskt brand väggen och nätverks säkerhets gruppen för att tillåta trafik på den valda porten.

> [!TIP]
> Som standard lyssnar SQL Server på en känd port, **1433**. För ökad säkerhet kan du ändra porten så att den lyssnar på en icke-standardport, till exempel 1401, i den föregående dialogrutan. Om du ändrar porten måste du ansluta med den porten från alla klient verktyg, till exempel SSMS.

![Säkerhet för virtuella SQL-datorer](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Om du vill ansluta till SQL Server via Internet måste du också aktivera SQL Server-autentisering, som beskrivs i nästa avsnitt.

Om du föredrar att inte aktivera anslutningar till databasmotorn via Internet väljer du något av följande alternativ:

* **Lokalt (endast inuti VM)** om du bara vill tillåta anslutningar till SQL Server inifrån den virtuella datorn.
* **Privat (inom Virtual Network)** om du vill tillåta anslutningar till SQL Server från datorer eller tjänster i samma virtuella nätverk.

I allmänhet kan du förbättra säkerheten genom att välja den mest restriktiva anslutningen som ditt scenario medger. Men alla alternativ kan skyddas med regler för nätverkssäkerhetsgrupper och SQL/Windows-autentisering. Du kan redigera nätverkssäkerhetsgruppen när den virtuella datorn har skapats. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Authentication

Om du behöver SQL Server autentisering klickar du på **Aktivera** under **SQL-autentisering** på fliken **SQL Server inställningar** .

![SQL Server-autentisering](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Om du planerar att komma åt SQL Server via Internet (alternativet offentlig anslutning) måste du aktivera SQL-autentisering här. Offentlig åtkomst till SQL Server kräver användning av SQL-autentisering.

Om du aktiverar SQL Server-autentisering anger du ett **inloggningsnamn** och **lösenord**. Det här inloggnings namnet är konfigurerat som en SQL Server inloggning för autentisering och medlem i den fasta Server rollen **sysadmin** . Mer information om autentiseringslägen finns i [Välja ett autentiseringsläge](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Om du inte aktiverar SQL Server-autentisering kan du använda det lokala administratörskontot på den virtuella datorn för att ansluta till SQL Server-instansen.


### <a name="azure-key-vault-integration"></a>Azure Key Vault-integrering

Om du vill lagra säkerhets hemligheter i Azure för kryptering väljer du **SQL Server inställningar**och bläddrar ned till **Azure Key Vault-integrering**. Välj **Aktivera** och fyll i den begärda informationen. 

![Azure Key Vault-integrering](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

Följande tabell innehåller de parametrar som krävs för att konfigurera Azure Key Vault-integrering.

| PARAMETER | BESKRIVNING | EXEMPEL |
| --- | --- | --- |
| **Key Vault-URL** |Platsen för nyckelvalvet. |https:\//contosokeyvault.vault.azure.net/ |
| **Huvudnamn** |Azure Active Directory-tjänstens huvudnamn. Det här namnet kallas också för klient-ID:t. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Huvudhemlighet** |Azure Active Directory-tjänstens huvudhemlighet. Den här hemligheten kallas även för klienthemligheten. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Namn på autentiseringsuppgifter** |**Namn på autentiseringsuppgift**: AKV-integreringen skapar en autentiseringsuppgift i SQL Server, vilket gör det möjligt för den virtuella datorn att ha åtkomst till nyckel valvet. Välj ett namn för autentiseringsuppgifterna. |mycred1 |

Mer information finns i [Konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Storage-konfiguration

På fliken **SQL Server inställningar** under lagrings **konfiguration**väljer du **ändra konfiguration** för att ange lagrings kraven.


> [!NOTE]
> Det här alternativet är inte tillgängligt om du manuellt har konfigurerat den virtuella datorn för användning av standardlagring. Automatisk lagringsoptimering är endast tillgängligt för Premium Storage.

> [!TIP]
> Antalet stopp och den övre gränsen för varje skjutreglage beror på den valda storleken för virtuell dator. En större och kraftfullare virtuell dator kan skalas upp mer.

Du kan ange krav som I/O-åtgärder per sekund (IOPs), genomflöde i MB/s och totalt lagringsutrymme. Konfigurera dessa värden med hjälp av reglagen. Du kan ändra dessa lagringsinställningar baserat på arbetsbelastningen. Portalen beräknar automatiskt antalet diskar som ska anslutas och konfigureras baserat på de här kraven.

Under **Storage optimerat för** väljer du något av följande alternativ:

* **Allmänt** är standardinställningen och har stöd för de flesta arbetsbelastningar.
* **Transaktionell** bearbetning optimerar lagringen för traditionella OLTP-arbetsbelastningar för databaser.
* **Datalagerhantering** optimerar lagringen för analys- och rapporteringsarbetsbelastningar.

![Konfiguration av SQL VM-lagring](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>SQL Server License
Om du är Software Assurance-kund kan du använda [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för att ta med din egen SQL Server licens och spara resurser. 

![SQL VM-licens](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisk uppdatering

**Automatisk uppdatering** är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Ange en dag i veckan, en tid och längden på en underhållsperiod. Azure utför uppdateringar under den här underhållsperioden. Den virtuella datorns lokala tid används för underhållsperiodens schema. Om du inte vill att Azure ska uppdatera SQL Server och operativsystemet automatiskt klickar du på **Inaktivera**.  

![Automatisk uppdatering av SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Mer information finns i [Automatisk uppdatering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatisk säkerhetskopiering

Aktivera automatiska säkerhetskopieringar för alla databaser under **Automatisk säkerhetskopiering**. Automatisk säkerhetskopiering är inaktiverat som standard.

När du aktiverar automatisk SQL-säkerhetskopiering kan du konfigurera följande inställningar:

* Kvarhållningsperiod (dagar) för säkerhetskopieringar
* Lagringskonto som ska användas för säkerhetskopieringar
* Krypteringsalternativ och lösenord för säkerhetskopieringar
* Säkerhetskopiera systemdatabaser
* Konfigurera schema för säkerhetskopiering

Om du vill kryptera säkerhetskopian klickar du på **Aktivera**. Ange sedan **lösenordet**. Azure skapar ett certifikat för att kryptera säkerhetskopiorna och använder det angivna lösenordet för att skydda certifikatet. Som standard anges schemat automatiskt, men du kan skapa ett manuellt schema genom att välja **manuell**. 

![Automatisk säkerhets kopiering av SQL VM](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Mer information finns i [Automatisk säkerhetskopiering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="r-services-advanced-analytics"></a>R Services (avancerad analys)

Du kan välja att aktivera [SQL Server R Services (avancerad analys)](/sql/advanced-analytics/r/sql-server-r-services/). Med det här alternativet kan du använda avancerad analys med SQL Server 2017. Välj **Aktivera** i fönstret **SQL Server inställningar** .


## <a name="4-review--create"></a>4. Granska + skapa

På fliken **Granska + skapa** granskar du sammanfattningen och väljer **skapa** för att skapa SQL Server, resurs grupp och resurser som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen.

> [!NOTE]
> För att ge dig en uppfattning om distributionstiden distribuerade jag en virtuell dator med SQL till östra USA med standardinställningar. Det här testdistributionen tog cirka 12 minuter att slutföra. Distributionen kan dock gå snabbare eller långsammare beroende på din region och dina valda inställningar.

## <a id="remotedesktop"></a> Öppna den virtuella datorn med Fjärrskrivbord

Använd följande anvisningar för att ansluta till den virtuella SQL Server-datorn med Fjärrskrivbord:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a id="connect"></a> Fjärransluta till SQL Server

I den här genom gången valde du **offentlig** åtkomst för den virtuella datorn och **SQL Server autentisering**. Dessa inställningar konfigurerade automatiskt den virtuella datorn så att SQL Server-anslutningar tillåts från alla klienter över Internet (förutsatt att de har rätt SQL-inloggningsuppgifter).

> [!NOTE]
> Om du inte valde Offentlig under etableringen kan du ändra SQL-anslutningsinställningarna via portalen efter etableringen. Mer information hittar du i [Ändra SQL-anslutningsinställningarna](virtual-machines-windows-sql-connect.md#change).

I följande avsnitt visas hur du ansluter via Internet till din SQL Server VM-instans.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > I det här exemplet används den gemensamma porten 1433. Det här värdet måste dock ändras om en annan port (till exempel 1401) angavs under distributionen av SQL Server VM. 


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder SQL Server i Azure finns i [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) och [Vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md).