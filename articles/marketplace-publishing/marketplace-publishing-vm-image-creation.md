---
title: Skapa en avbildning av virtuell dator för Azure Marketplace | Microsoft Docs
description: Detaljerade anvisningar om hur du skapar en avbildning av virtuell dator på Azure Marketplace för andra att köpa.
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 6737e16efa93370b5b5d2b46026fce3bbc22d38f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075166"
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Guiden för att skapa en avbildning av virtuell dator för Azure Marketplace
Den här artikeln **steg 2**, beskriver hur du förbereder de virtuella hårddiskarna (VHD) som du distribuerar på Azure Marketplace. De virtuella hårddiskarna är grunden för din SKU. Processen skiljer sig beroende på om du erbjuder en Linux- eller Windows-baserad SKU. Den här artikeln tas båda scenarier upp. Den här processen kan utföras parallellt med [skapande och registrering][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definiera erbjudanden och SKU: er
I det här avsnittet får du lära dig att definiera dina erbjudanden och deras associerade SKU: er.

Ett erbjudande är överordnat alla sina SKU:er. Du kan ha flera erbjudanden. Hur du väljer att strukturera dina erbjudanden är upp till dig. När ett erbjudande skickas till mellanlagringen, skickas det tillsammans med alla SKU:er. Överväg noggrant dina SKU-identifierare eftersom de kommer att vara synliga i URL: en:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Azure-portalen: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

En SKU är det kommersiella namnet för en VM-avbildning. En datoravbildning av virtuell innehåller en operativsystemdisk och noll eller flera datadiskar. Det är i grund och botten den fullständiga lagringsprofilen för en virtuell dator. En VHD krävs per disk. Även tomma datadiskar kräver en virtuell Hårddisk som ska skapas.

Oavsett vilket operativsystem du använder lägger du endast till det minsta antalet datadiskar som SKU n kräver. Kunder kan inte ta bort diskar som är en del av en avbildning vid tidpunkten för distribution, men kan alltid lägga till diskar under eller efter distributionen om de behöver dem.

> [!IMPORTANT]
> *Ändra inte Diskantalet i en ny Avbildningsversion.* Om du måste konfigurera om datadiskar i bilden, definierar du en ny SKU. Publicera en ny Avbildningsversion med annan disk antal kommer har potentialen hos de senaste ny distribution baseras på den nya avbildningsversionen i fall av automatisk skalning, automatiska distributioner av lösningar via ARM-mallar och andra scenarier.
>
>

### <a name="11-add-an-offer"></a>1.1 Lägg till ett erbjudande
1. Logga in på den [Publiceringsportalen] [ link-pubportal] med ditt försäljarkonto.
2. Välj den **virtuella datorer** fliken i publiceringsportalen. I inmatningsfältet, anger du namnet på ditt erbjudande. Erbjudandenamn är vanligtvis namnet på produkten eller tjänsten som du planerar att sälja på Azure Marketplace.
3. Välj **Skapa**.

### <a name="12-define-a-sku"></a>1.2 definiera en SKU
När du har lagt till ett erbjudande, måste du definiera och identifiera dina SKU: er. Du kan ha flera erbjudanden och varje erbjudande kan ha flera SKU: er under den. När ett erbjudande skickas till mellanlagringen, skickas det tillsammans med alla SKU:er.

1. **Lägg till en SKU.** SKU: N kräver en identifierare som används i URL: en. Identifieraren måste vara unikt inom din publiceringsprofil, men det finns ingen risk för identifierare står i konflikt med andra utgivare.

   > [!NOTE]
   > Erbjudande och SKU-identifierare visas i URL-Adressen för erbjudandet i Marketplace.
   >
   >
2. **Lägg till en sammanfattande beskrivning av din SKU.** Sammanfattning av beskrivningar visas för kunder, så att du ska göra dem enkelt kan läsas. Den här informationen behöver inte låsas förrän den ”mellanlagring Push to Staging”-fasen. Till dess kan du redigera den som du vill.
3. Om du använder Windows-baserade SKU:er följer du de rekommenderade länkarna för att hämta godkända versioner av Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Skapa en Azure-kompatibel VHD (Linux-baserad)
Det här avsnittet fokuserar på Metodtips för att skapa en Linux-baserade VM-avbildning för Azure Marketplace. En stegvis genomgång finns följande dokumentation: [Skapa en anpassad Linux VM-avbildning](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Skapa en Azure-kompatibel VHD (Windows-baserad)
Det här avsnittet beskrivs stegen för att skapa en SKU som baseras på Windows Server på Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 kontrollerar du att du använder rätt base VHD: er
Operativsystemet VHD för din avbildning måste baseras på en Azure-godkänd Källavbildning som innehåller Windows Server eller SQL Server.

Om du vill börja skapa en virtuell dator från någon av följande avbildningar som finns på den [Microsoft Azure-portalen][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter] [länk-datactr-2012-r2], [2012 Datacenter] [länk-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 
* SQL Server 2012 SP2 

Du hittar även dessa länkar i publiceringsportalen på SKU-sidan.

> [!TIP]
> Om du använder den aktuella Azure-portalen eller PowerShell, har Windows Server-avbildningar publicerade den 8 September 2014 och senare godkänts.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Skapa en Windows-baserad virtuell dator
Du kan använda Microsoft Azure-portalen för att skapa en virtuell dator baserat på en godkänd Källavbildning med några få enkla steg. I följande lista innehåller en översikt över processen:

1. Från sidan basavbildningen Välj **Skapa virtuell dator** så att de dirigeras till den nya [Microsoft Azure-portalen][link-azure-portal].

    ![Rita][img-acom-1]
2. Logga in på portalen med Microsoft-konto och lösenord för den Azure-prenumeration du vill använda.
3. Följ anvisningarna för att skapa en virtuell dator med Källavbildningen du har valt. Ange en värd namn (namnet på datorn), användarnamn (registrerad som administratör) och lösenord för den virtuella datorn.

    ![Rita][img-portal-vm-create]
4. Välj storleken på den virtuella datorn ska distribueras:

    a.    Om du planerar att utveckla VHD lokala roll storleken ingen. Överväg att använda en av de mindre virtuella datorerna.

    b.    Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av de storlekar som rekommenderas för den valda avbildningen.

    c.    Information om priser finns i den **rekommenderade prisnivåer** väljare som visas i portalen. Den tillhandahåller de tre storlekar som rekommenderas av utgivaren. (I det här fallet är utgivaren Microsoft.)

    ![Rita][img-portal-vm-size]
5. Ställ in egenskaper:

    a.    För snabb distribution kan du kan lämna standardvärdena för egenskaper under **valfri konfiguration** och **resursgrupp**.

    b.    Under **Lagringskonto**, du kan också välja det lagringskonto där operativsystemet VHD lagras.

    c.    Under **resursgrupp**, du kan också välja den logiska grupp som du vill placera den virtuella datorn.
6. Välj den **plats** för distribution:

    a.    Om du planerar att utveckla den VHD: N lokalt spelar ingen plats eftersom du kommer att överföra avbildningen till Azure senare.

    b.    Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av amerikanska Microsoft Azure-regionerna från start. Det här alternativet går det snabbare VHD kopiering som Microsoft utför åt dig när du skickar in din avbildning för certifiering.

    ![Rita][img-portal-vm-location]
7. Klicka på **Skapa**. Den virtuella datorn börjar distribuera. Inom några minuter har du en färdig distribution och kan börja skapa avbildningen för din SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 utveckla din VHD i molnet
Vi rekommenderar starkt att du utvecklar din VHD i molnet genom att använda Remote Desktop Protocol (RDP). Du ansluter till RDP med användarnamnet och lösenordet som angavs vid etableringen.

> [!IMPORTANT]
> **Använd inte skivor som hanteras.** Den virtuella datorn som används för att utveckla VHD: N till molnet måste inte baseras på diskar som hanteras som det för närvarande inte stöder skapandet av en avbildning från dem.
> Skapa den virtuella datorn i valfri funktion ändra standard för diskar hanterade.

> Om du utvecklar din VHD lokalt (vilket inte rekommenderas), se [och skapa en avbildning av virtuell dator lokalt](marketplace-publishing-vm-image-creation-on-premise.md). Ladda ned din VHD är inte nödvändigt om du utvecklar i molnet.
>
>

**Anslut via RDP med hjälp av den [Microsoft Azure-portalen][link-azure-portal]**

1. Välj **alla tjänster** > **VMs**.
2. Bladet Virtual machines öppnas. Kontrollera att den virtuella datorn som du vill ansluta med körs och markera den i listan över distribuerade virtuella datorer.
3. Då öppnas ett blad som beskriver den valda virtuella datorn. Överst på sidan klickar du på **Connect**.
4. Du uppmanas att ange användarnamn och lösenord som du angav vid etablering.

**Ansluta via RDP med PowerShell**

Om du vill ladda ned en fjärrskrivbordsfil till en lokal dator genom att använda den [cmdleten Get-AzureRemoteDesktopFile][link-technet-2]. Om du vill använda denna cmdlet behöver du veta namnet på tjänsten och namnet på den virtuella datorn. Om du har skapat den virtuella datorn från den [Microsoft Azure-portalen][link-azure-portal], du kan hitta denna information under VM properties:

1. I Microsoft Azure-portalen väljer **alla tjänster** > **VMs**.
2. Bladet Virtual machines öppnas. Välj den virtuella datorn som du har distribuerat.
3. Då öppnas ett blad som beskriver den valda virtuella datorn.
4. Klicka på **Egenskaper**.
5. Den första delen av domännamnet är tjänstens namn. Värdnamnet är namnet på den virtuella datorn.

    ![Rita][img-portal-vm-rdp]
6. Cmdlet för att ladda ned RDP-filen för den skapade virtuella datorn till administratörens lokala skrivbord är som följer.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Mer information om RDP finns på MSDN i artikeln [Anslut till en Azure-dator med RDP eller SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

**Konfigurera en virtuell dator och skapa din SKU**

Efter att operativsystemet VHD hämtas, använder Hyper-v och konfigurerar en virtuell dator för att börja skapa din SKU. Detaljerade anvisningar finns på följande TechNet-länk: [Installera Hyper-v och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 Välj rätt VHD-storlek
Windows-operativsystemet VHD i din avbildning av virtuell dator bör skapas som en 128 GB fast format virtuell Hårddisk.  

Om den fysiska storleken är mindre än 128 GB bör VHD: N vara begränsad. Windows- och SQL Server Källavbildningen angetts uppfyller redan dessa krav, så ändra inte format eller storleken på den hämtade VHD: N.  

Datadiskar kan vara så stora som 1 TB. När du bestämmer dig diskens storlek, Kom ihåg att kunder inte kan ändra storlek på VHD: er inuti en avbildning vid distributionen. Data disk VHD: er bör skapas som en VHD i fast format. De bör också vara begränsad. Datadiskar kan antingen vara tomma eller innehålla data.

### <a name="35-install-the-latest-windows-patches"></a>3.5 Installera de senaste uppdateringarna för Windows
Källavbildningen innehåller de senaste uppdateringarna fram till och med det datum då den publicerades. Se till att Windows Update har körts och att alla de senaste kritiska och viktiga säkerhetsuppdateringarna har installerats innan du publicerar operativsystemet VHD som du har skapat.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 utföra ytterligare konfigurationer och Schemalägg uppgifter vid behov
Om ytterligare konfigurationer behövs bör du överväga att använda en schemalagd aktivitet som körs vid start för att göra ytterligare ändringar till den virtuella datorn när den har distribuerats:

* Det är bäst att låta uppgiften radera sig själv när den här utförts.
* Ingen konfiguration bör förlita sig på några andra enheter än enheter C eller D, eftersom dessa enheter är de enda två som garanterat alltid finns. Enhet C är operativsystemdisken och enhet D är den temporära lokala disken.

### <a name="37-generalize-the-image"></a>3.7 generalisera avbildningen
Alla avbildningar i Azure Marketplace måste kunna återanvändas på ett generiskt sätt. Med andra ord måste vara generaliserad virtuell Hårddisk för operativsystem:

* För Windows, bör avbildningen vara en Sysprep-avbildning ”och inga konfigurationer bör göras som inte stöder den **sysprep** kommando.
* Du kan köra följande kommando från katalogen % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Anvisningar för hur du sysprep operativsystemet finns i steg i följande MSDN-artikel: [Skapa och ladda upp en Windows Server VHD till Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Distribuera en virtuell dator från dina VHD: er
När du har laddat upp dina VHD: er (generaliserad operativsystemet VHD och noll eller fler datadisk VHD: er) till ett Azure storage-konto, kan du registrera dem som en avbildning för användaren. Du kan sedan testa avbildningen. Du kan inte direkt distribuera den virtuella datorn genom att tillhandahålla VHD-URL eftersom operativsystemet VHD är generaliserad.

Om du vill veta mer om VM-avbildningar kan du granska följande blogginlägg:

* [VM-avbildning](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [VM Image PowerShell så här](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Om VM-avbildningar i Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-classic-cli"></a>Konfigurera de nödvändiga verktyg, PowerShell och Azure klassiskt CLI
* [Så här konfigurerar du PowerShell](/powershell/azure/overview)
* [Så här konfigurerar du Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

### <a name="41-create-a-user-vm-image"></a>4.1 skapa en virtuell datoravbildning för användare
#### <a name="capture-vm"></a>Avbilda virtuell dator
Läs de länkar som anges nedan för information om avbildning av den virtuella datorn med hjälp av API/PowerShell/Azure CLI.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Generalisera avbildningen
Läs de länkar som anges nedan för information om avbildning av den virtuella datorn med hjälp av API/PowerShell/Azure CLI.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 distribuera en virtuell dator från en användaravbildning för virtuell dator
Om du vill distribuera en virtuell dator från en användare VM-avbildning, du kan använda aktuellt [Azure-portalen](https://manage.windowsazure.com) eller PowerShell.

**Distribuera en virtuell dator från den aktuella Azure-portalen**

1. Gå till **New** > **Compute** > **VM** > **från galleriet**.

2. Gå till **Mina avbildningar**, och sedan välja VM-avbildning som du vill distribuera en virtuell dator från:

   1. Var uppmärksam på vilken avbildning du väljer, eftersom den **Mina avbildningar** visas både operativsystemavbildningar och VM-avbildningar.
   2. Titta på antalet diskar kan avgöra vilken typ av avbildning du distribuerar eftersom majoriteten av avbildningar av Virtuella datorer har mer än en disk. Det är dock fortfarande möjligt att ha en VM-avbildning med bara en enda operativsystemdisk, som sedan måste **antalet diskar** angetts till 1.

      ![Rita][img-manage-vm-select]
3. Följ guiden Skapa virtuell dator och ange VM namn, VM-storlek, plats, användarnamn och lösenord.

**Distribuera en virtuell dator från PowerShell**

Du kan använda följande cmdletar för att distribuera en stor virtuell dator från den nyligen skapade generaliserade avbildningen.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> [Felsöka vanliga problem som kan uppstå när VHD skapas] finns för ytterligare hjälp.
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Skaffa en certifiering för VM-avbildning
Nästa steg förbereder en VM-avbildning för Azure Marketplace är att få den certifierad.

Den här processen omfattar som kör ett särskilt certifieringsverktyg, ladda upp verifikationsresultaten till Azure-behållaren där de virtuella hårddiskarna lagras, lägga till ett erbjudande, definiera din SKU och skicka in din avbildning för certifiering.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 ladda ned och kör verktyget för Test-certifiering för Azure Certified
Certification tool körs på en aktiv virtuell dator som etablerats från din VM-avbildning för användare, så att avbildningen är kompatibel med Microsoft Azure. Verktyget verifierar att vägledningen och kraven för förberedelserna av din VHD har uppfyllts. Resultatet från verktyget är en Kompatibilitetsrapport som ska överföras på Publisher-portalen när du begär certifiering.

Certification tool kan användas med både Windows och Linux-datorer. Ansluter den till Windows-baserade virtuella datorer via PowerShell och ansluter till virtuella Linux-datorer via SSH.Net:

1. Först laddar du ned certification tool på den [Microsoft hämtningsplats][link-msft-download].
2. Öppna certification tool och klicka sedan på den **Start New Test** knappen.
3. Från den **Test Information** skärmen, ange ett namn för testkörningen.
4. Välj om din virtuella dator körs på Linux eller Windows. Beroende på vad du väljer, markerar du efterföljande alternativ.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Ansluta certification tool till en Linux VM-avbildning**
1. Välj autentiseringsläge för SSH: lösenord eller nyckelfil.
2. Om du använder lösenordsbaserad autentisering, ange Domain Name System (DNS) namn, användarnamn och lösenord.
3. Om du använder nyckelfilsautentisering anger du DNS-namn, användarnamn och privat nyckel plats.

   ![För lösenordsautentisering av Linux VM-avbildning][img-cert-vm-pswd-lnx]

   ![Nyckelfilsautentisering av Linux VM-avbildning][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ansluta certification tool till en Windows-baserad VM-avbildning**
1. Ange det fullständigt kvalificerade VM DNS-namnet (till exempel MyVMName.Cloudapp.net).
2. Ange användarnamn och lösenord.

   ![För lösenordsautentisering av Windows VM-avbildning][img-cert-vm-pswd-win]

När du har valt rätt alternativ för din Linux eller Windows-baserade VM-avbildning, Välj **Testanslutningen** att säkerställa att SSH.Net eller PowerShell har en giltig anslutning för testning. När en anslutning har upprättats väljer **nästa** att starta testet.

När testet är klart visas resultaten (Pass/Fail/Warning) för varje del av testet.

![Testfall för Linux VM-avbildning][img-cert-vm-test-lnx]

![Testfall för Windows VM-avbildning][img-cert-vm-test-win]

Om någon av testerna misslyckas certifieras inte din avbildning. Om det här problemet uppstår, granska kraven och gör nödvändiga ändringar.

Efter den automatiserade testen uppmanas du att ange ytterligare information i din avbildning via en enkät skärm.  Slutför frågorna och välj sedan **nästa**.

![Certification Tool enkäten][img-cert-vm-questionnaire]

![Certification Tool enkäten][img-cert-vm-questionnaire-2]

När du har slutfört enkäten, kan du ange ytterligare information, till exempel SSH åtkomstinformation för Linux VM-avbildning och en förklaring till alla misslyckade utvärderingar. Du kan hämta testresultat och loggfiler för utförda testfall förutom svaren på enkäten. Spara resultaten i samma behållare som de virtuella hårddiskarna.

![Spara certifiering testresultat][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 hämta signatur för delad åtkomst URI för dina VM-avbildningar
Under publiceringsprocessen ska ange du uniform resource Identifier (URI: er) som leder till VHD: erna som du har skapat för din SKU. Microsoft behöver åtkomst till dessa VHD:er under certifieringsprocessen. Därför måste du skapa en signatur för delad åtkomst URI för varje VHD. URI: N ska anges i den **avbildningar** fliken i Publiceringsportalen.

Signatur för delad åtkomst som URI: N skapas bör uppfylla följande krav:

Följande instruktioner gäller endast för ohanterade diskar, vilket är den enda typen som stöds.

* När du genererar signatur för delad åtkomst URI: er för de virtuella hårddiskarna är List och Read-behörigheter tillräckliga. Bevilja inte skriv- eller raderingsbehörighet.
* Varaktigheten för åtkomsten bör vara minst tre (3) veckor från när signaturen för delad åtkomst URI: N skapas.
* Välj dag före aktuellt datum för att skydda för UTC-tid. Om det aktuella datumet infaller 6 oktober 2014, väljer du till exempel 10/5/2014.

SAS-URL kan skapas på flera olika sätt att dela din VHD för Azure Marketplace.
Följande är de tre rekommendera verktyg:

1.  Azure Lagringsutforskaren
2.  Microsoft Storage Explorer
3.  Azure CLI

**Azure Storage Explorer (rekommenderas för Windows-användare)**

Nedan följer stegen för att generera SAS-Webbadressen genom att använda Azure Storage Explorer

1. Ladda ned [Azure Storage Explorer 6 förhandsversion 3](https://azurestorageexplorer.codeplex.com/) från CodePlex. Gå till [Azure Storage Explorer 6 Preview](https://azurestorageexplorer.codeplex.com/) och klicka på **”hämtar”**.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Ladda ned [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) och installera när du har packat upp den.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Öppna programmet när den har installerats.
4. Klicka på **Lägg till konto**.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Ange lagringskontonamn, din lagringskontonyckel och domän för lagringsslutpunkter. Det här lagringskontot är i din Azure-prenumeration där du har sparat din VHD i Azure portal.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. När Azure Storage Explorer är ansluten till ditt specifika storage-konto, den börjar som den innehåller i lagringskontot. Välj behållaren dit du kopierade operativsystemets disk VHD-filen (även datadiskar om de är tillämpliga för ditt scenario).

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. När du har valt blob-behållare startar Azure Storage Explorer som visar filerna i behållaren. Välj bildfil (.vhd) som måste skickas.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  När du har valt VHD-filen i behållaren, klickar du på den **Security** fliken.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  I den **Blob Behållarsäkerhet** dialogrutan låter standardinställningarna på den **åtkomstnivå** fliken och klicka sedan på **signaturer för delad åtkomst** fliken.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Följ stegen nedan för att generera en signatur för delad åtkomst URI: N för VHD-avbildningen:

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Åtkomst tillåts från:** Välj dag före aktuellt datum för att skydda för UTC-tid. Om det aktuella datumet infaller 6 oktober 2014, väljer du till exempel 10/5/2014.

    b. **Åtkomst till tillåten:** Välj ett datum som är minst tre veckor efter den **åtkomst tillåts från** datum.

    c. **Åtgärder som tillåts:** Välj den **lista** och **Läs** behörigheter.

    d. Om du har valt VHD-filen korrekt kommer din fil visas i **blobnamnet att komma åt** med filnamnstillägget .vhd.

    e. Klicka på **generera signatur**.

    f. I **genereras delad åtkomst signatur-URI för den här behållaren**, Sök efter följande som markerade ovan:

       - Se till att din avbildning filnamn och **”VHD”** i URI: N.
       - I slutet av signaturen, se till att **”= rl”** visas. Det här värdet visar att läs- och lista åtkomst har angetts korrekt.
       - I mitten av signaturen, se till att **”sr = c”** visas. Det här värdet visar att du har åtkomst till nivån-behållare

11. För att säkerställa att den genererade delad åtkomst signatur URI: N fungerar, klickar du på **Test in Browser**. Den bör starta hämtningen.

12. Kopiera signatur för delad åtkomst URI. Klistra in den här URI: N i publiceringen Portal.

13. Upprepa steg 6 – 10 för varje VHD i SKU.

**Microsoft Azure Storage Explorer (Windows/MAC/Linux)**

Nedan följer stegen för att generera SAS-Webbadressen genom att använda Microsoft Azure Lagringsutforskaren

1.  Ladda ned Microsoft Azure Lagringsutforskaren formuläret [ http://storageexplorer.com/ ](http://storageexplorer.com/) webbplats. Gå till [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/releasenotes.html) och klicka på **”ladda ned för Windows”**.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Öppna programmet när den har installerats.

3.  Klicka på **Lägg till konto**.

4.  Konfigurera Microsoft Azure Storage Explorer till din prenumeration genom att logga in på ditt konto

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Gå till storage-konto och välj behållaren

6.  Välj **”hämta signatur för åtkomst av resurs...”** med hjälp av högerklickar du på den **behållare**

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Uppdatera starttid, förfallotid och behörigheter enligt följande

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Starttid:** Välj dag före aktuellt datum för att skydda för UTC-tid. Om det aktuella datumet infaller 6 oktober 2014, väljer du till exempel 10/5/2014.

    b.  **Förfallotid:** Välj ett datum som är minst tre veckor efter den **starttid** datum.

    c.  **Behörigheter:** Välj den **lista** och **Läs** behörigheter

8.  Kopiera URI: N signatur för delad åtkomst

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    Den genererade SAS URL är för behållare nivå och nu behöver vi lägga till namn på virtuell Hårddisk i den.

    Format för behållare på SAS-Webbadress: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Infoga namn på virtuell Hårddisk efter behållarens namn i SAS-URL enligt nedan `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Exempel:

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd är den virtuella Hårddiskens namn och sedan VHD SAS URL: en blir `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Se till att din avbildning filnamn och **”VHD”** i URI: N.
    - I mitten av signaturen, se till att **”sp = rl”** visas. Det här värdet visar att läs- och lista åtkomst har angetts korrekt.
    - I mitten av signaturen, se till att **”sr = c”** visas. Det här värdet visar att du har åtkomst till nivån-behållare

9.  Säkerställ att den genererade delad åtkomst signatur URI: N fungerar genom att testa den i webbläsaren. Den bör starta hämtningen

10. Kopiera signatur för delad åtkomst URI. Klistra in den här URI: N i publiceringen Portal.

11. Upprepa stegen för varje VHD i SKU:n.

**Azure CLI 2.0 (rekommenderas för icke-Windows och kontinuerlig integrering)**

Nedan följer stegen för att generera SAS-Webbadressen genom att använda Azure klassiskt CLI

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]

1.  Ladda ned Microsoft Azure-CLI från [här](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Du kan också hitta olika länkar för **[Windows](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)** och  **[MAC OS](https://docs.microsoft.com/cli/azure/install-azure-cli-macos?view=azure-cli-latest)**.

2.  När den har hämtats, kan du installera det här verktyget.

3.  Skapa en Bash (eller andra motsvarande skript körbara filen) med följande kod och spara den lokalt

        export AZURE_STORAGE_ACCOUNT=<Storage Account Name>
        EXPIRY=$(date -d "3 weeks" '+%Y-%m-%dT%H:%MZ')
        CONTAINER_SAS=$(az storage container generate-sas --account-name -n vhds --permissions rl --expiry $EXPIRY -otsv)
        BLOB_URL=$(az storage blob url -c vhds -n <VHD Blob Name> -otsv)
        echo $BLOB_URL\?$CONTAINER_SAS

    Uppdatera följande parametrar i ovan

    a. **`<Storage Account Name>`**: Ge namnet på ditt lagringskonto

    b. **`<VHD Blob Name>`**: Ge namnet på din VHD-blob.

    Välj ett datum som är minst tre veckor efter startdatum (standardvärde sas-token genererades). Ett exempel är: `2018-10-11T23:56Z`.

    Följande är exempelkoden efter uppdatering rätt parametrar exportera AZURE_STORAGE_ACCOUNT = vhdstorage1ba78dfb6bc2d8 förfallodatum = $(date -d ”tre veckor” ”+ %Y-%m-% dT % H: % MZ”) CONTAINER_SAS = $(az storage container generera sas - n VHD--behörigheter rl-- förfallodatum $EXPIRY - otsv) BLOB_URL = $(az storage blob url - c VHD - n osdisk_1ba78dfb6b.vhd - otsv) echo $BLOB_URL\?$CONTAINER_SAS

4.  Kör skriptet och ger du SAS-Webbadressen för åtkomst till nivån-behållare.

5.  Kontrollera din SAS-URL.

    - Se till att ditt filnamn för avbildning och ”VHD” finns i URI: N.
    -   I mitten av signaturen, se till att ”sp = rl” visas. Det här värdet visar att läs- och lista åtkomst har angetts korrekt.
    -   I mitten av signaturen, se till att ”sr = c” visas. Detta demonstrerar att du har åtkomst till nivån-behållare

    Exempel:

    `https://vhdstorage1ba78dfb6bc2d8.blob.core.windows.net/vhds/osdisk_1ba78dfb6b.vhd?se=2018-10-12T00%3A04Z&sp=rl&sv=2018-03-28&sr=c&sig=...`

8.  Säkerställ att den genererade delad åtkomst signatur URI: N fungerar genom att testa den i webbläsaren. Den bör starta hämtningen

9.  Kopiera signatur för delad åtkomst URI. Klistra in den här URI: N i publiceringen Portal.

10. Upprepa stegen för varje VHD i SKU:n.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 innehåller information om VM-avbildning och begär certifiering i Publiceringsportalen
När du har skapat ett erbjudande och SKU, bör du ange avbildningsdetaljerna som är kopplade till SKU: N:

1. Gå till den [Publiceringsportalen][link-pubportal], och sedan logga in med ditt försäljarkonto.
2. Välj den **VM-avbildningar** fliken.
3. Identifieraren som visas överst på sidan är faktiskt identifieraren för erbjudandet och inte SKU-ID.
4. Fyll i egenskaperna under den **SKU: er** avsnittet.
5. Under **operativsystemets familj**, klickar du på den typ av operativsystem som är associerade med det virtuella Hårddisken för operativsystemet.
6. I den **operativsystemet** rutan, beskriver du operativsystemet. Överväg att ett format till exempel operativsystemets familj, typ, version och uppdateringar. Ett exempel är ”Windows Server Datacenter 2014 R2”.
7. Välj upp till sex rekommenderade virtuella datorstorlekar. Dessa storlekar finns rekommendationer visas för kunden i bladet prisnivå i Azure Portal när de beslutar sig att köpa och distribuera din avbildning. **Detta är bara rekommendationer. Kunden kan välja vilken storlek som kan hanterar diskarna som specificerats i din avbildning.**
8. Ange version. Versionsfältet kapslar in en semantiska version för att identifiera produkten med uppdateringar:
   * Versionerna bör vara i formatet X.Y.Z, där X, Y och Z är heltal.
   * Bilder i olika SKU: er kan ha olika versioner av högre och den lägre.
   * Versioner i en SKU bör endast vara inkrementella ändringar, som ökar Uppdateringsversion (Z från X.Y.Z).
9. I den **OS VHD URL** anger signaturen för delad åtkomst URI: N som skapats för det virtuella Hårddisken för operativsystemet.
10. Om det finns datadiskar kopplade till denna SKU, väljer du logiskt enhetsnummer (LUN) som du vill att datadisken ska monteras till vid distributionen.
11. I den **LUN X VHD URL** anger signaturen för delad åtkomst URI: N som skapats för den första data-VHD.

    ![Rita](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Vanliga problem med SAS-URL och korrigeringar

|Problem|Meddelande om anslutningsfel|Korrigera|Länk till dokumentation|
|---|---|---|---|
|Det gick inte att kopiera avbildningar - ””? hittades inte i SAS-url|Fel: Kopiera avbildningar. Det går inte att hämta blob med hjälp av angivna SAS-Uri.|Uppdatera SAS-Url med rekommenderade verktyg|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar - ”a” och ”se” parametrar inte i SAS-url|Fel: Kopiera avbildningar. Det går inte att hämta blob med hjälp av angivna SAS-Uri.|Uppdatera SAS-webbadressen med Start- och slutdatumen på den|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar - ”sp = rl” inte i SAS-url|Fel: Kopiera avbildningar. Kunde inte hämta blob med hjälp av angivna SAS-Uri|Uppdatera SAS-webbadressen med behörigheterna inställda som ”läsa” och ”-lista|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar - SAS-webbadress har blanksteg i namn på virtuell hårddisk|Fel: Kopiera avbildningar. Det går inte att hämta blob med hjälp av angivna SAS-Uri.|Uppdatera SAS-URL: en utan blanksteg|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar – Url-auktorisering i SAS-fel|Fel: Kopiera avbildningar. Det går inte att ladda ned blob på grund av auktoriseringsfel|Återskapa SAS-Url|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar – SAS-Url ”a” och ”se” parametrar har inte fullständig datum / tid-specifikation|Fel: Kopiera avbildningar. Det går inte att ladda ned blob på grund av felaktiga SAS-Url |SAS URL: en Start- och slutdatum parametrar (”a”, ”se”) måste ha fullständig datum / tid-specifikationen, till exempel 11-02-2017T00:00:00Z, och inte bara det eller de förkortade versioner för tiden. Det är möjligt att stöta på det här scenariot med Azure CLI version 2.0 eller senare. Var noga med att tillhandahålla fullständig datum / tid-specifikationen och återskapa SAS-webbadressen.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Nästa steg
När du är klar med SKU-information kan du gå vidare och den [marketing content för för Azure Marketplace][link-pushstaging]. I det steget i publiceringsprocessen ska du ange marknadsföring innehåll, priser och annan information som krävs före **steg3: Testa din virtuella dator erbjudande i mellanlagringen**, där du testa olika Användningsscenarier innan du distribuerar erbjudandet på Azure Marketplace för offentliga synlighet och köp.  

## <a name="see-also"></a>Se också
* [Komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
