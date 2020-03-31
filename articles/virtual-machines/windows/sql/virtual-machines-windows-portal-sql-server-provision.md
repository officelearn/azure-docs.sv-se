---
title: Etablera virtuell dator med Azure-portal
description: I den här programguiden beskrivs dina alternativ för att skapa virtuella Windows SQL Server 2017-datorer i Azure-portalen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1fdf776570b6f10a363fb98dfe343387d86219d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249794"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Etablera en virtuell Windows SQL Server-dator i Azure-portalen

Den här guiden innehåller information om de olika alternativ som är tillgängliga när du skapar en virtuell Windows SQL Server-dator i Azure-portalen. Den här artikeln innehåller fler konfigurationsalternativ än [snabbstarten för SQL Server VM](quickstart-sql-vm-create-portal.md), som går mer igenom en möjlig etableringsuppgift. 

Använd den här guiden för att skapa en egen VIRTUELL SQL Server.Use this guide to create your own SQL Server VM. Eller använd den som referens för de tillgängliga alternativen i Azure-portalen.

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>Sql Server virtuella dator galleri avbildningar

När du skapar en virtuell SQL Server-dator kan du välja en av flera förkonfigurerade avbildningar från galleriet för virtuella datorer. Följande steg visar hur du väljer en av SQL Server 2017-avbildningarna.

1. Välj **Azure SQL** i menyn till vänster i Azure-portalen. Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Välj **+ Lägg till** om du vill öppna **alternativsidan Välj SQL-distribution.** Du kan visa ytterligare information genom att välja **Visa information**. 
1. Skriv `2017` i sökrutan för SQL Server-avbildning på panelen **virtuella SQL-datorer** och välj sedan **Gratis SQL Server-licens: SQL Server 2017 Developer på Windows Server 2016** i listrutan. 


   ![Välj SQL VM-avbildning](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > Utvecklarutgåvan används i den här genomgången eftersom det är en komplett, gratis utgåva av SQL Server för utvecklingstestning. Du betalar endast för kostnaden för den VM som körs. Du kan dock välja någon av bilderna som ska användas i den här genomgången. En beskrivning av tillgängliga avbildningar finns i [översikten över virtuella SQL Server Windows-datorer](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Licenskostnader för SQL Server ingår i prissättningen per sekund för den virtuella datorn som du skapar och varierar beroende på version och kärnor. SQL Server Developer Edition är dock gratis för utveckling/testning (inte produktion) och SQL Express är gratis för lätta arbetsbelastningar (mindre än 1 GB minne, mindre än 10 GB lagringsutrymme). Du kan också ta med dig självlicens (BYOL) och endast betala för den virtuella datorn. Dessa avbildningsnamn föregås av {BYOL}. 
   >
   > Mer information om alternativen finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).


1. Välj **Skapa**.


## <a name="1-configure-basic-settings"></a>1. Konfigurera grundläggande inställningar


Ange följande information på fliken **Grunderna:**

* Kontrollera att rätt prenumeration är markerad under **Projektinformation.** 
*  I avsnittet **Resursgrupp** väljer du antingen en befintlig resursgrupp i listan eller väljer **Skapa ny** för att skapa en ny resursgrupp. En resursgrupp är en samling relaterade resurser i Azure (virtuella datorer, lagringskonton, virtuella nätverk osv.). 

    ![Prenumeration](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > En ny resursgrupp är praktiskt om du bara testar eller lär dig om SQL Server-distributioner i Azure. När du är klar med testet tar du bort resursgruppen. När du gör det tas den virtuella datorn och alla resurser som associeras med resursgruppen bort automatiskt. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


* Under **Instansinformation:**
    1. Ange ett unikt **namn på en virtuell dator**.  
    1. Välj en plats för **din region**. 
    1. I den här guiden lämnar du **tillgänglighetsalternativ** inställda på _Ingen infrastruktursredundans krävs_. Mer information om tillgänglighetsalternativ finns i [Tillgänglighet](../../windows/availability.md). 
    1. Välj Gratis SQL _Server-licens i listan Bild: SQL Server 2017 Developer på Windows Server 2016_. **Image**  
    1. Välj att **ändra storlek** för **storleken på** den virtuella datorn och välj **A2 Basic-erbjudandet.** Var noga med att rensa upp dina resurser när du är klar med dem för att förhindra oväntade avgifter. Vad gäller produktionsarbetsbelastningar hittar du rekommendationer för datorstorlek och konfiguration i [Prestandametodtips för SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

    ![Information om instans](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Den uppskattade månadskostnaden som visas på sidan **Välj en storlek** omfattar inte SQL Server-licenskostnaden. Den här uppskattningen är kostnaden för den virtuella datorn ensam. För Express- och Utvecklarutgåvorna av SQL Server är den här uppskattningen den totala uppskattade kostnaden. För andra utgåvor kan du se [sidan med priser för Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) and och välja din utgåva av SQL Server. Se även [prisvägledningen för virtuella virtuella SQL Server-azure-datorer](virtual-machines-windows-sql-server-pricing-guidance.md) och [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **Administratörskonto**anger du ett användarnamn och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administratörskonto](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Under **Regler för inkommande port**väljer du Tillåt valda **portar** och väljer sedan **RDP (3389)** i listrutan. 

   ![Regler för inkommande portar](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Konfigurera valfria funktioner

### <a name="disks"></a>Diskar

Konfigurera diskalternativen på fliken **Diskar.** 

* Under **OS-disktyp**väljer du den typ av disk som du vill använda för operativsystemet i listrutan. Premium rekommenderas för produktionssystem men är inte tillgängligt för en grundläggande virtuell dator. Om du vill använda Premium SSD ändrar du storleken på den virtuella datorn. 
* Under **Avancerat**väljer du **Ja** under användning **av Hanterade diskar**.

   > [!NOTE]
   > Microsoft rekommenderar Managed Disks för SQL Server. Managed Disks hanterar lagring i bakgrunden. När virtuella datorer med Managed Disks finns i samma tillgänglighetsuppsättning, distribuerar Azure dessutom lagringsresurser för att tillhandahålla rätt redundans. Mer information finns i [Översikt över hanterade hårddiskar i Azure](../managed-disks-overview.md). Mer information om hanterade diskar i en tillgänglighetsuppsättning finns i [Använda hanterade diskar för virtuella datorer i tillgänglighetsuppsättning](../manage-availability.md).

![Diskinställningar för SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Nätverk

Konfigurera nätverksalternativen på fliken **Nätverk.** 

* Skapa ett nytt **virtuellt nätverk**eller använd ett befintligt virtuellt nätverk för din VIRTUELLA SQL Server-dator. Utse även ett **undernät.** 

* Under säkerhetsgrupp för **nätverkskort**väljer du antingen en grundläggande säkerhetsgrupp eller den avancerade säkerhetsgruppen. Om du väljer det grundläggande alternativet kan du välja inkommande portar för VIRTUELL SQL Server (samma värden som har konfigurerats på fliken **Grundläggande).** Om du väljer det avancerade alternativet kan du välja en befintlig nätverkssäkerhetsgrupp eller skapa en ny. 

* Du kan göra andra ändringar i nätverksinställningarna eller behålla standardvärdena.

![Inställningar för SQL VM-nätverk](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Övervakning

Konfigurera övervakning och automatisk nedskalning på fliken **Övervakning.** 

* Azure aktiverar **Startdiagnostik** som standard med samma lagringskonto som angetts för den virtuella datorn. Du kan ändra dessa inställningar här, samt aktivera **OS gäst diagnostik**. 
* Du kan aktivera **System tilldelade hanterade identitet** och **autoshutdown** på den här fliken också. 

![Hanteringsinställningar för SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurera SQL Server-inställningar

Konfigurera specifika inställningar och optimeringar för SQL Server på fliken **SQL Server-inställningar.** De inställningar som du kan konfigurera för SQL Server innehåller följande:

| Inställning |
| --- |
| [Anslutning](#connectivity) |
| [Autentisering](#authentication) |
| [Integrering av Azure Key Vault](#azure-key-vault-integration) |
| [Storage-konfiguration](#storage-configuration) |
| [Automatisk korrigering](#automated-patching) |
| [Automatisk säkerhetskopiering](#automated-backup) |
| [Maskininlärningstjänster](#machine-learning-services) |


### <a name="connectivity"></a>Anslutning

Under **SQL-anslutning** anger du vilken typ av åtkomst du vill ha till SQL Server-instansen på den här virtuella datorn. Vid den här genomgången väljer du **Offentlig (internet)** för att tillåta anslutningar till SQL Server från datorer eller tjänster på Internet. Med det här alternativet valt konfigurerar Azure automatiskt brandväggen och nätverkssäkerhetsgruppen så att trafik på den valda porten tillåts.

> [!TIP]
> Som standard lyssnar SQL Server på en känd port, **1433**. För ökad säkerhet kan du ändra porten så att den lyssnar på en icke-standardport, till exempel 1401, i den föregående dialogrutan. Om du ändrar porten måste du ansluta med den porten från alla klientverktyg, till exempel SSMS.

![SQL VM-säkerhet](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Om du vill ansluta till SQL Server via Internet måste du också aktivera SQL Server-autentisering, som beskrivs i nästa avsnitt.

Om du föredrar att inte aktivera anslutningar till databasmotorn via Internet väljer du något av följande alternativ:

* **Lokalt (endast inuti VM)** om du bara vill tillåta anslutningar till SQL Server inifrån den virtuella datorn.
* **Privat (inom Virtual Network)** om du vill tillåta anslutningar till SQL Server från datorer eller tjänster i samma virtuella nätverk.

I allmänhet kan du förbättra säkerheten genom att välja den mest restriktiva anslutningen som ditt scenario medger. Men alla alternativ kan skyddas med regler för nätverkssäkerhetsgrupper och SQL/Windows-autentisering. Du kan redigera nätverkssäkerhetsgruppen när den virtuella datorn har skapats. Mer information finns i [Säkerhetsöverväganden för SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Autentisering

Om du behöver SQL Server-autentisering klickar du på **Aktivera** under **SQL-autentisering** på fliken **SQL Server-inställningar.**

![SQL Server-autentisering](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Om du planerar att komma åt SQL Server via internet (alternativet Offentlig anslutning) måste du aktivera SQL-autentisering här. Offentlig åtkomst till SQL Server kräver användning av SQL-autentisering.

Om du aktiverar SQL Server-autentisering anger du ett **inloggningsnamn** och **lösenord**. Det här inloggningsnamnet är konfigurerat som en SQL Server Authentication-inloggning och medlem i **sysadmin-rollen** för fast server. Mer information om autentiseringslägen finns i [Välja ett autentiseringsläge](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Om du inte aktiverar SQL Server-autentisering kan du använda det lokala administratörskontot på den virtuella datorn för att ansluta till SQL Server-instansen.


### <a name="azure-key-vault-integration"></a>Azure Key Vault-integrering

Om du vill lagra säkerhetshemligheter i Azure för kryptering väljer du **SQL Server-inställningar**och bläddrar ned till **Azure key vault-integrering**. Välj **Aktivera** och fyll i den begärda informationen. 

![Azure Key Vault-integrering](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

Följande tabell innehåller de parametrar som krävs för att konfigurera Azure Key Vault-integrering.

| PARAMETER | BESKRIVNING | EXEMPEL |
| --- | --- | --- |
| **Key Vault-URL** |Platsen för nyckelvalvet. |https:\//contosokeyvault.vault.azure.net/ |
| **Huvudnamn** |Azure Active Directory-tjänstens huvudnamn. Det här namnet kallas också för klient-ID:t. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Huvudhemlighet** |Azure Active Directory-tjänstens huvudhemlighet. Den här hemligheten kallas även för klienthemligheten. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Namn på autentiseringsuppgifter** |**Namn på autentiseringsuppgifter**: AKV-integreringen skapar autentiseringsuppgifter på SQL-servern som gör att den virtuella datorn kan komma åt nyckelvalvet. Välj ett namn för autentiseringsuppgifterna. |mycred1 |

Mer information finns i [Konfigurera Azure Key Vault-integrering för SQL Server på Azure Virtual Machines](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Storage-konfiguration

På fliken **SQL Server** under **Lagringskonfiguration**väljer du **Ändra konfiguration** för att öppna sidan Prestandaoptimerad lagringskonfiguration och ange lagringskraven.

![Konfiguration av SQL VM-lagring](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Under **Storage optimerat för** väljer du något av följande alternativ:

* **Allmänt** är standardinställningen och har stöd för de flesta arbetsbelastningar.
* **Transaktionsbearbetning** optimerar lagringen för traditionella OLTP-arbetsbelastningar för databasen.
* **Datalagerhantering** optimerar lagringen för analys- och rapporteringsarbetsbelastningar.

![Konfiguration av SQL VM-lagring](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Du kan välja att lämna värdena som standard, eller så kan du manuellt ändra lagringstopologin så att den passar dina IOPS-behov. Mer information finns i [lagringskonfiguration](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server-licens
Om du är kund hos Software Assurance kan du använda [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) för att ta med din egen SQL Server-licens och spara resurser. 

![SQL VM-licens](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisk uppdatering

**Automatisk uppdatering** är aktiverat som standard. Med inställningen Automatisk uppdatering kan Azure korrigera SQL Server och operativsystemet automatiskt. Ange en dag i veckan, en tid och längden på en underhållsperiod. Azure utför uppdateringar under den här underhållsperioden. Den virtuella datorns lokala tid används för underhållsperiodens schema. Om du inte vill att Azure ska uppdatera SQL Server och operativsystemet automatiskt klickar du på **Inaktivera**.  

![Automatisk korrigering av SQL VM](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Mer information finns i [Automatisk uppdatering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatisk säkerhetskopiering

Aktivera automatiska säkerhetskopieringar för alla databaser under **Automatisk säkerhetskopiering**. Automatisk säkerhetskopiering är inaktiverat som standard.

När du aktiverar automatisk SQL-säkerhetskopiering kan du konfigurera följande inställningar:

* Kvarhållningsperiod (dagar) för säkerhetskopieringar
* Lagringskonto som ska användas för säkerhetskopieringar
* Krypteringsalternativ och lösenord för säkerhetskopieringar
* Säkerhetskopiera systemdatabaser
* Konfigurera schema för säkerhetskopiering

Om du vill kryptera säkerhetskopian klickar du på **Aktivera**. Ange sedan **lösenordet**. Azure skapar ett certifikat för att kryptera säkerhetskopiorna och använder det angivna lösenordet för att skydda certifikatet. Som standard anges schemat automatiskt, men du kan skapa ett manuellt schema genom att välja **Manuell**. 

![Automatiska säkerhetskopieringar av SQL VM](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Mer information finns i [Automatisk säkerhetskopiering av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).


### <a name="machine-learning-services"></a>Machine Learning Services

Du har möjlighet att aktivera [Machine Learning Services](/sql/advanced-analytics/). Med det här alternativet kan du använda maskininlärning med Python och R i SQL Server 2017. Välj **Aktivera** i fönstret **SQL Server-inställningar.**


## <a name="4-review--create"></a>4. Granska + skapa

På fliken **Granska + skapa** granskar du sammanfattningen och väljer **Skapa** för att skapa SQL Server, resursgrupp och resurser som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen.

> [!NOTE]
> För att ge dig en uppfattning om distributionstiden distribuerade jag en virtuell dator med SQL till USA, östra med standardinställningar. Det här testdistributionen tog cirka 12 minuter att slutföra. Distributionen kan dock gå snabbare eller långsammare beroende på din region och dina valda inställningar.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a>Öppna den virtuella datorn med fjärrskrivbord

Använd följande anvisningar för att ansluta till den virtuella SQL Server-datorn med Fjärrskrivbord:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a>Ansluta till SQL Server på distans

I den här genomgången valde du **Offentlig** åtkomst för den virtuella datorn och **SQL Server-autentisering**. Dessa inställningar konfigurerade automatiskt den virtuella datorn så att SQL Server-anslutningar tillåts från alla klienter över Internet (förutsatt att de har rätt SQL-inloggningsuppgifter).

> [!NOTE]
> Om du inte valde Offentlig under etableringen kan du ändra SQL-anslutningsinställningarna via portalen efter etableringen. Mer information hittar du i [Ändra SQL-anslutningsinställningarna](virtual-machines-windows-sql-connect.md#change).

Följande avsnitt visar hur du ansluter via internet till din SQL Server VM-instans.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > I det här exemplet används den gemensamma porten 1433. Det här värdet måste dock ändras om en annan port (till exempel 1401) angavs under distributionen av DEN VIRTUELLA SQL Server-datorn. 


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder SQL Server i Azure finns i [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) och [Vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md).
