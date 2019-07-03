---
title: Lägga till backend-lagringsenheter i klustret för Microsoft Azure FXT Edge Filer
description: Konfigurera backend-lagring och klientinriktade pseudonamespace för Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 4a69aa7838e08c83b47c5f0248e821edf86b3990
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543274"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Självstudier: Lägg till backend-lagring och konfigurera virtuella namnområdet 

Den här självstudien beskrivs hur du lägger till tillbaka-edge-lagring för din cachelagring och hur du ställer in klientinriktade virtuellt filsystem. 

Klustret ansluts till backend-lagringssystem att komma åt databegäran för klienter och för att lagra ändringar mer permanent än i cachen. 

Namnområdet är klientinriktade pseudo filsystemet där du kan byta ut backend-lagring utan att ändra på klientsidan arbetsflöden. 

I den här självstudien lär du dig: 

> [!div class="checklist"]
> * Hur du lägger till backend-lagring till Azure FXT Edge Filer-kluster 
> * Hur du definierar klientinriktade sökväg för lagring

## <a name="about-back-end-storage"></a>Om backend-lagring

Azure FXT Edge Filer klustret använder en *viktiga filer* definitionen för att länka en backend-lagringssystemet till FXT-klustret.

Azure FXT Edge-Filer är kompatibel med flera populära NAS maskinvarusystem och kan använda tom behållare från Azure Blob eller andra molnlagring. 

Cloud storage-behållare måste vara tom när du lägger till så att operativsystemet FXT helt kan hantera alla data på lagringsvolymen för molnet. Du kan flytta dina befintliga data till molnbehållaren för när du lägger till behållaren i klustret som en core-filer.

Använd Kontrollpanelen för att lägga till en core-filer i systemet.

> [!NOTE]
> 
> Om du vill använda Amazon AWS eller Google Cloud storage måste du installera en FlashCloud<sup>TM</sup> funktionen licens. Kontakta din Microsoft-representant för en licensnyckel och följ sedan instruktionerna i den äldre konfigurationsguiden för [när du lägger till eller ta bort funktionen licenser](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Support för Azure Blob storage ingår i Azure FXT Edge Filer programvarulicensen. 

Mer detaljerad information om att lägga till grundläggande filter finns i dessa avsnitt ur i konfigurationsguiden för klustret:

* För mer information om att välja och förbereder för att lägga till en core-filer, läsa [arbeta med Core filter](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Detaljerade krav och stegvisa anvisningar finns i de här artiklarna:

  * [Att lägga till en ny NAS Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Att lägga till en ny Cloud Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

När du lägger till en core-filer kan du uppdatera sina inställningar på sidan Inställningar Core Filer information.

## <a name="add-a-core-filer"></a>Lägg till en core-filer

Definiera en core-filer genom att klicka på den **skapa** knappen på den **Core Filer** > **hantera Core filter** inställningssidan.

![Klicka på knappen Skapa ovanför listan med core filter på sidan Hantera grundläggande filter](media/fxt-cluster-config/create-core-filer-button.png)

Den **lägga till nya Core Filer** guiden vägleder dig genom processen att skapa en core-filer som länkar till backend-lagringen. Konfigurationsguide för kluster har steg för steg-beskrivning av processen, vilket skiljer sig för NFS/NAS-lagring och för molnlagring (länkar är ovan). 

Underuppgifter är:

* Ange vilken typ av core-filer (NAS eller i molnet)

  ![Första sidan i maskinvara NAS core filer guiden Ny. Alternativet för ”cloud core filer” är inaktiverad och visar ett felmeddelande visas om saknade licens.](media/fxt-cluster-config/new-nas-1.png)

* Ange namnet på filservern core. Välj ett namn som hjälper dig att klusteradministratörer förstå vilka lagringssystemet som representerar.

* Ange fullständigt kvalificerade domännamnet (FQDN) eller IP-adress för NAS core filter. FQDN är rekommenderas för alla kärnor filter och krävs för SMB-åtkomst.

* Välj en princip för cache - den andra sidan i guiden visas tillgänglig cache-principer för den nya core-filer. Mer information finns i [cachelagra principavsnittet i guiden för konfiguration av kluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Andra sidan i en maskinvara NAS core filer guiden Ny; listrutan princip är öppen, och flera inaktiverade alternativ och alternativ för tre giltig cache (kringgå, läsa cachelagring och skrivbar cachelagring).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Du måste ange de cloud service och autentiseringsuppgifterna för åtkomst, bland andra parametrar för molnlagring. Mer information finns [molnbaserad tjänst och protokoll](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) i konfigurationsguiden för klustret.

  ![Viktig information för filer i guiden Ny Core-Filer i molnet](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Om du redan har lagt till molnautentiseringsuppgifter för det här klustret som de visas i listan. Uppdatera och lägga till autentiseringsuppgifter i den **kluster** > **Molnautentiseringsuppgifter** inställningssidan. 

När du har fyllt i alla de nödvändiga inställningarna i guiden klickar du på den **lägga till Filer** knapp för att skicka ändringen.

Efter en liten stund lagringssystemet som visas på den **instrumentpanelen** core filter lista och kan nås via core inställningssidor för filer.

![Core filer ”hektisk tid NAS” på inställningssidan hantera Core filter med Detaljvyn filer expanderas](media/fxt-cluster-config/core-filer-in-manage-page.png)

Core-filer i den här skärmbilden saknar en vserver. Du måste länka core-filer till en vserver och skapa en förgrening så att klienter har åtkomst till lagringen. De här stegen beskrivs nedan i [konfigurera namnområdet](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurera namnområdet

Azure FXT Edge Filer-klustret skapar ett virtuellt filsystem som kallas den *kluster namnområde* som gör det enklare för klientåtkomst till data som lagras på skilda backend-system. Eftersom klienter begär filer med hjälp av en virtuell sökväg, lagringssystem läggs till eller ersättas utan att ändra klient-arbetsflödet. 

Namnområdet kluster kan du presentera moln- och NAS-lagringssystem i en liknande filstruktur. 

Klustrets vservers Underhåll namnområdet och hämta innehåll till klienter. Det finns två steg för att skapa kluster-namnområde: 

1. Skapa en vserver 
1. Konfigurera vägkorsningar mellan backend-lagringssystem och klientinriktade filsystem-sökvägar 

### <a name="create-a-vserver"></a>Skapa en vserver

VServers är virtuella filservrar som styr hur data flödar mellan klienten och klustrets core filter:

* VServers värden klientinriktade IP-adresser
* VServers skapa namnområdet och definiera vägkorsningar som mappar klientinriktade virtuell katalogstruktur till export på backend-lagring
* VServers framtvinga filen åtkomstkontroller, däribland core filer export principer och användaren autentiseringssystem
* VServers tillhandahåller en SMB-infrastruktur

Innan du börjar konfigurera ett kluster vserver, Läs länkade dokumentationen och kontakta din Microsoft-representant för hjälp med att förstå namnområde och vservers. Om du använder VLAN [skapa dem](fxt-configure-network.md#adjust-network-settings) innan du skapar vserver. 

Dessa avsnitt ur i klustret konfigurationsguiden hjälper dig att bekanta dig med de FXT vserver och globalt namnområde funktioner:

* [Skapa och arbeta med VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Med hjälp av en Global Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Skapa en VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Du behöver minst en vserver för klustret. 

Om du vill skapa en ny vserver, behöver du följande information:

* Namnet som ska anges för vserver

* Intervallet för klientinriktade IP-adresser som hanterar vserver

  Du måste ange ett enda antal sammanhängande IP-adresser när du skapar vserver. Du kan lägga till fler adresser senare med hjälp av den **som riktas mot klientnätverk** inställningssidan.

* Om nätverket innehåller VLAN, vilka VLAN för det här vserver

Använd den **VServer** > **hantera VServers** inställningssidan för att skapa en ny vserver. Läs [skapar en VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) i konfigurationsguiden för klustret. 

![popup-fönstret för att skapa en ny vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Skapa en förgrening

En *knutpunkt* mappar en backend-lagringssökväg till namnområdet synligt för klienten.

Du kan använda det här systemet att förenkla den sökväg som användes i klienten monteringspunkter och skala kapaciteten sömlöst eftersom en virtuell sökväg kan hantera lagring från flera kärnor filter.

![Lägg till ny knutpunkt sida i guiden med inställningar som fyllts](media/fxt-cluster-config/add-junction-full.png)

Referera till [ **VServer** > **Namespace** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) i klustret konfigurationsguide för fullständig information om hur du skapar ett namnområde knutpunkt.

![VServer > Namespace inställningssidan som visar information för en knutpunkt](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurera regler för export

När du har både en vserver och arkiverar core kan du anpassa regler för export och exportera principer som styr hur klienterna kan komma åt filer på core filer export.

Använd först den **VServer** > **exportera regler** sidan för att lägga till nya regler att ändra standardprincipen eller skapa egna anpassade export-princip.

Sedan använder den **VServer** > **exportera principer** att gälla din core filer export när nås via den vserver den anpassade principen.

Läs guiden för konfiguration av kluster-artikeln [styra åtkomst till Core Filer export](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) mer information.


## <a name="next-steps"></a>Nästa steg

Efter att lägga till och konfigurera klientinriktade namnområdet kan slutföra installationen för ditt kluster: 

> [!div class="nextstepaction"]
> [Konfigurera klustrets nätverksinställningar](fxt-configure-network.md)
