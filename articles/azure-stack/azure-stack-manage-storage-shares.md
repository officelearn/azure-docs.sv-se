---
title: Hantera Azure-stacken lagringskapacitet | Microsoft Docs
description: "Övervaka och hantera tillgängligt lagringsutrymme för Azure-stacken."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 7056aefc6bc6203c8961b8a254a2b631c9072c7b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Hantera lagringskapacitet för Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Informationen i den här artikeln hjälper Azure Stack molnet operatorn övervakaren och hantera lagringskapaciteten för deras Azure Stack-distribution. Azure-stacken lagringsinfrastruktur allokerar en delmängd av lagringskapacitet på Azure Stack-distribution som ska användas för **lagringstjänster**. Storage-tjänster lagra data för en klient på resurser på volymer som motsvarar noderna i distributionen.

Du har en begränsad mängd lagringsutrymme för att arbeta med som en moln-operator. Mängden lagringsutrymme som definieras av du implementerar lösningen. Din lösning tillhandahålls av leverantören OEM när du använder en lösning med flera noder eller av den maskinvara som du installerar Azure Stack Development Kit.

Eftersom Azure stacken inte stöder expandering av lagringskapacitet, är det viktigt att [övervakaren](#monitor-shares) lagringsutrymme för att säkerställa effektiv operations bevaras.  

När en resurs återstående ledigt kapacitet blir begränsad, planerar att [hantera utrymmet](#manage-available-space) att förhindra att resurserna kapaciteten tar slut.

Alternativ för att hantera kapacitet är:
- Frigöra kapacitet
- Migrera en behållare

När en resurs är 100% används, lagring-tjänsten inte längre funktioner för resursen. Kontakta Microsoft support om du behöver hjälp med att återställa åtgärder för resursen.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Förstå volymer och resurser, behållare och diskar
### <a name="volumes-and-shares"></a>Volymer och resurser
Den *lagringstjänsten* partitionerar tillgängligt lagringsutrymme i separata och lika volymer som har allokerats för att lagra klientdata. Antalet volymer är lika med antalet noder i Azure Stack-distributionen:

- Det finns fyra volymer på en distribution med fyra noder. Varje volym har en enda resurs. På en distribution med flera noder minskas inte antalet resurser som en nod har tagits bort eller inte fungerar korrekt.
- Om du använder Azure Stack Developer Kit, finns det en enda volym med en enda resurs.

Eftersom storage service-resurser för exklusiv användning av storage-tjänster måste du inte direkt ändra, lägga till eller ta bort filer för resurserna. Storage-tjänster bör arbeta med filer som lagras i dessa volymer.

Resurser på volymer som innehåller klientdata. Klientdata innehåller sidblobar, blockblobbar, Lägg till blobbar, tabeller, köer, databaser och relaterade metadata lagras. Eftersom lagringsobjekt (blobbar osv) finns individuellt inom en enskild resurs, får inte den maximala storleken för varje objekt överskrida storleken på en resurs. Den maximala storleken för nya objekt är beroende av den kapacitet som finns kvar på en resurs som outnyttjat utrymme när det nya objektet skapas.

När en resurs börjar ta slut på ledigt utrymme och åtgärder för att [Frigör](#reclaim-capacity) utrymme inte lyckades eller tillgängliga, operatorn Azure Stack molnet kan [migrera](#migrate-a-container-between) blob-behållare från en resurs till en annan.

- Mer information om behållare och blobbar finns [Blob storage](azure-stack-key-features.md#blob-storage) i nyckeln funktioner och koncept i Azure-stacken.
- Information om hur klienten användare arbetar med blogg lagring i Azure-stacken finns [Azure Stack lagringstjänster](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Behållare
Klient användare skapa behållare som sedan används för att lagra blob-data. När en användare väljer i vilken behållare att placera BLOB storage-tjänsten använder en algoritm att ta reda på vilken volym att placera behållaren. Algoritmen väljer vanligtvis volymen med mest tillgängligt utrymme.  

När en blob placeras i en behållare, kan att blob växa till mer utrymme. När du lägger till nya blobbar och befintliga växer blobbar, det tillgängliga utrymmet på volymen som innehåller behållaren krymper.  

Behållare är inte begränsade till en enda resurs. När den kombinerade blob-data i en behållare växer Använd 80% eller mer av det tillgängliga utrymmet behållaren anges *spill* läge. I spill läge tilldelas alla nya blobbar som skapas i behållaren till en annan volym som har tillräckligt med utrymme. Med tiden kan en behållare i spill läge ha blobbar som är fördelade på flera volymer.

När du använder 80% och 90% av det tillgängliga utrymmet på en volym, genererar systemet aviseringar i Azure Stack-administratörsportalen. Molnoperatörer bör granska tillgängliga lagringskapaciteten och planera att balansera innehållet. Lagringstjänsten slutar fungera när en disk är 100% används, och inga ytterligare aviseringar aktiveras.

### <a name="disks"></a>Diskar
Virtuella diskar har lagts till i behållare av klienter och inkluderar en operativsystemdisk. Virtuella datorer kan också ha en eller flera datadiskar. Båda typerna av diskar lagras som sidblobar. Vägledning för att klienter är att placera varje disk i en separat behållare för att förbättra prestanda för den virtuella datorn.
- Varje behållare som innehåller en disk (sidblob) från en virtuell dator betraktas som en bifogad behållare till den virtuella datorn som äger disken.
- En behållare som inte innehåller en disk från en virtuell dator betraktas som en kostnadsfri behållare.

Alternativ för att frigöra utrymme på en behållare för anslutna [begränsas](#move-vm-disks).
> [!TIP]  
> Molnoperatörer hanterar inte diskar som är kopplade till virtuella datorer (VM) som klienter kan lägga till en behållare. När du planerar att hantera utrymmet på storage-resurser, kan det vara Använd för att förstå hur diskar relaterar till behållare och resurser.

## <a name="monitor-shares"></a>Övervakare för resurser
Använd PowerShell eller administrationsportal för att övervaka resurser så att du är medveten om när utrymmet är begränsat. När du använder portalen kan få du aviseringar om resurser som har ont om utrymme.    

### <a name="use-powershell"></a>Använd PowerShell
Du kan övervaka lagringskapaciteten för en resurs med hjälp av PowerShell som en moln-operator **Get-AzsStorageShare** cmdlet. Get-AzsStorageShare cmdlet returnerar totala allokerade och ledigt utrymme i byte på var och en av resurserna.   
![Exempel: Returnera ledigt utrymme för resurser](media/azure-stack-manage-storage-shares/free-space.png)

- **Total kapacitet** är det totala utrymmet i byte som är tillgängliga på resursen. Detta utrymme används för data och metadata som underhålls av lagringstjänsterna.
- **Används kapacitet** är mängden data i byte som används av de alla omfattningar från filer som lagrar klientdata och associerade metadata.

### <a name="use-the-administrator-portal"></a>Använd administratörsportalen
Du kan använda på administrationsportalen för att visa lagringskapaciteten för alla resurser som en moln-operator. **Gå till lagring** > **filresurser** att öppna listan resurs där du kan visa information om syntax.
![Exempel: Lagringsfilresurser](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **Totalt antal** är det totala utrymmet i byte som är tillgängliga på resursen. Detta utrymme används för data och metadata som underhålls av lagringstjänsterna.
- **ANVÄNDS** är mängden data i byte som används av de alla omfattningar från filer som lagrar klientdata och associerade metadata.

### <a name="storage-space-alerts"></a>Lagring utrymme aviseringar
När du använder administrationsportal, får du aviseringar om resurser som har ont om utrymme.

> [!IMPORTANT]
> Behåll resurser från att nå fullständig användning som en moln-operator. När en resurs är 100% används, lagring-tjänsten inte längre funktioner för resursen. Om du vill återställa ledigt utrymme och återställning på en resurs som är 100% används, måste du kontakta Microsoft support.

**Varning**: när en filresurs är över 80% används, visas en *varning* avisering i administrationsportal: ![exempel: varning](media/azure-stack-manage-storage-shares/alert-warning.png)


**Kritiska**: när en filresurs är över 90% används, visas en *kritiska* avisering i administrationsportal: ![exempel: varning](media/azure-stack-manage-storage-shares/alert-critical.png)

**Visa information**: du kan öppna information för en avisering om du vill visa alternativ för minskning i administrationsportal: ![exempel: Visa aviseringsinformation](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Hantera diskutrymme
När det är nödvändigt att ledigt utrymme på en resurs kan du använda metoderna minst inkräktande först. Till exempel försök att frigöra utrymme innan du väljer att migrera en behållare.  

### <a name="reclaim-capacity"></a>Frigöra kapacitet
*Det här alternativet gäller både flernodiga distributioner och i Azure-stacken Development Kit.*

Du kan frigöra kapacitet som används av innehavarens konton som har tagits bort. Denna förmåga är automatiskt frigöras när data [kvarhållningsperioden](azure-stack-manage-storage-accounts.md#set-the-retention-period) har uppnåtts eller du kan vidta åtgärder för att frigöra det. omedelbart.

Mer information finns i [frigöra kapacitet](azure-stack-manage-storage-accounts.md#reclaim) i hantera lagringsresurser.

### <a name="migrate-a-container-between-volumes"></a>Migrera en behållare mellan volymer
*Det här alternativet gäller endast för distributioner med flera noder.*

På grund av klient användningsmönster använder några resurser på klienten mer utrymme än andra. Resultatet kan vara en resurs som börjar ta slut på utrymme före andra resurser som är relativt oanvända.

Du kan försöka att frigöra utrymme på en överansträngda resurs manuellt migrera vissa blobbbehållare till en annan resurs. Du kan migrera flera mindre behållare till en enda resurs som har kapacitet att behålla dem alla. Du kan använda migrering för att flytta *ledigt* behållare. Ledigt behållare är behållare som inte innehåller en disk för en virtuell dator.   

Migrering konsoliderar alla behållare blob på den nya resursen.

- Om en behållare har angetts spill läge och har placerats blobbar på ytterligare volymer, måste den nya resursen ha tillräckligt med kapacitet för att rymma alla blobbar för den behållare som du migrerar. Detta inkluderar blobbar som finns på ytterligare resurser.

- PowerShell-cmdleten *Get-AzsStorageContainer* identifierar utrymmen som används i den ursprungliga volymen för en behållare. Cmdleten identifierar inte utrymme som används av blobbar spärra ytterligare volymer. Maximal storlek för en behållare kan därför inte klart. Det går att slå samman en behållare på en ny resurs kan skicka den nya resursen i ett spilltillstånd där placeras data till ytterligare resurser. Därför kan behöva du balansera om resurser.

- Om du saknar behörighet till en resursgrupp och det går inte att använda PowerShell för att efterfråga ytterligare volymer för spill data kan du arbeta med ägaren av dessa resursgrupper och behållare att förstå den totala storleken på data att migrera innan du migrerar data.  

> [!IMPORTANT]
> Migrering av BLOB för en behållare är en offline-åtgärd som kräver att PowerShell. Tills migreringen är klar alla BLOB för den behållare som du migrerar vara offline och kan inte användas.

#### <a name="to-migrate-containers-using-powershell"></a>Att migrera behållare med hjälp av PowerShell
1. Bekräfta att du har [Azure PowerShell installeras och konfigureras](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767).
2.  Granska behållare för att förstå vilka data som finns på den resurs som du planerar att migrera. Använd för att identifiera de bästa kandidat behållarna för migrering i en volym på **Get-AzsStorageContainer** cmdlet:

    ```
    $shares = Get-AzsStorageShare
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -Intent Migration
    ```
    Granska $containers:
    ```
    $containers
    ```
    ![Exempel: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Identifiera de bästa mål resurserna för att rymma den behållare som du migrerar:
    ```
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```
    Granska $destinationshares:
    ```
    $destinationshares
    ```    
    ![Exempel: $destination resurser](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Starta migreringen för en behållare. Migreringen är asynkron. Om du startar migreringen av ytterligare behållare innan den första migreringen är klar, kan du använda jobb-id för att spåra status för var och en.
  ```
  $jobId = Start-AzsStorageContainerMigration -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares[0].UncPath
  ```
  Granska $jobId. I följande exempel ersätter *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* med jobb-id som du vill undersöka:
  ```
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```
5. Du kan använda jobb-id för att kontrollera status för migreringen. När behållaren migreringen är klar, **MigrationStatus** är inställd på **Slutför**.
  ```
  Get-AzsStorageContainerMigrationStatus -JobId $jobId
  ```
  ![Exempel: Migreringsstatus](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  Du kan avbryta ett pågående migreringsjobb. Avbryta jobb bearbetas asynkront migrering. Du kan spåra cancellation med hjälp av $jobid:

  ```
  Stop-AzsStorageContainerMigration -JobId $jobId
  ```
  ![Exempel: Återställningsstatus](media/azure-stack-manage-storage-shares/rollback.png)

7. Du kan köra en kommandot tillsammans i steg 6 igen förrän statusen bekräftar migreringsjobbet är **avbruten**:  
    ![Exempel: Avbruten status](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Flytta Virtuella diskar
*Det här alternativet gäller endast för distributioner med flera noder.*

Extrema metoden att hantera utrymmet innebär flytt av virtuella diskar. Eftersom flytta en bifogade behållare (en som innehåller en virtuell disk) är komplexa, kontakta Microsoft Support om du vill utföra den här åtgärden.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [erbjudande virtuella datorer för att användare](azure-stack-tutorial-tenant-vm.md).
