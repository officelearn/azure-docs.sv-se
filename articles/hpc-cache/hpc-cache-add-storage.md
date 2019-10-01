---
title: Lägga till lagring till en Azure HPC-cache (för hands version)
description: Definiera lagrings mål så att Azure HPC-cachen kan använda ditt lokala NFS-system eller Azure Blob-behållare för långsiktig fil lagring
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: 302d727ede9604d11972eaa8f46a3e27f204858f
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710026"
---
# <a name="add-storage-targets"></a>Lägga till lagringsmål

*Lagrings målen* är Server dels lagring för filer som nås via en Azure HPC-instans. Du kan lägga till NFS-lagring (t. ex. ett lokalt maskin varu system) eller lagra data i Azure blob.

Du kan definiera upp till tio olika lagrings mål för ett cacheminne. Cachen visar alla lagrings mål i ett sammanlagt namn område.

Kom ihåg att lagrings exporten måste vara tillgänglig från cachens virtuella nätverk. För lokal maskin varu lagring kan du behöva konfigurera en DNS-server som kan matcha värdnamn för NFS-lagrings åtkomst. Läs mer i [DNS-åtkomst](hpc-cache-prereqs.md#dns-access).

Lägg till lagrings mål när du har skapat din cache. Proceduren skiljer sig något beroende på om du lägger till Azure Blob Storage eller en NFS-export. Information om var och en finns nedan.

## <a name="open-the-storage-targets-page"></a>Öppna sidan Storage-mål

Öppna din cache-instans från Azure Portal och klicka på **lagrings mål** på den vänstra sid panelen. Sidan lagrings mål listar alla befintliga mål och innehåller en länk för att lägga till en ny.

![skärm bild av länken lagrings mål på sid panelen under rubriken konfigurera, som är mellan kategorins rubrik inställningar och övervakning](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Lägg till ett nytt Azure Blob Storage-mål

Ett nytt Blob Storage-mål måste ha en tom BLOB-behållare eller en behållare som är ifylld med data i Azure HPC-cachens moln fil system format. Läs mer om att för hands läsa in en BLOB-behållare i [Flytta data till Azure Blob Storage](hpc-cache-ingest.md).

Ange den här informationen för att definiera en Azure Blob-behållare.

![skärm bild av sidan Lägg till lagrings mål, ifylld med information för ett nytt Azure Blob Storage-mål](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles -->

* **Lagrings mål namn** – ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.
* **Måltyp** – Välj **BLOB**.
* **Lagrings konto** – Välj det konto som ska referera till behållaren.

  Du måste auktorisera cache-instansen för att komma åt lagrings kontot enligt beskrivningen i [Lägg till åtkomst roller](#add-the-access-control-roles-to-your-account).
* **Lagrings behållare** – Välj BLOB-behållaren för målet.

* **Sökväg till virtuellt namn område** – ange sökvägen till klientens fil för det här lagrings målet. Läs [Konfigurera sammanställd namnrymd](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

När du är färdig klickar du på **OK** för att lägga till lagrings målet.

### <a name="add-the-access-control-roles-to-your-account"></a>Lägg till roller för åtkomst kontroll i ditt konto

Azure HPC cache använder [rollbaserad åtkomst kontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) för att ge cache-programmet åtkomst till ditt lagrings konto för Azure Blob Storage-mål.

Lagrings kontots ägare måste uttryckligen lägga till rollerna [lagrings konto deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) och [Storage BLOB data-deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) för användaren "StorageCache Resource Provider".

Du kan göra detta i förväg eller genom att klicka på en länk på sidan där du lägger till ett Blob Storage-mål.

Steg för att lägga till RBAC-roller:

1. Öppna sidan **åtkomst kontroll (IAM)** för lagrings kontot. (Länken på sidan **Lägg till lagrings mål** öppnar automatiskt den här sidan för det valda kontot.)

1. Klicka på överst på sidan och välj **Lägg till en roll tilldelning.** **+**

1. Välj rollen "lagrings konto deltagare" i listan.

1. I fältet **tilldela åtkomst till** lämnar du standardvärdet markerat ("Azure AD User, Group eller service huvud namn").  

1. I **Välj** -fältet söker du efter "storagecache".  Den här strängen ska matcha ett säkerhets objekt med namnet "HPC-Provider för HPC-cache". Klicka på det primära objektet för att välja det.

1. Klicka på knappen **Spara** för att lägga till roll tilldelningen i lagrings kontot.

1. Upprepa den här processen för att tilldela rollen "Storage BLOB data Contributor".  

![skärm bild av användar gränssnittet för Lägg till roll tilldelning](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Lägg till ett nytt NFS-lagrings mål

Ett NFS-lagrings mål har vissa extra fält för att ange hur lagrings exporten ska nå och hur data ska cachelagras effektivt. Du kan också skapa flera namn områdes Sök vägar från en NFS-värd om den har fler än en export tillgänglig.

![Skärm bild av sidan Lägg till lagrings mål med NFS-mål definierat](media/hpc-cache-add-nfs-target.png)

Ange den här informationen för ett NFS-baserat lagrings mål:

* **Lagrings mål namn** – ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.

* **Måltyp** – Välj **NFS**.

* **Hostname** – ange IP-adressen eller det fullständiga domän namnet för NFS-lagrings systemet. (Använd bara ett domän namn om din cache har åtkomst till en DNS-server som kan matcha namnet.)

* **Användnings modell** – Välj en av profilerna för Datacachen baserat på ditt arbets flöde, som beskrivs i [Välj en användnings modell nedan](#choose-a-usage-model).

### <a name="nfs-namespace-paths"></a>Sökväg till NFS-namnrymd

Ett NFS-lagrings mål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export-eller under katalog på samma lagrings system.

Skapa alla sökvägar från ett lagrings mål.
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

Fyll i följande värden för varje namn områdes Sök väg:

* **Sökväg till virtuellt namn område** – ange sökvägen till klientens fil för det här lagrings målet. Läs [Konfigurera sammanställd namnrymd](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

<!--  The virtual path should start with a slash ``/``. -->

* **NFS-export Sök väg** – ange sökvägen till NFS-exporten.

* **Sökväg till under katalog** – om du vill montera en speciell under katalog för exporten anger du den här. Annars lämnar du fältet tomt.

När du är färdig klickar du på **OK** för att lägga till lagrings målet.

### <a name="choose-a-usage-model"></a>Välj en användnings modell
<!-- referenced from GUI - update aka.ms link if you change this heading -->

När du skapar ett lagrings mål som pekar på ett NFS-lagrings system måste du välja *användnings modellen* för det målet. Den här modellen avgör hur dina data cachelagras.

* Läs mer tung – om du oftast använder cachen för att påskynda data Läs åtkomst väljer du det här alternativet.

* Läsa/skriva – om klienter använder cachen för att läsa och skriva väljer du det här alternativet.

* Klienter kringgå cache – Välj det här alternativet om dina klienter skriver data direkt till lagrings systemet utan att först skriva till cachen.

## <a name="next-steps"></a>Nästa steg

När du har skapat lagrings mål bör du överväga något av följande:

* [Montera Azure HPC-cachen](hpc-cache-mount.md)
* [Flytta data till Azure Blob Storage](hpc-cache-ingest.md)
