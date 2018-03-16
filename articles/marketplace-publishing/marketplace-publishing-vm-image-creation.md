---
title: "Skapa en avbildning av virtuell dator för Azure Marketplace | Microsoft Docs"
description: "Detaljerade anvisningar om hur du skapar en avbildning av virtuell dator på Azure Marketplace för andra att köpa."
services: Azure Marketplace
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: f7b19066ca3fa156456766ff82afeadadc6b1efa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Guide för att skapa en avbildning av virtuell dator för Azure Marketplace
Den här artikeln **steg 2**, vägleder dig genom förbereder de virtuella hårddiskar (VHD) som du ska distribuera till Azure Marketplace. De virtuella hårddiskarna är grunden för dina SKU: N. Processen skiljer sig åt beroende på om du tillhandahåller en Linux- eller Windows-baserade SKU. Den här artikeln täcker båda scenarierna. Den här processen kan utföras parallellt med [skapande av konton och registrering][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definiera erbjudanden och SKU: er
I det här avsnittet kan du lära dig att definiera vilka erbjudanden och deras associerade SKU: er.

Ett erbjudande är överordnat alla sina SKU:er. Du kan ha flera erbjudanden. Hur du väljer att strukturera dina erbjudanden är upp till dig. När ett erbjudande skickas till mellanlagringen, skickas det tillsammans med alla SKU:er. Du noga överväga din SKU-identifierare, eftersom de kommer att vara synliga i URL-Adressen:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Azure preview portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

En SKU är kommersiella namnet för en VM-avbildning. En VM-avbildning som innehåller en operativsystemdisk och noll eller flera datadiskar. Det är i grund och botten den fullständiga lagringsprofilen för en virtuell dator. En virtuell Hårddisk krävs per disk. Även tomt datadiskar kräver en virtuell Hårddisk som ska skapas.

Oavsett vilket operativsystem du använder lägger du endast till det minsta antalet datadiskar som SKU n kräver. Kunder kan inte ta bort diskar som är en del av en avbildning vid tidpunkten för distribution, men kan alltid lägga till diskar under eller efter distributionen om de behöver dem.

> [!IMPORTANT]
> **Ändra inte Diskantalet i en ny Avbildningsversion.** Definiera en ny SKU om du måste konfigurera om datadiskar i bilden. En ny version för avbildningen med annan disk antal kommer ha risken för senaste ny distribution baseras på den nya avbildning versionen i fall av automatisk skalning, automatisk distribution av lösningar via ARM-mallar och andra scenarier.
>
>

### <a name="11-add-an-offer"></a>1.1 Lägg till ett erbjudande
1. Logga in på den [Publiceringsportal] [ link-pubportal] med säljaren-konto.
2. Välj den **virtuella datorer** fliken Publishing Portal. Ange ditt erbjudande i fältet Ange transaktion. Erbjudandenamn är vanligtvis namnet på den produkt eller tjänst som du planerar att sälja i Azure Marketplace.
3. Välj **Skapa**.

### <a name="12-define-a-sku"></a>1.2 definiera en SKU
När du har lagt till ett erbjudande, måste du definiera och identifiera din SKU: er. Du kan ha flera erbjudanden och varje erbjudande kan ha flera SKU: er under den. När ett erbjudande skickas till mellanlagringen, skickas det tillsammans med alla SKU:er.

1. **Lägg till en SKU.** SKU: N kräver en identifierare som används i URL: en. Identifieraren måste vara unikt inom din publiceringsprofil, men det finns ingen risk för identifierare kollision med andra utgivare.

   > [!NOTE]
   > Erbjudandet och SKU-ID: n visas i URL-Adressen för erbjudande på Marketplace.
   >
   >
2. **Lägga till en sammanfattande beskrivning för din SKU.** Översikt över beskrivningar är synlig för kunderna, så bör du se dem lättläst. Den här informationen behöver inte vara låst tills fasen ”Push till mellanlagring”. Till dess kan du redigera den som du vill.
3. Om du använder Windows-baserade SKU:er följer du de rekommenderade länkarna för att hämta godkända versioner av Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Skapa en virtuell Hårddisk på Azure-kompatibel (Linux-baserat)
Det här avsnittet fokuserar på bästa praxis för att skapa en Linux-baserade VM-avbildning för Azure Marketplace. En stegvis genomgång finns följande dokumentation: [skapa en anpassad Linux VM-avbildning](../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Skapa en virtuell Hårddisk på Azure-kompatibel (Windows-baserade)
Det här avsnittet fokuserar på hur du skapar en SKU baserat på Windows Server för Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 kontrollerar du att du använder rätt base VHD
Operativsystemet VHD för VM-avbildning måste baseras på en Azure-godkända basavbildning som innehåller Windows Server eller SQL Server.

Om du vill börja skapa en virtuell dator från en av följande avbildningar som finns i den [Microsoft Azure-portalen][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQLServer 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Du hittar även dessa länkar i publiceringsportalen på SKU-sidan.

> [!TIP]
> Om du använder den aktuella Azure-portalen eller PowerShell, har Windows Server-avbildningar publicerade på 8 September 2014 och senare godkänts.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Skapa ditt Windows-baserad virtuell dator
Du kan använda Microsoft Azure-portalen för att skapa den virtuella datorn baserat på en godkänd basavbildning i några enkla steg. Följande är en översikt över processen:

1. Från sidan basavbildning Välj **Skapa virtuell dator** omdirigeras till den nya [Microsoft Azure-portalen][link-azure-portal].

    ![Rita][img-acom-1]
2. Logga in på portalen med Microsoft-konto och lösenord för Azure-prenumerationen du vill använda.
3. Följ anvisningarna för att skapa en virtuell dator med hjälp av basavbildningen som du har valt. Du måste ange en värd (namn på datorn), användarnamn (registrerad som administratör) och lösenord för den virtuella datorn.

    ![Rita][img-portal-vm-create]
4. Välj storleken på den virtuella datorn ska distribueras:

    a.    Om du planerar att utveckla VHD lokal spelar storleken ingen roll. Överväg att använda en av de mindre virtuella datorerna.

    b.    Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av de storlekar som rekommenderas för den valda avbildningen.

    c.    Mer information om priser finns i den **rekommenderas prisnivåer** selector visas på portalen. Den tillhandahåller de tre storlekar som rekommenderas av utgivaren. (I det här fallet är utgivaren Microsoft.)

    ![Rita][img-portal-vm-size]
5. Ange egenskaper för:

    a.    Du kan lämna standardvärdena för egenskaper under för snabbdistribution, **valfri konfiguration** och **resursgruppen**.

    b.    Under **Lagringskonto**, du kan också välja lagringskontot som operativsystemet virtuella Hårddisken ska sparas.

    c.    Under **resursgruppen**, du kan också välja logisk grupp att placera den virtuella datorn.
6. Välj den **plats** för distribution:

    a.    Om du planerar att utveckla VHD lokal roll inte platsen eftersom du kommer att överföra avbildningen till Azure senare.

    b.    Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av amerikanska Microsoft Azure-regionerna från start. Detta förbättrar VHD kopieringen som Microsoft utför åt dig när du skickar avbildningen för certifiering.

    ![Rita][img-portal-vm-location]
7. Klicka på **Skapa**. Den virtuella datorn börjar distribuera. Inom några minuter har du en färdig distribution och kan börja skapa avbildningen för din SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 utveckla den virtuella Hårddisken i molnet
Vi rekommenderar starkt att du utveckla den virtuella Hårddisken i molnet genom att använda Remote Desktop Protocol (RDP). Du ansluter till RDP med användarnamn och lösenord har angetts under etableringen.

> [!IMPORTANT]
> **Använd inte skivor hanteras.** Den virtuella datorn som används för att utveckla den virtuella Hårddisken till molnet måste inte baseras på diskar som hanteras som för närvarande inte stöder skapandet av en avbildning från dem.
> Skapa den virtuella datorn i valfri funktion ändra standard för diskar hanteras.

> Om du utvecklar dina VHD lokalt (vilket inte rekommenderas), se [och skapa en avbildning av virtuell dator lokalt](marketplace-publishing-vm-image-creation-on-premise.md). Hämta den virtuella Hårddisken är inte nödvändigt om du utvecklar i molnet.
>
>

**Ansluta via RDP med hjälp av den [Microsoft Azure-portalen][link-azure-portal]**

1. Välj **alla tjänster** > **VMs**.
2. Öppnar bladet för virtuella datorer. Kontrollera att den virtuella datorn som du vill ansluta med körs och markera den i listan med distribuerade virtuella datorer.
3. Då öppnas ett blad som beskriver den valda virtuella datorn. Överst, klickar du på **Anslut**.
4. Du uppmanas att ange användarnamn och lösenord som du angav under etableringen.

**Anslut via RDP med hjälp av PowerShell**

Du kan hämta en remote desktop-fil till en lokal dator den [cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Du måste känna till namnet på tjänsten och namnet på den virtuella datorn för att kunna använda denna cmdlet. Om du har skapat den virtuella datorn från den [Microsoft Azure-portalen][link-azure-portal], du kan hitta den här informationen under Egenskaper för Virtuella datorer:

1. Välj i Microsoft Azure-portalen **alla tjänster** > **VMs**.
2. Öppnar bladet för virtuella datorer. Välj den virtuella datorn som du har distribuerat.
3. Då öppnas ett blad som beskriver den valda virtuella datorn.
4. Klicka på **Egenskaper**.
5. Den första delen av domännamnet är tjänstens namn. Värdnamnet är namnet på den virtuella datorn.

    ![Rita][img-portal-vm-rdp]
6. För att ladda ned RDP-filen för den skapade Virtuellt för administratörens lokala skrivbordet är som följer.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Mer information om RDP kan hittas på MSDN i artikeln [Anslut till en virtuell Azure-dator med RDP eller SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Konfigurera en virtuell dator och skapa din SKU**

Efter att operativsystemet VHD hämtas, Använd Hyper-v och konfigurera en virtuell dator för att skapa din SKU. Detaljerade anvisningar finns i följande TechNet-länk: [installera Hyper-v och konfigurera en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 Välj rätt storlek för virtuell Hårddisk
Windows-operativsystemet VHD i VM-avbildning ska skapas som en virtuell Hårddisk fast format 128 GB.  

Om den fysiska storleken är mindre än 128 GB, vara den virtuella Hårddisken sparse. Grundläggande Windows- och SQL Server avbildningar som uppfyller redan kraven, så ändras inte formatet eller storleken på den virtuella Hårddisken som hämtades.  

Datadiskar kan vara så stor som 1 TB. När du funderar över diskens storlek, Kom ihåg att kunder inte ändra storlek på virtuella hårddiskar i en bild vid tidpunkten för distribution. Data disk virtuella hårddiskar ska skapas som en virtuell Hårddisk med fast format. De bör också vara sparse. Datadiskar kan antingen vara tomma eller innehålla data.

### <a name="35-install-the-latest-windows-patches"></a>3.5 Installera de senaste korrigeringarna för Windows
Källavbildningen innehåller de senaste uppdateringarna fram till och med det datum då den publicerades. Se till att Windows Update har körts och att samtliga senaste kritiska och viktiga uppdateringar har installerats innan du publicerar operativsystemet VHD som du har skapat.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3,6 utföra ytterligare aktiviteter för konfiguration och schema vid behov
Överväg att använda en schemalagd aktivitet som körs vid start att göra ytterligare ändringar för den virtuella datorn när den har distribuerats om ytterligare konfiguration krävs:

* Det är bäst att låta uppgiften radera sig själv när den här utförts.
* Ingen konfiguration bör lita på enheter än enheter C eller D, eftersom de är bara två enheterna som alltid är garanterat finns. Enhet D är tillfällig lokal disk enhet C är operativsystemets disk.

### <a name="37-generalize-the-image"></a>3.7 generalisera avbildningen
Alla avbildningar i Azure Marketplace måste vara återanvändbara i ett allmänt sätt. Med andra ord måste vara generaliserad virtuell Hårddisk för operativsystem:

* För Windows, bilden ska vara ”Sysprep” och inga konfigurationer bör göras som inte stöder den **sysprep** kommando.
* Du kan köra följande kommando från katalogen % windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Riktlinjer för att köra sysprep tillhandahålls hur operativsystemet i steg i följande MSDN-artikel: [skapa och ladda upp en Windows Server VHD till Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Distribuera en virtuell dator från din virtuella hårddiskar
När du har laddat upp de virtuella hårddiskarna (operativsystemet generaliserad virtuell Hårddisk och noll eller fler data disk virtuella hårddiskar) till ett Azure storage-konto, kan du registrera dem som en användare VM-avbildning. Du kan sedan testa avbildningen. Observera att eftersom operativsystemet VHD är generaliserad, du inte kan direkt distribuera den virtuella datorn genom att tillhandahålla VHD-URL.

Om du vill veta mer om VM-avbildningar kan du granska följande blogginlägg:

* [VM-avbildning](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [VM avbildningen PowerShell så](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Om VM-avbildningar i Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Ställa in verktyg som krävs, PowerShell och Azure CLI
* [Hur du konfigurerar PowerShell](/powershell/azure/overview)
* [Hur du konfigurerar Azure CLI](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 skapa en användare VM-avbildning
#### <a name="capture-vm"></a>Spela in VM
Läs länkar som anges nedan vägledning om att avbilda den virtuella datorn med Azure-API/PowerShell CLI.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Generalisera avbildningen
Läs länkar som anges nedan vägledning om att avbilda den virtuella datorn med Azure-API/PowerShell CLI.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 distribuera en virtuell dator från en användare VM-avbildning
Om du vill distribuera en virtuell dator från en användare VM-avbildning, du kan använda aktuellt [Azure-portalen](https://manage.windowsazure.com) eller PowerShell.

**Distribuera en virtuell dator från den aktuella Azure-portalen**

1. Gå till **ny** > **Compute** > **virtuella** > **från galleriet**.

2. Gå till **Mina avbildningar**, och välj sedan VM-avbildning som du vill distribuera en virtuell dator:

   1. Var uppmärksam på vilken avbildning som du väljer, eftersom den **Mina avbildningar** visas både avbildningar av operativsystem och VM-avbildningar.
   2. Titta på antalet diskar kan hjälpa avgöra vilken typ av bild som du distribuerar, eftersom flesta av VM-avbildningar har mer än en disk. Det är dock fortfarande möjligt att ha en VM-avbildning med bara en enda disk, som sedan måste **antalet diskar** värdet 1.

      ![Rita][img-manage-vm-select]
3. Följ guiden Skapa virtuell dator och ange den VM namn, VM storlek, plats, användarnamn och lösenord.

**Distribuera en virtuell dator från PowerShell**

Du kan använda följande cmdletar för att distribuera en stor virtuell dator från generaliserad VM-avbildning som precis har skapat.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Se [felsöka vanliga problem som påträffas under skapande av VHD] för ytterligare hjälp.
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Hämta certifikat för VM-avbildning
Nästa steg för att förbereda dina VM-avbildning för Azure Marketplace är den certifierade.

Den här processen omfattar köra ett verktyg för särskilda certifikatutfärdare, överför Verifieringsresultat till Azure-behållaren där de virtuella hårddiskarna finnas, lägga till ett erbjudande, definiera din SKU och skickar VM-avbildning för certifiering.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 hämta och kör verktyget certifikatutfärdare Test för Azure-certifierad
Verktyget certifikatutfärdare körs på en aktiv virtuell dator, etablerats från användaren VM-avbildning så att den Virtuella datorn är kompatibel med Microsoft Azure. Verktyget verifierar att vägledningen och kraven för förberedelserna av din VHD har uppfyllts. Utdata från verktyget är en Kompatibilitetsrapport som ska överföras i Publishing Portal när du begär certifiering.

Verktyget certifikatutfärdare kan användas med både Windows och Linux virtuella datorer. Ansluter till Windows-baserade virtuella datorer via PowerShell och ansluter till Linux virtuella datorer via SSH.Net:

1. Först hämta verktyget certifikatutfärdare på den [Microsoft hämtningsplats][link-msft-download].
2. Öppna verktyget certifikatutfärdare och klicka sedan på den **Starta nytt Test** knappen.
3. Från den **testa Information** skärmen, anger du ett namn för kör.
4. Välj om din virtuella dator körs på Linux eller Windows. Beroende på vad du väljer, markerar du efterföljande alternativ.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Ansluta certifikatutfärdare verktyget till en Linux-VM-avbildning**
1. Välj autentiseringsläge för SSH: lösenord eller nyckelfil.
2. Ange Domain Name System (DNS) namn, användarnamn och lösenord om du använder lösenordsbaserad autentisering.
3. Om nyckeln autentisering med anger du DNS-namn, användarnamn och privat nyckel plats.

   ![För lösenordsautentisering av Linux VM-avbildning][img-cert-vm-pswd-lnx]

   ![Nyckelfilen autentisering av Linux VM-avbildning][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ansluta certifikatutfärdare verktyget till en Windows-baserade VM-avbildning**
1. Ange det fullständigt kvalificerade VM DNS-namnet (till exempel MyVMName.Cloudapp.net).
2. Ange användarnamn och lösenord.

   ![Autentisering av lösenord i Windows VM-avbildning][img-cert-vm-pswd-win]

När du har valt rätt alternativ för Linux- eller Windows-baserade Virtuella bilden, Välj **Testanslutningen** så att du har en giltig anslutning för testning av SSH.Net eller PowerShell. När en anslutning upprättas väljer **nästa** att starta testet.

När testet är klart visas resultaten (Pass/Fail/Warning) för varje del av testet.

![Testfall för Linux VM-avbildning][img-cert-vm-test-lnx]

![Testfall för Windows VM-avbildning][img-cert-vm-test-win]

Om någon av testerna misslyckas certifieras inte bilden. Om detta inträffar kan du granska kraven och gör nödvändiga ändringar.

Efter den automatiserade testen uppmanas du att ange ytterligare kommentarer om VM-avbildning via en frågeformulär skärm.  Slutför frågorna och välj sedan **nästa**.

![Certifikatutfärdare verktyget frågeformulär][img-cert-vm-questionnaire]

![Certifikatutfärdare verktyget frågeformulär][img-cert-vm-questionnaire-2]

När du har slutfört frågeformuläret, kan du ange ytterligare information, till exempel SSH åtkomstinformation för Linux VM avbildningen och en förklaring till misslyckade bedömningar. Du kan hämta testresultat och loggfiler för utförda testfall förutom dina svar på frågeformulär. Spara resultatet i samma behållare som de virtuella hårddiskarna.

![Spara certifikatutfärdare testresultat][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 hämta signatur för delad åtkomst URI för VM-avbildningar
Under publiceringsprocessen, kan du ange de uniform resource Identifier () som kan leda till var och en av de virtuella hårddiskarna som du har skapat för ditt SKU. Microsoft behöver åtkomst till dessa VHD:er under certifieringsprocessen. Därför måste du skapa en signatur för delad åtkomst URI för varje virtuell Hårddisk. Det här är den URI som ska anges i den **bilder** fliken i Publishing Portal.

Signatur för delad åtkomst URI skapas måste uppfylla följande krav:

Obs: följande instruktioner gäller endast för ohanterade diskar som är den typ som stöds.

* När du genererar signatur för delad åtkomst URI: er för de virtuella hårddiskarna räcker lista och läsbehörighet. Bevilja inte skriv- eller raderingsbehörighet.
* Varaktighet för åtkomst ska vara minst tre (3) veckor från när signatur för delad åtkomst URI: N har skapats.
* Om du vill skydda för UTC-tid, väljer du dag före det aktuella datumet. Det aktuella datumet infaller 6 oktober 2014 väljer du exempelvis 2014-10-5.

SAS-URL kan skapas på flera olika sätt att dela den virtuella Hårddisken för Azure Marketplace.
Följande är 3 rekommenderade verktyg:

1.  Azure Lagringsutforskaren
2.  Microsoft Storage Explorer
3.  Azure CLI

**Azure Lagringsutforskaren (rekommenderas för Windows-användare)**

Nedan följer stegen för att generera SAS-URL med hjälp av Azure Lagringsutforskaren

1. Hämta [förhandsvisning 3 av Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) från CodePlex. Gå till [Azure Storage Explorer 6 Preview](https://azurestorageexplorer.codeplex.com/) och på **”hämtar”**.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Hämta [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) och installera efter packa upp den.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Öppna programmet när den har installerats.
4. Klicka på **Lägg till konto**.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Ange lagringskontonamn, lagringskontonyckel och lagring slutpunkter domän. Detta är storage-konto i din Azure-prenumeration där du har sparat den virtuella Hårddisken på Azure-portalen.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. När Azure Lagringsutforskaren är ansluten till specifika storage-konto, startas med alla de innehåller inom lagringskonto. Markera den behållare som du kopierade operativsystemet disk VHD-filen (även datadiskar om de är tillämpliga för ditt scenario).

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. När du har valt blob-behållare startar Azure Lagringsutforskaren visar filerna i behållaren. Välj bildfil (.vhd) som ska skickas.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  När du har valt VHD-filen i behållaren, klicka på den **säkerhet** fliken.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  I den **Blob-behållaren säkerhet** dialogrutan rutan, lämna standardvärdena på den **åtkomstnivå** fliken och klicka sedan på **signaturer för delad åtkomst** fliken.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Följ stegen nedan för att generera en signatur för delad åtkomst URI för VHD-avbildningen:

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Åtkomst tillåts från:** för att skydda för UTC-tid, väljer du dag före det aktuella datumet. Det aktuella datumet infaller 6 oktober 2014 väljer du exempelvis 2014-10-5.

    b. **Åtkomst till tillåten:** Välj ett datum som är minst tre veckor efter den **åtkomst tillåts från** datum.

    c. **Åtgärder som tillåts:** markerar du den **lista** och **Läs** behörigheter.

    d. Om du har valt VHD-filen på rätt sätt och sedan filen visas i **blobbnamnet till** med filnamnstillägget .vhd.

    e. Klicka på **generera signatur**.

    f. I **genereras delad åtkomst signatur URI: N för den här behållaren**, Sök efter följande markerade ovan:

       - Se till att bilden filnamn och **”VHD”** i URI: N.
       - I slutet av signaturen, se till att **”= rl”** visas. Detta demonstrerar att läs- och åtkomst har angetts korrekt.
       - I mitten av signaturen, se till att **”sr = c”** visas. Detta demonstrerar att du har åtkomst behållare

11. För att säkerställa att den genererade delad åtkomst signatur URI fungerar, klickar du på **Test i webbläsaren**. Den bör startar hämtningen.

12. Kopiera signatur för delad åtkomst URI. Detta är URI:n som ska klistras in i publiceringsportalen.

13. Upprepa steg 6 – 10 för varje virtuell Hårddisk i SKU: N.

**Microsoft Azure Lagringsutforskaren (Windows-/ MAC/Linux)**

Nedan följer stegen för att generera SAS-URL genom att använda Microsoft Azure Lagringsutforskaren

1.  Ladda ned Microsoft Azure Lagringsutforskaren formuläret [ http://storageexplorer.com/ ](http://storageexplorer.com/) webbplats. Gå till [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/releasenotes.html) och på **”hämta för Windows”**.

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Öppna programmet när den har installerats.

3.  Klicka på **Lägg till konto**.

4.  Konfigurera Microsoft Azure Lagringsutforskaren till din prenumeration genom att logga in på ditt konto

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Gå till lagringskontot och välj behållaren

6.  Välj **”hämta resursen Åtkomstsignatur...”** med hjälp av högerklickar du på den **behållare**

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Starttiden för uppdateringen, förfallotiden och behörigheter enligt följande

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Starttid:** för att skydda för UTC-tid, väljer du dag före det aktuella datumet. Det aktuella datumet infaller 6 oktober 2014 väljer du exempelvis 2014-10-5.

    b.  **Förfallotiden:** Välj ett datum som är minst tre veckor efter den **starttid** datum.

    c.  **Behörigheter:** markerar du den **lista** och **Läs** behörigheter

8.  Kopiera URI för signatur för delad åtkomst av behållare

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    Genererade SAS-URL är för behållare nivå och nu behöver lägga till VHD-namn i den.

    Format för behållare nivån SAS-URL: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Infoga VHD namn efter behållarens namn i SAS-URL som nedan `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Exempel:

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd är den virtuella Hårddiskens namn sedan VHD SAS-URL kommer att `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Se till att bilden filnamn och **”VHD”** i URI: N.
    - I mitten av signaturen, se till att **”sp = rl”** visas. Detta demonstrerar att läs- och åtkomst har angetts korrekt.
    - I mitten av signaturen, se till att **”sr = c”** visas. Detta demonstrerar att du har åtkomst behållare

9.  För att säkerställa att den genererade delad åtkomst signatur URI fungerar, testa den i webbläsaren. Den bör startar hämtningen

10. Kopiera signatur för delad åtkomst URI. Detta är URI:n som ska klistras in i publiceringsportalen.

11. Upprepa stegen för varje VHD i SKU:n.

**Azure CLI (rekommenderas för icke-Windows & kontinuerlig Integration)**

Nedan följer stegen för att generera SAS-URL med hjälp av Azure CLI

1.  Ladda ned Microsoft Azure CLI från [här](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Du kan också hitta olika länkar för  **[Windows](http://aka.ms/webpi-azure-cli)**  och  **[MAC OS x](http://aka.ms/mac-azure-cli)**.

2.  När du har laddat ned och installera

3.  Skapa en PowerShell (eller andra skript körbara filen) med följande kod och spara lokalt

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Uppdatera följande parametrar i ovan

    a. **`<StorageAccountName>`**: Ange namnet på ditt lagringskonto

    b. **`<Storage Account Key>`**: Ger din lagringskontonyckel

    c. **`<Permission Start Date>`**: Om du vill skydda för UTC-tid, väljer du dag före det aktuella datumet. Till exempel om det aktuella datumet infaller 26 oktober 2016 sedan värdet bör vara 10/25/2016. Om du använder Azure CLI 2.0 (az kommando), ange både datum och tid i Start- och slutdatum, till exempel: 10-25-2016T00:00:00Z.

    d. **`<Permission End Date>`**: Välj ett datum som är minst tre veckor efter den **startdatum**. Värdet bör vara **2016-02-11**. Om du använder Azure CLI 2.0 (az kommando), ange både datum och tid i Start- och slutdatum, till exempel: 11-02-2016T00:00:00Z.

    Följande är exempelkoden när du har uppdaterat rätt parametrar

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Öppna Redigeraren för Powershell med ”Kör som administratör”-läge och öppna filen i steg #3. Du kan använda en Skriptredigerare som är tillgänglig på ditt operativsystem.

5.  Kör skript och det ger dig SAS-URL för behållaren åtkomst

    Följande ska vara resultatet av den SAS-signaturen och kopiera den markerade del i en anteckningar

    ![Rita](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  Nu får du behållaren nivå SAS-URL och du behöver lägga till VHD-namn i den.

    Behållaren nivån SAS-URL #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Infoga VHD namn efter behållarens namn i SAS-URL som visas nedan `https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Exempel:

    TestRGVM201631920152.vhd är den virtuella Hårddiskens namn sedan VHD SAS-URL kommer att

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Kontrollera att dina bildfilens namn och ”VHD” i URI: N.
    -   I mitten av signaturen, se till att ”sp = rl” visas. Detta demonstrerar att läs- och åtkomst har angetts korrekt.
    -   I mitten av signaturen, se till att ”sr = c” visas. Detta demonstrerar att du har åtkomst behållare

8.  För att säkerställa att den genererade delad åtkomst signatur URI fungerar, testa den i webbläsaren. Den bör startar hämtningen

9.  Kopiera signatur för delad åtkomst URI. Detta är URI:n som ska klistras in i publiceringsportalen.

10. Upprepa stegen för varje VHD i SKU:n.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 innehåller information om VM-avbildning och begär certifiering i Publishing Portal
När du har skapat ditt erbjudande och SKU, ska du ange avbildningsinformation som är associerade med den SKU:

1. Gå till den [Publiceringsportal][link-pubportal], och sedan logga in med ditt säljare.
2. Välj den **VM-avbildningar** fliken.
3. Identifieraren visas längst upp på sidan är faktiskt erbjuder identifierare och inte SKU-ID.
4. Ange egenskaper under den **SKU: er** avsnitt.
5. Under **operativsystemsfamilj**, klicka på den typ av operativsystem som är associerade med operativsystemet VHD.
6. I den **operativsystemet** rutan, beskriver operativsystemet. Överväg ett format som operativsystemsfamilj, typ, version och uppdateringar. Ett exempel är ”Windows Server Datacenter 2014 R2”.
7. Välj upp till sex rekommenderade virtuella datorn. Dessa är rekommendationer som visas till kunden i bladet priser nivå i Azure Portal när de vill köpa och distribuera avbildningen. **Detta är endast rekommendationer. Kunden kan välja VM-storlek som innehåller diskar som anges i bilden.**
8. Ange vilken version. Versionsfältet kapslar in en semantiska version för att identifiera produkten med uppdateringar:
   * Versioner ska vara i formatet X.Y.Z, där X, Y och Z är heltal.
   * Bilder i olika SKU: er kan ha olika versioner av högre och lägre.
   * Versioner i en SKU bara ska inkrementella ändringar som ökar patch-version (Z från X.Y.Z).
9. I den **OS VHD URL** Ange Signatur för delad åtkomst URI som skapats för operativsystemet VHD.
10. Om det finns datadiskar som är associerade med denna SKU, Välj logiskt enhetsnummer (LUN) som du vill att data disken som ska monteras på distribution.
11. I den **LUN X VHD URL** Ange Signatur för delad åtkomst URI som skapats för den första data VHD.

    ![Rita](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Vanliga problem med SAS-URL & korrigeringar

|Problem|Felmeddelande|Korrigera|Länk till dokumentation|
|---|---|---|---|
|Det gick inte att kopiera bilder - ””? hittades inte i SAS-url|Fel: Kopiera avbildningar. Det går inte att hämta blob att använda som SAS-Uri.|Uppdatera SAS-Url med rekommenderade verktyg|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera bilder - ”a” och ”se” parametrar inte i SAS-url|Fel: Kopiera avbildningar. Det går inte att hämta blob att använda som SAS-Uri.|Uppdatera SAS-Url med Start- och slutdatum på den|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar - ”sp = rl” inte i SAS-url|Fel: Kopiera avbildningar. Det går inte att hämta blob att använda som SAS-Uri|Uppdatera SAS-Url med behörigheter som har angetts som ”läsa” och ”lista|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar - SAS-url har blanksteg i vhd-namn|Fel: Kopiera avbildningar. Det går inte att hämta blob att använda som SAS-Uri.|Uppdatera SAS-Url utan blanksteg|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar – Url-auktorisering för SAS-fel|Fel: Kopiera avbildningar. Det går inte att hämta blob på grund av Behörighetsfel|Återskapa SAS-Url|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Det gick inte att kopiera avbildningar – SAS-Url: ”a” och ”se” parametrar har inte datum tid specifikation|Fel: Kopiera avbildningar. Det går inte att hämta blob på grund av felaktig SAS-Url |SAS-URL: en Start- och slutdatum parametrar (”a”, ”SA”) måste ha fullständig datum / tid-specifikationen som 11-02-2017T00:00:00Z, och inte bara datum eller kortare versioner för tiden. Det är möjligt att få det här scenariot använder Azure CLI 2.0 (az kommandot). Se till att ange specifikationen datum tid och återskapa SAS-Url.|[https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|

## <a name="next-step"></a>Nästa steg
När du är klar med SKU-uppgifter du kan gå till den [administratörshandboken för Azure Marketplace marketing content][link-pushstaging]. I steget publiceringsprocessen och ger du marknadsföring innehållet priser och annan information som krävs före **steg3: testa den virtuella datorn erbjuder i Förproduktion**, där du kan testa olika användningsfall scenarier innan du distribuerar erbjudanden på Azure Marketplace för offentliga synlighet och inköp.  

## <a name="see-also"></a>Se också
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

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
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/
