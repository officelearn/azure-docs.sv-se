---
title: Skapa tekniska till gångar för ett erbjudande för virtuella Azure Marketplace-datorer
description: Lär dig hur du skapar och konfigurerar tekniska till gångar för ett erbjudande för virtuella datorer (VM) för Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a83532e2dd6fc8e83206a3b4a055170b40d131fd
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803525"
---
# <a name="create-technical-assets-for-an-azure-marketplace-virtual-machine-offer"></a>Skapa tekniska till gångar för ett erbjudande för virtuella Azure Marketplace-datorer

När du publicerar dina virtuella dator avbildningar till Azure Marketplace, verifierar Azure-teamet avbildningen av den virtuella datorn för att säkerställa att den är i Start-, säkerhets-och Azure-kompatibilitet. Om något av testerna med hög kvalitet Miss låter det att ett meddelande som innehåller fel och eventuella fel korrigerings [steg](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)Miss förfaller.

Den här artikeln beskriver hur du skapar och konfigurerar tekniska till gångar för ett erbjudande för virtuella datorer (VM) för Azure Marketplace. En virtuell dator innehåller två komponenter: virtuella hård diskar (VHD) för operativ system och valfria associerade data diskar för virtuella hård diskar:

- **Operativ systemets virtuella hård disk**: innehåller det operativ system och den lösning som distribueras med ditt erbjudande. Processen för att förbereda den virtuella hård disken varierar beroende på om det är en Linux-baserad, Windows-baserad eller en anpassad virtuell dator.

- **Data disk-VHD**: dedikerad, beständig lagring för en virtuell dator. Använd inte den virtuella hård disken (t. ex. C: Drive) för att lagra beständig information.

En avbildning av en virtuell dator innehåller en operativ system disk och upp till 16 data diskar. Använd en virtuell hård disk per data disk, även om disken är tom.

> [!NOTE]
> Oavsett vilket operativ system du använder lägger du endast till det minsta antalet data diskar som behövs för lösningen. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen.

> [!IMPORTANT]
> Varje VM-avbildning i en plan måste ha samma antal data diskar.

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet. Förutom din lösnings domän bör ditt tekniska team ha kunskap om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
- Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
- Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking)
- Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Arbeta med kunskaper om [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Valfria rekommenderade verktyg

Överväg att använda någon av följande skript miljöer för att hantera virtuella datorer och virtuella hård diskar:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

Överväg dessutom att lägga till följande verktyg i utvecklings miljön:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Skapa en VM-avbildning med en godkänd bas

Om du vill skapa tekniska till gångar för virtuella datorer med hjälp av en avbildning som du har skapat på dina egna anläggningar, se [skapa en VM-avbildning med en godkänd bas](#create-a-vm-image-using-an-approved-base) nedan.

I det här avsnittet beskrivs olika aspekter av att använda en godkänd bas, till exempel att använda Remote Desktop Protocol (RDP), välja en storlek för den virtuella datorn, installera de senaste Windows-uppdateringarna och generalisera VHD-avbildningen.

Följ anvisningarna i den här artikeln för att använda Azure för att skapa en virtuell dator som innehåller ett förkonfigurerat, godkänt operativ system. Om detta inte är kompatibelt med din lösning, är det möjligt att skapa och konfigurera en lokal virtuell dator med ett godkänt operativ system. Du kan sedan konfigurera och förbereda den för uppladdning enligt beskrivningen i [förbereda en virtuell Windows-VHD eller VHDX för att ladda upp till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Välj en godkänd bas avbildning

Välj antingen Windows operativ system eller Linux som bas.

Operativ systemets virtuella hård disk för din VM-avbildning måste baseras på en Azure-godkänd bas avbildning som innehåller Windows Server eller SQL Server.

När du skickar en begäran om att publicera om din avbildning med uppdateringar kan det hända att test fallet vid verifiering av del nummer Miss lyckas. I den instansen godkänns inte avbildningen.

Detta fel uppstår när du använder en bas avbildning som tillhör en annan utgivare och du har uppdaterat avbildningen. I det här fallet kommer du inte att kunna publicera din avbildning.

Du kan åtgärda det här problemet genom att hämta den senaste avbildningen från Azure Marketplace och göra ändringar i avbildningen. Se följande för att visa godkända bas avbildningar där du kan söka efter din avbildning:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 Data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

Azure erbjuder ett antal godkända Linux-distributioner. En aktuell lista finns i [Linux on distributioner som har godkänts av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Skapa en virtuell dator på Azure Portal

Följ de här stegen för att skapa en bas avbildning av virtuella datorer på [Azure Portal](https://ms.portal.azure.com/):

1. Logga in på [Azure Portal](https://ms.portal.azure.com/) med det Microsoft-konto som är associerat med den Azure-prenumeration som du vill använda för att publicera ditt virtuella dator erbjudande.
2. Skapa en ny resurs grupp och ange **resurs gruppens namn**, **prenumeration**och **plats för resurs gruppen**. Mer information finns i [Hantera resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Visar starten av att skapa en resurs grupp.":::

3. Välj **virtuella datorer** i det vänstra navigerings fältet för att visa sidan med information om virtuella datorer.
4. Välj **+ Lägg** till för att öppna **upplevelsen skapa en virtuell dator**.
5. Välj avbildningen i den nedrullningsbara listan eller Välj **Bläddra bland alla offentliga och privata avbildningar** för att söka eller bläddra bland alla tillgängliga avbildningar av virtuella datorer. Exempel:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Visar starten av att skapa en resurs grupp.":::

6. Välj storleken på den virtuella dator som ska distribueras med följande rekommendationer:
    1. Storleken spelar ingen roll om du planerar att utveckla den virtuella hård disken lokalt. Överväg att använda en av de mindre virtuella datorerna.
    2. Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av de rekommenderade VM-storlekarna för den valda avbildningen.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="Visar starten av att skapa en resurs grupp.":::

7. I avsnittet **diskar** expanderar du avsnittet **Avancerat** och anger alternativet **Använd Managed disks** till **Nej**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Visar starten av att skapa en resurs grupp.":::

8. Ange övrig information som krävs för att skapa den virtuella datorn.
9. Välj **Granska + skapa** för att granska dina val. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

Azure börjar etablering av den virtuella dator som du har angett. Du kan följa förloppet genom att välja fliken **Virtual Machines** till vänster. När den har skapats ändras statusen till **körs**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Skapa en virtuell dator i generation 2 på Azure Portal

Skapa en virtuell dator i generation 2 (Gen2) i Azure Portal.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).
2. Välj **Skapa en resurs**.
3. Välj **Visa alla** från Azure Marketplace till vänster.
4. Välj en avbildning som stöder Gen2.
5. Välj **Skapa**.
6. På fliken **Avancerat** under avsnittet VM- **generering** väljer du alternativet **gen 2** .
7. På fliken **grundläggande** , under **information om instans**, går du till **storlek** och öppnar bladet **Välj en VM-storlek** .
8. Välj en rekommenderad storlek som [stöds för generation 2 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) och storlek.
9. Gå igenom [Azure Portal skapande flödet](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) för att slutföra skapandet av den virtuella datorn.

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Visar starten av att skapa en resurs grupp.":::

## <a name="connect-to-your-azure-vm"></a>Anslut till din virtuella Azure-dator

I det här avsnittet beskrivs hur du ansluter och loggar in på den virtuella datorn som du skapade i Azure. När du har anslutit kan du arbeta med den virtuella datorn som om du var inloggad lokalt på värd servern.

### <a name="connect-to-a-windows-based-vm"></a>Ansluta till en Windows-baserad virtuell dator

Använd fjärr skrivbords klienten för att ansluta till den Windows-baserade virtuella datorn som finns på Azure. De flesta versioner av Windows innehåller inbyggt stöd för Remote Desktop Protocol (RDP). För andra operativ system kan du hitta mer information om klienter i [fjärr skrivbords klienter](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Den här artikeln beskriver hur du använder det inbyggda Windows RDP-stödet för att ansluta till din virtuella dator: [hur du ansluter och loggar in på en virtuell Azure-dator som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Du kan få säkerhets varningar under processen. Exempel: varningar som "RDP-filen kommer från en okänd utgivare" eller "dina användarautentiseringsuppgifter kan inte verifieras". Det är säkert att ignorera dessa varningar.

### <a name="connect-to-a-linux-based-vm"></a>Ansluta till en Linux-baserad virtuell dator

Om du vill ansluta till en Linux-baserad virtuell dator behöver du en SSH-klient (Secure Shell Protocol). I följande steg används den kostnads fria SHH-terminalen för [SparaTillFil](https://www.ssh.com/ssh/putty/) .

1. Gå till [Azure-portalen](https://ms.portal.azure.com/).
2. Sök efter och välj virtuella datorer.
3. Välj den virtuella dator som du vill ansluta till.
4. Starta den virtuella datorn om den inte redan körs.
5. Välj namnet på den virtuella datorn för att öppna dess översikts sida.
6. Anteckna den offentliga IP-adressen och DNS-namnet på den virtuella datorn (om dessa värden inte har angetts måste du [skapa ett nätverks gränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).
7. Öppna programmet SparaTillFil.
8. Ange IP-adressen eller DNS-namnet på den virtuella datorn i dialog rutan SparaTillFil-konfiguration.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Visar starten av att skapa en resurs grupp.":::

9. Välj **Öppna** för att öppna en SparaTillFil-Terminal.
10. När du uppmanas till det anger du konto namnet och lösen ordet för ditt Linux VM-konto.

## <a name="configure-the-virtual-machine"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du ändrar storlek, uppdaterar och generaliserar en virtuell Azure-dator. Dessa steg är nödvändiga för att förbereda din virtuella dator så att den distribueras på Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ändra storlek på de virtuella hård diskarna

Följande regler gäller för begränsningar av storleken på OS-disken. När du skickar en begäran kontrollerar du att OS-diskens storlek är inom begränsningen för Linux eller Windows.

| Operativsystem | Rekommenderad VHD-storlek |
| --- | --- |
| Linux | 30 till 1023 GB |
| Windows | 30 till 250 GB |

Eftersom virtuella datorer tillåter åtkomst till det underliggande operativ systemet kontrollerar du att VHD-storleken är tillräckligt stor för den virtuella hård disken. Eftersom diskar inte kan utökas utan drift stopp använder du en disk storlek på mellan 30 och 50 &nbsp; GB.

| VHD-storlek | Faktisk naturlig storlek | Lösning |
| --- | --- | --- |
| >500 TB | Saknas | Kontakta support teamet om du vill ha ett undantags godkännande. |
| 250-500 TB | >200 GB skiljer sig från BLOB-storlek | Kontakta support teamet om du vill ha ett undantags godkännande. |

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

De grundläggande avbildningarna av virtuella operativ system måste innehålla de senaste uppdateringarna upp till publicerings datumet. Innan du publicerar den virtuella hård disk som du skapade måste du uppdatera operativ systemet och alla installerade tjänster med de senaste säkerhets-och underhålls korrigeringarna.

- För Windows Server kör du kommandot Sök efter uppdateringar.
- För Linux-distributioner hämtas och installeras uppdateringar ofta med hjälp av ett kommando rads verktyg eller grafiskt verktyg. Till exempel tillhandahåller Ubuntu Linux [apt-get-](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) kommandot och [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) -verktyget för att uppdatera operativ systemet.

#### <a name="perform-additional-security-checks"></a>Utför ytterligare säkerhets kontroller

Upprätthålla en hög säkerhets nivå för dina lösnings avbildningar på Azure Marketplace. En check lista över säkerhetskonfigurationer och procedurer finns i [säkerhets rekommendationer för Azure Marketplace-avbildningar](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Några av de här rekommendationerna är speciella för Linux-baserade avbildningar, men de flesta gäller för alla VM-avbildningar.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utför anpassad konfiguration och schemalagda aktiviteter

Om du behöver ytterligare konfiguration använder du en schemalagd aktivitet som körs vid start för att göra slutliga ändringar i den virtuella datorn efter att den har distribuerats. Tänk också på följande:

- Om det är en körnings aktivitet ska aktiviteten Ta bort sig själv när den har slutförts.
- Konfigurationer bör inte förlita sig på andra enheter än C eller D, eftersom endast dessa två enheter alltid garanterar att de finns (enhet C är operativ system disken och enhet D är den temporära lokala disken).

Mer information om Linux-anpassningar finns i [tillägg och funktioner för virtuella datorer för Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalisera avbildningen

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå detta måste den virtuella hård disken generaliseras, en åtgärd som tar bort alla instans-/regionsspecifika identifierare och program driv rutiner från en virtuell dator.

### <a name="for-windows"></a>För Windows

Windows OS-diskar generaliseras med [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) -verktyget. Om du senare uppdaterar eller omkonfigurerar operativ systemet måste du köra Sysprep igen.

> [!WARNING]
> När du har kört Sysprep stänger du av den virtuella datorn tills den har distribuerats eftersom uppdateringar kan köras automatiskt. Den här avstängningen kommer att undvika efterföljande uppdateringar från att göra instansbaserade ändringar i operativ systemet eller de installerade tjänsterna. Mer information om hur du kör Sysprep finns i [steg för att generalisera en virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>För Linux

Följande process generaliserar en virtuell Linux-dator och distribuerar den igen som en separat virtuell dator. Mer information finns i [så här skapar du en avbildning av en virtuell dator eller virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Du kan stoppa när du når avsnittet "skapa en virtuell dator från avbildningen".

1. Ta bort Azure Linux-agenten

    1. Ansluta till din virtuella Linux-dator med en SSH-klient
    2. I SSH-fönstret anger du följande kommando: `sudo waagent –deprovision+user` .
    3. Skriv in Y om du vill fortsätta (du kan lägga till parametern-Force i föregående kommando för att undvika bekräftelse steget).
    4. När kommandot har slutförts skriver du Exit för att stänga SSH-klienten.

2. Stoppa en virtuell dator

    1. I Azure Portal väljer du resurs gruppen (RG) och avallokerar den virtuella datorn.
    2. Din virtuella hård disk är nu generaliserad och du kan skapa en ny virtuell dator med den här virtuella hård disken.

## <a name="next-steps"></a>Nästa steg

- Om du har problem med att skapa din nya Azure-baserade virtuella hård disk kan du läsa [vanliga problem när du skapar en virtuell hård disk](common-issues-during-vhd-creation.md).
- Om du inte gör det kan du testa och skicka en virtuell dator avbildning för Azure Marketplace-certifiering, inklusive var du ska hämta certifierings test verktyget för Azure Certified-verktyget och hur du använder det för att certifiera din VM [-](azure-vm-image-certification.md) avbildning.
