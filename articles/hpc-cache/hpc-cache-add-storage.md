---
title: Lägga till lagringsutrymme i en Azure HPC-cache
description: Så här definierar du lagringsmål så att din Azure HPC-cache kan använda ditt lokala NFS-system eller Azure Blob-behållare för långsiktig fillagring
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: rohogue
ms.openlocfilehash: a68bf06bad995f71bedf6a5bdedcb676737a8c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271894"
---
# <a name="add-storage-targets"></a>Lägga till lagringsmål

*Lagringsmål* är backend-lagring för filer som används via en Azure HPC-cacheinstans. Du kan lägga till NFS-lagring (som ett lokalt maskinvarusystem) eller lagra data i Azure Blob.

Du kan definiera upp till tio olika lagringsmål för en cache. Cachen presenterar alla lagringsmål i ett aggregerat namnområde.

Kom ihåg att lagringsexporten måste vara tillgänglig från cachens virtuella nätverk. För lokal maskinvarulagring kan du behöva konfigurera en DNS-server som kan matcha värdnamn för NFS-lagringsåtkomst. Läs mer i [DNS-åtkomst](hpc-cache-prereqs.md#dns-access).

Lägg till lagringsmål när du har skapat cacheminnet. Proceduren är något annorlunda beroende på om du lägger till Azure Blob-lagring eller en NFS-export. Detaljer för varje är nedan.

## <a name="open-the-storage-targets-page"></a>Öppna sidan lagringsmål

Öppna cacheinstansen från Azure-portalen och klicka på **Lagringsmål** på det vänstra sidofältet. Sidan Lagringsmål listar alla befintliga mål och ger en länk för att lägga till en ny.

![skärmbild av länken för lagringsmål i sidofältet under rubriken Konfigurera, som ligger mellan kategorirubrikerna Inställningar och Övervakning](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Lägga till ett nytt Azure Blob-lagringsmål

Ett nytt Blob-lagringsmål behöver en tom Blob-behållare eller en behållare som fylls med data i Azure HPC Cache-molnfilsystemet. Läs mer om att förinläsa en Blob-behållare i [Flytta data till Azure Blob storage](hpc-cache-ingest.md).

Du kan skapa en ny behållare från den här sidan precis innan du lägger till den.

Om du vill definiera en Azure Blob-behållare anger du den här informationen.

![skärmbild av målsidan för lägga till lagring, ifylld med information för ett nytt Azure Blob-lagringsmål](media/hpc-cache-add-blob.png)

* **Namn på lagringsmål** - Ange ett namn som identifierar det här lagringsmålet i Azure HPC-cachen.
* **Måltyp** - Välj **Blob**.
* **Lagringskonto** - Välj det konto som du vill använda.

  Du måste auktorisera cacheinstansen för att komma åt lagringskontot enligt beskrivningen i [Lägg till åtkomstrollerna](#add-the-access-control-roles-to-your-account).

  Information om vilken typ av lagringskonto du kan använda finns [i Blob storage requirements](hpc-cache-prereqs.md#blob-storage-requirements).

* **Lagringsbehållare** - Välj Blob-behållaren för det här målet eller klicka på **Skapa ny**.

  ![skärmbild av dialogrutan för att ange namn och åtkomstnivå (privat) för ny behållare](media/add-blob-new-container.png)

* **Sökväg till virtuellt namnområde** – Ange sökvägen till klienten för det här lagringsmålet. Läs [Konfigurera aggregerat namnområde](hpc-cache-namespace.md) om du vill veta mer om funktionen för det virtuella namnområdet.

När du är klar klickar du på **OK** för att lägga till lagringsmålet.

> [!NOTE]
> Om brandväggen för lagringskontot är inställd på att begränsa åtkomsten till endast "valda nätverk" använder du den tillfälliga lösningen som dokumenteras i [Brandvägg för Blob-lagringskonto](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Lägga till åtkomstkontrollrollerna i ditt konto

Azure HPC Cache använder [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) för att auktorisera cachetjänsten för att komma åt ditt lagringskonto för Azure Blob-lagringsmål.

Lagringskontoägaren måste uttryckligen lägga till rollerna [Storage Account Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) and Storage [Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) för användaren "HPC Cache Resource Provider".

Du kan göra detta i förväg eller genom att klicka på en länk på sidan där du lägger till ett Blob-lagringsmål. Tänk på att det kan ta upp till fem minuter innan rollinställningarna sprids via Azure-miljön, så du bör vänta några minuter efter att du lagt till rollerna innan du skapar ett lagringsmål.

Steg för att lägga till RBAC-rollerna:

1. Öppna sidan **IAM (Access Control)** för lagringskontot. (Länken på sidan **Lägg till lagringsmål** öppnar automatiskt den här sidan för det valda kontot.)

1. Klicka **+** högst upp på sidan och välj **Lägg till en rolltilldelning**.

1. Välj rollen "Storage Account Contributor" i listan.

1. Lämna standardvärdet markerat ("Azure AD-användare, grupp eller tjänsthuvudnamn" i fältet **Tilldela åtkomst till.**  

1. Sök efter "hpc" i fältet **Välj.**  Den här strängen ska matcha ett tjänsthuvudnamn med namnet "HPC Cache Resource Provider". Markera det huvudbeloppet genom att klicka på det.

   > [!NOTE]
   > Om en sökning efter "hpc" inte fungerar kan du prova att använda strängen "storagecache" i stället. Användare som gick med i förhandsgranskningarna (före GA) kan behöva använda det äldre namnet för tjänstens huvudnamn.

1. Klicka på **knappen Spara** längst ned.

1. Upprepa den här processen om du vill tilldela rollen "Storage Blob Data Contributor".  

![skärmbild av gränssnittet för tillägg av rolltilldelning](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Lägga till ett nytt NFS-lagringsmål

Ett NFS-lagringsmål har fler fält än Blob-lagringsmålet. De här fälten anger hur lagringsexporten ska nås och hur du effektivt cachelagrar dess data. Med ett NFS-lagringsmål kan du också skapa flera namnområdessökvägar om NFS-värden har mer än en export tillgänglig.

![Skärmbild av målsidan för lägga till lagring med NFS-mål definierat](media/hpc-cache-add-nfs-target.png)

Ange den här informationen för ett NFS-stödt lagringsmål:

* **Namn på lagringsmål** - Ange ett namn som identifierar det här lagringsmålet i Azure HPC-cachen.

* **Måltyp** - Välj **NFS**.

* **Värdnamn** - Ange IP-adressen eller fullständigt kvalificerat domännamn för ditt NFS-lagringssystem. (Använd bara ett domännamn om cachen har åtkomst till en DNS-server som kan matcha namnet.)

* **Användningsmodell** - Välj en av datacacheprofilerna baserat på arbetsflödet, som beskrivs i [Välj en användningsmodell](#choose-a-usage-model)nedan.

### <a name="nfs-namespace-paths"></a>NFS-namnområdessökvägar

Ett NFS-lagringsmål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export eller underkatalog i samma lagringssystem.

Skapa alla sökvägar från ett lagringsmål.

Du kan när som helst [lägga till och redigera namnområdessökvägar](hpc-cache-edit-storage.md) på ett lagringsmål.

Fyll i dessa värden för varje namnområdessökväg:

* **Sökväg till virtuellt namnområde** – Ange sökvägen till klienten för det här lagringsmålet. Läs [Konfigurera aggregerat namnområde](hpc-cache-namespace.md) om du vill veta mer om funktionen för det virtuella namnområdet.

<!--  The virtual path should start with a slash ``/``. -->

* **NFS-exportsökväg** - Ange sökvägen till NFS-exporten.

* **Underkatalogsökväg** - Om du vill montera en specifik underkatalog för exporten anger du den här. Om inte, lämna det här fältet tomt.

När du är klar klickar du på **OK** för att lägga till lagringsmålet.

### <a name="choose-a-usage-model"></a>Välj en användningsmodell
<!-- referenced from GUI - update aka.ms link if you change this heading -->

När du skapar ett lagringsmål som pekar på ett NFS-lagringssystem måste du välja *användningsmodellen* för det målet. Den här modellen avgör hur dina data cachelagras.

Det finns tre alternativ:

* **Läs tunga, sällan förekommande skrivningar** - Använd det här alternativet om du vill snabba upp läsåtkomsten till filer som är statiska eller sällan ändras.

  Det här alternativet cachelagrar filer som klienter läser, men skickar skrivningar vidare till backend-lagringen omedelbart. Filer som lagras i cacheminnet jämförs aldrig med filerna på NFS-lagringsvolymen.

  Använd inte det här alternativet om det finns en risk för att en fil kan ändras direkt på lagringssystemet utan att först skriva den till cachen. Om det händer uppdateras aldrig den cachelagrade versionen av filen med ändringar från den bakre delen och datauppsättningen kan bli inkonsekvent.

* **Större än 15% skriver** - Det här alternativet snabbar upp både läs- och skrivprestanda. När du använder det här alternativet måste alla klienter komma åt filer via Azure HPC-cachen i stället för att montera backend-lagringen direkt. De cachelagrade filerna kommer att ha de senaste ändringarna som inte lagras på baksidan.

  I den här användningsmodellen kontrolleras inte filer i cacheminnet mot filerna på backend-lagring. Den cachelagrade versionen av filen antas vara mer aktuell. En modifierad fil i cacheminnet skrivs bara till backend-lagringssystemet efter att den har funnits i cacheminnet i en timme utan ytterligare ändringar.

* **Klienter skriver till NFS-målet och kringgår cacheminnet** - Välj det här alternativet om några klienter i arbetsflödet skriver data direkt till lagringssystemet utan att först skriva till cachen. Filer som klientbegäran begär cachelagras, men alla ändringar av dessa filer från klienten skickas omedelbart tillbaka till backend-lagringssystemet.

  Med den här användningsmodellen kontrolleras filerna i cacheminnet ofta mot backend-versionerna för uppdateringar. Den här verifieringen gör att filer kan ändras utanför cacheminnet samtidigt som datakonsekvensen bibehålls.

I den här tabellen sammanfattas skillnaderna i användningsmodell:

| Användningsmodell | Cachelagringsläge | Backend-verifiering | Maximal tillbakaskrivningsfördröjning |
| ---- | ---- | ---- | ---- |
| Läs tunga, sällan skriver | Läsa | Aldrig | Inget |
| Större än 15% skriver | Läsa/skriva | Aldrig | 1 timme |
| Klienter kringgår cachen | Läsa | 30 sekunder | Inget |

## <a name="next-steps"></a>Nästa steg

När du har skapat lagringsmål bör du tänka på en av följande uppgifter:

* [Montera Azure HPC-cachen](hpc-cache-mount.md)
* [Flytta data till Azure Blob-lagring](hpc-cache-ingest.md)

Om du behöver uppdatera några inställningar kan du [redigera ett lagringsmål](hpc-cache-edit-storage.md).
