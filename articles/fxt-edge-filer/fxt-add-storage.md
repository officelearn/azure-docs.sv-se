---
title: 'Självstudiekurs: Lägga till lagringsutrymme i ett Azure FXT Edge Filer-kluster'
description: Konfigurera backend-lagring och det klientvända pseudonamnområdet för Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239219"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Självstudiekurs: Lägga till backend-lagring och konfigurera det virtuella namnområdet 

I den här självstudien beskrivs hur du lägger till fjärråtkomstlagring för cacheminnet och hur du konfigurerar det klientinriktade virtuella filsystemet. 

Klustret ansluter till backend-lagringssystem för att komma åt begäran om dataklienter och för att lagra ändringar mer permanent än i cacheminnet. 

Namnområdet är det klientinriktade pseudofilsystemet som gör att du kan byta ut backend-lagring utan att ändra arbetsflöden på klientsidan. 

I den här självstudien lär du dig: 

> [!div class="checklist"]
> * Så här lägger du till serverdelslagring i Azure FXT Edge Filer-klustret 
> * Så här definierar du den klientvända sökvägen för lagring

## <a name="about-back-end-storage"></a>Om backend-lagring

Azure FXT Edge Filer-klustret använder en *kärnfilsdefinition* för att länka ett backend-lagringssystem till FXT-klustret.

Azure FXT Edge Filer är kompatibelt med flera populära NAS-maskinvarusystem och kan använda tomma behållare från Azure Blob eller annan molnlagring. 

Molnlagringsbehållare måste vara tomma när de läggs till så att FXT-operativsystemet helt kan hantera alla data på molnlagringsvolymen. Du kan flytta dina befintliga data till molnbehållaren när du har lagt till behållaren i klustret som en kärnfiler.

Använd Kontrollpanelen för att lägga till en kärnfiler i systemet.

> [!NOTE]
> 
> Om du vill använda Amazon AWS eller Google Cloud<sup>TM</sup> lagring, måste du installera en FlashCloud TM-funktionslicens. Kontakta din Microsoft-representant för en licensnyckel och följ sedan instruktionerna i den äldre konfigurationsguiden för [Att lägga till eller ta bort funktionslicenser](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Stöd för Azure Blob-lagring ingår i Azure FXT Edge Filer-programvarulicensen. 

Mer detaljerad information om hur du lägger till kärnfilers finns i följande avsnitt i guiden för klusterkonfiguration:

* Mer information om hur du väljer och förbereder dig för att lägga till en kärnfiler finns i [Arbeta med kärnfilers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* För detaljerade förutsättningar och steg-för-steg-instruktioner, läs följande artiklar:

  * [Lägga till en ny NAS Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Lägga till en ny Cloud Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

När du har lagt till en kärnfiler kan du uppdatera dess inställningar på sidan Grundläggande filerinformationsinställningar.

## <a name="add-a-core-filer"></a>Lägg till en kärnfiler

Definiera en kärnfiler genom att klicka på knappen Skapa på sidan **Core Filer** > Manage Core Filers.Define a core filers by clicking the **Create** button on the Core**Filer Manage Core Filers** settings page.

![Klicka på knappen Skapa ovanför listan över kärnfilers på sidan Hantera kärnfilers](media/fxt-cluster-config/create-core-filer-button.png)

Guiden **Lägg till nya kärnfiler** hjälper dig att skapa en kärnfiler som länkar till din serverdelslagring. Klusterkonfigurationsguiden har steg-för-steg-beskrivningar av processen, vilket är annorlunda för NFS/NAS-lagring och för molnlagring (länkarna är ovan). 

Underaktiviteter inkluderar:

* Ange vilken typ av kärnfiler (NAS eller moln)

  ![Första sidan i guiden hårdvara NAS nya kärna filer. Alternativet för "cloud core filer" är inaktiverat och visar ett felmeddelande om den saknade licensen.](media/fxt-cluster-config/new-nas-1.png)

* Sätt in kärnfilernas namn. Välj ett namn som hjälper klusteradministratörerna att förstå vilket lagringssystem det representerar.

* För NAS-kärnfilers anger du fullständigt kvalificerat domännamn (FQDN) eller IP-adress. FQDN rekommenderas för alla kärnfilers, och krävs för SMB-åtkomst.

* Välj en cacheprincip - Den andra sidan i guiden visar tillgängliga cacheprinciper för de nya kärnfilerna. Mer information finns i [avsnittet cacheprinciper i guiden för klusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Andra sidan i en maskinvaru-NAS-guide för nya kärnfiler. Den nedrullnings av cacherincipen är öppen, som visar flera inaktiverade alternativ och tre giltiga cacheprincipalternativ (bypass, läsa cachelagring och läsa/skriva cachelagring).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* För molnlagring måste du ange molntjänsten och åtkomstautentiseringsuppgifterna, bland andra parametrar. Mer information finns i [Molntjänsten och protokollet](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) i klusterkonfigurationsguiden.

  ![Information om filer i molnet i guiden Nya Kärnfiler](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Om du redan har lagt till autentiseringsuppgifter för molnåtkomst för det här klustret visas de i listan. Uppdatera och lägg till autentiseringsuppgifter på sidan Inställningar för > **Klustermolnautentiseringsuppgifter.** **Cluster** 

När du har fyllt i alla nödvändiga inställningar i guiden klickar du på knappen **Lägg till filer** för att skicka ändringen.

Efter en stund visas lagringssystemet på **listan Över kärnfilers** på Dashboard core och kan nås via grundläggande filer-inställningssidor.

![core filer "Flurry-NAS" på sidan Hantera kärnfilers-inställningar, med filer-detaljvyn utökad](media/fxt-cluster-config/core-filer-in-manage-page.png)

Kärnan filer i denna skärmdump saknas en vserver. Du måste länka kärnfilerna till en vserver och skapa en korsning så att klienter kan komma åt lagringen. De här stegen beskrivs nedan i [Konfigurera namnområdet](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurera namnområdet

Azure FXT Edge Filer-klustret skapar ett virtuellt filsystem som kallas *klusternamnområdet* som förenklar klientåtkomst till data som lagras på olika backend-system. Eftersom klienter begär filer med en virtuell sökväg kan lagringssystem läggas till eller ersättas utan att behöva ändra klientarbetsflödet. 

Med klusternamnområdet kan du också presentera moln- och NAS-lagringssystem i en liknande filstruktur. 

Klustrets vservers underhåller namnområdet och betjänar innehåll till klienter. Det finns två steg för att skapa klusternamnområdet: 

1. Skapa en vserver 
1. Ställ in korsningar mellan backend-lagringssystemen och klientinriktade filsystemsökvägar 

### <a name="create-a-vserver"></a>Skapa en vserver

VServers är virtuella filservrar som styr hur data flödar mellan klienten och klustrets kärnfilers:

* VServers värd för klientinriktade IP-adresser
* VServers skapar namnområdet och definierar korsningar som mappar den klientinriktade virtuella katalogstrukturen till export på backend-lagring
* VServers framtvingar filåtkomstkontroller, inklusive grundläggande filexportprinciper och system för användarautentisering
* VServers tillhandahåller SMB-infrastruktur

Innan du börjar konfigurera en klustervserver läser du den länkade dokumentationen och kontaktar din Microsoft-representant för att få hjälp med att förstå namnområde och virtuella servrar. Om du använder VLAN skapar [du dem](fxt-configure-network.md#adjust-network-settings) innan du skapar vservern. 

De här avsnitten i guiden för klusterkonfiguration hjälper dig att bekanta dig med FXT-vservern och globala namnrymdsfunktioner:

* [Skapa och arbeta med VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Använda ett globalt namnområde](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Skapa en VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Du behöver minst en vserver för klustret. 

Om du vill skapa en ny vserver behöver du följande information:

* Namnet som ska ställas in för vservern

* Utbudet av klientvända IP-adresser som vservern hanterar

  Du måste ange ett enda intervall med sammanhängande IP-adresser när du skapar vservern. Du kan lägga till fler adresser senare med hjälp av sidan **Inställningar för klientvändningsnätverk.**

* Om ditt nätverk har VLAN, som VLAN ska använda för den här vservern

Använd > **inställningssidan för VServer Manage VServers** för att skapa en ny vserver. **VServer** Mer information finns [i Skapa en VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) i klusterkonfigurationsguiden. 

![popup-fönster för att skapa en ny vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Skapa en korsning

En *korsning* mappar en backend-lagringssökväg till det klient synliga namnområdet.

Du kan använda det här systemet för att förenkla sökvägen som används i klientmonteringspunkter och för att skala kapaciteten sömlöst eftersom en virtuell sökväg kan rymma lagring från flera kärnfilers.

![Sidan Lägg till guiden Ny korsning med inställningarna ifyllda](media/fxt-cluster-config/add-junction-full.png)

Se [ **VServers** > namnområde](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) i klusterkonfigurationsguiden för fullständig information om hur du skapar en namnområdeskorsning.

![Sidan VServer > namnområdesinställningar som visar information om en korsning](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurera exportregler

När du har både en vserver och en kärnfiler bör du anpassa exportreglerna och exportprinciperna som styr hur klienter kan komma åt filer på kärnfilerexporten.

Använd först sidan > **VServer-exportregler** för att lägga till nya regler, ändra standardprincipen eller skapa en egen anpassad exportprincip. **VServer**

För det andra använder du sidan **VServer** > **Exportrinciper** för att tillämpa den anpassade principen på din kärnfilers export när den nås via den virtuella servern.

Läs artikeln Om klusterkonfigurationsguiden [Som kontrollerar åtkomst till kärnfiler exporterar](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) du för mer information.


## <a name="next-steps"></a>Nästa steg

När du har lagt till lagring och konfigurerat det klientinriktade namnområdet slutför du klustrets första installation: 

> [!div class="nextstepaction"]
> [Konfigurera klustrets nätverksinställningar](fxt-configure-network.md)
