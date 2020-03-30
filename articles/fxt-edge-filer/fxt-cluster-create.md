---
title: 'Självstudiekurs: Skapa cacheklustret för Azure FXT Edge Filer'
description: Skapa ett hybridlagringscachekluster med Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: bfe1d1aeeac55039acf0c7eb295001277be9cd2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239212"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Självstudiekurs: Skapa Azure FXT Edge Filer-klustret

När du har installerat och initierat azure FXT Edge Filer-maskinvarunoderna för cacheminnet använder du FXT-klusterprogramvaran för att skapa cacheklustret. 

Den här självstudien går igenom stegen för att konfigurera maskinvarunoderna som ett kluster. 

I den här kursen lär du dig: 

> [!div class="checklist"]
> * Vilken information som behövs innan du börjar skapa klustret
> * Skillnaden mellan klustrets hanteringsnätverk, klusternätverket och det klientinriktade nätverket
> * Ansluta till en klusternod 
> * Skapa ett första kluster med en Azure FXT Edge Filer-nod
> * Logga in på klusterkontrollpanelen för att konfigurera klusterinställningarna

Den här proceduren tar mellan 15 och 45 minuter, beroende på hur mycket forskning du behöver göra för att identifiera IP-adresser och nätverksresurser.

## <a name="prerequisites"></a>Krav

Slutför dessa förutsättningar innan du startar den här självstudien:

* Installera dina Azure FXT Edge Filer-maskinvarusystem i ditt datacenter 

  Du behöver bara en nod för att skapa klustret, men du måste lägga till [minst två noder](fxt-add-nodes.md) till innan du kan konfigurera klustret och göra det klart att användas. 

* Anslut lämpliga ström- och nätverkskablar till systemet  
* Slå på minst en Azure FXT Edge Filer-nod och [ange dess rotlösenord](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Samla in information för klustret 

Du behöver följande information för att skapa Azure FXT Edge Filer-klustret:

* Klusternamn

* Administrativt lösenord som ska anges för klustret

* IP-adresser:

  * En enda IP-adress för klusterhantering och den nätmask och router som ska användas för hanteringsnätverket
  * Den första och sista IP-adresserna i ett sammanhängande intervall med IP-adresser för klusterkommunikation (nod till nod). Mer information finns i [IP-adressdistributionen](#ip-address-distribution)nedan. 
  * (Klientinriktade IP-adresser ställs in när klustret har skapats.)

* Information om nätinfrastruktur:

  * IP-adressen för en DNS-server för klustret
  * Namnet på DNS-domänen för klustret
  * Namnet eller IP-adressen för klustrets NTP-servrar (antingen en server eller tre eller fler) 
  * Oavsett om du vill aktivera IEEE 802.1AX-2008 länk aggregering på klustrets gränssnitt
  * Om du aktiverar länkaggregering, oavsett om du vill använda dynamisk Aggregering av IEEE 802.3ad (LACP) eller inte

Du kan konfigurera dessa nätverksinfrastrukturobjekt när du har skapat klustret, men det är bättre att göra det när du skapar. 

### <a name="ip-address-distribution"></a>Distribution av IP-adresser

Azure FXT Edge Filer hybridlagringscachekluster använder IP-adresser i tre kategorier:

* Hanterings-IP: En enda IP-adress för klusterhantering

  Den här adressen fungerar som startpunkt för åtkomst till klusterkonfigurationsverktygen (den webbaserade kontrollpanelen eller XML-RPC-API:et). Den här adressen tilldelas automatiskt till den primära noden i klustret och flyttas automatiskt om den primära noden ändras.

  Andra IP-adresser kan användas för att komma åt kontrollpanelen, men hanterings-IP-adressen är utformad för att ge åtkomst även om enskilda noder växlar över.

* Klusternätverk: Ett intervall med IP-adresser för klusterkommunikation

  Klusternätverket används för kommunikation mellan klusternoder och för att hämta filer från serverdelslagringen (kärnfilers).

  **Bästa praxis:** Allokera en IP-adress per fysisk port som används för klusterkommunikation på varje Azure FXT Edge Filer-nod. Klustret tilldelar automatiskt adresserna i det angivna intervallet till enskilda noder.

* Klientvänt nätverk: Det intervall av IP-adresser som klienter använder för att begära och skriva filer

  Klientnätverksadresserna används av klienter för att komma åt kärnfilerdata via klustret. En NFS-klient kan till exempel montera en av dessa adresser.

  **Bästa praxis:** Allokera en IP-adress per fysisk port som används för klientkommunikation på varje NOD i FXT-serien.

  Klustret distribuerar klientinriktade IP-adresser över sina ingående noder så jämnt som möjligt.

  För enkelhetens skull konfigurerar många administratörer ett enda DNS-namn med RRDNS-konfiguration (Round-Robin DNS) för att göra det enklare att distribuera klientbegäranden över adressintervallet. Den här inställningen gör det också möjligt för alla klienter att använda samma monteringskommando för att komma åt klustret. Läs [Konfigurera DNS](fxt-configure-network.md#configure-dns-for-load-balancing) för mer information.

Hanterings-IP-adressen och ett intervall med klusternätverksadresser måste anges för att skapa ett nytt kluster. Klientinriktade adresser anges när klustret har skapats.

## <a name="connect-to-the-first-node"></a>Ansluta till den första noden

Du kan ansluta till någon av de installerade FXT-noderna och använda dess OS-programvara för att konfigurera klustret.

Om du inte redan har gjort det, slå på minst en av FXT-noderna för klustret och se till att den har en nätverksanslutning och en IP-adress. Du måste ange ett nytt rotlösenord för att aktivera noden, så följ stegen i [Ange maskinvarulösenord](fxt-node-password.md) om du inte redan har gjort det.

Kontrollera nätverksanslutningen genom att se till att nodens nätverkslänklysdioder är tända (och vid behov indikatorerna på nätverksväxeln som den är ansluten till). Indikatorlysdioder beskrivs i [maskinvarustatus för Monitor Azure FXT Edge Filer](fxt-monitor.md).

När noden startar begär den en IP-adress. Om den är ansluten till en DHCP-server accepteras IP-adressen som tillhandahålls av DHCP. (Den här IP-adressen är tillfällig. Det ändras när du skapar klustret.)

Om den inte är ansluten till en DHCP-server eller inte får något svar, använder noden Bonjour-programvaran för att ange en självtilldelad IP-adress i formuläret 169.254. \*. \*. Du bör dock ange en tillfällig statisk IP-adress på ett av nodens nätverkskort innan du använder den för att skapa ett kluster. Instruktioner ingår i det här äldre dokumentet. kontakta Microsoft Service and Support för uppdaterad information: [Bilaga A: Ange en statisk IP-adress på en FXT-nod](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Hitta IP-adressen

Anslut till noden Azure FXT Edge Filer för att hitta dess IP-adress. Du kan använda en seriell kabel, direktanslutning till USB- och VGA-portarna eller ansluta via en KVM-switch. (För information om portanslutning se [Ange inledande lösenord](fxt-node-password.md).)

När du har anslutit loggar `root` du in med användarnamnet och lösenordet som du angav när du startade noden för första gången.  

När du har loggat in måste du bestämma nodens IP-adress.

Använd kommandot `ifconfig` för att se de adresser som tilldelats det här systemet.

Kommandot `ifconfig | grep -B5 inet` söker till exempel efter portar med internetadresser och ger fem kontextrader för att visa portidentifieraren.

Skriv ned en IP-adress som visas i ifconfig-rapporten. Adresser som anges med portnamn som e0a eller e0b är bra alternativ. Använd inte några IP-adresser som anges med e7*-namn, eftersom dessa namn endast används för iDRAC/IPMI-tjänstportar.  

## <a name="load-the-cluster-configuration-wizard"></a>Läs in guiden klusterkonfiguration

Använd det webbläsarbaserade klusterkonfigurationsverktyget för att skapa klustret. 

Ange IP-adressen för noden i en webbläsare. Om webbläsaren ger ett meddelande om att webbplatsen inte är betrodd, gå vidare till webbplatsen ändå. (Enskilda Azure FXT Edge Filer-noder har inte certifikatutfärdare som tillhandahålls.)

![Inloggningssida för kontrollpanelen i webbläsarfönstret](media/fxt-cluster-create/unconfigured-node-gui.png)

Lämna fälten **Användarnamn** och **Lösenord** tomma. Klicka på **Logga in** om du vill läsa in sidan för att skapa kluster.

![Startskärmen för en okonfigurerad nod på den webbläsarbaserade GUI-kontrollpanelen. Den visar alternativ för att uppdatera programvara, konfigurera ett kluster manuellt eller konfigurera ett kluster från en installationsfil.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Skapa klustret

Verktyget för klusterkonfiguration guidar dig genom en uppsättning skärmar för att skapa Azure FXT Edge Filer-klustret. Se till att du har den [information som krävs](#gather-information-for-the-cluster) klar innan du börjar. 

### <a name="creation-options"></a>Skapa alternativ

Den första skärmen ger tre alternativ. Använd alternativet manuell konfiguration om du inte har särskilda instruktioner från supportpersonalen.

Klicka på **Jag konfigurerar klustret manuellt** för att läsa in den nya skärmen för klusterkonfigurationsalternativ. 

De andra alternativen används sällan:

* "Uppdatera systemavbildningen" uppmanar dig att installera ny OS-programvara innan du skapar klustret. (Den installerade programvaruversionen visas högst upp på skärmen.) Du måste ange programpaketfilen - antingen en URL och användarnamn/lösenord, eller genom att ladda upp en fil från datorn. 

* Alternativet klusterinställningsfil används ibland av Microsofts kundtjänst och support. 

## <a name="cluster-options"></a>Klusteralternativ

Nästa skärm uppmanar dig att konfigurera alternativ för det nya klustret.

Sidan är uppdelad i två huvudavsnitt, **Grundläggande konfiguration** och **nätverkskonfiguration**. Avsnittet nätverkskonfiguration har också underavsnitt: ett **Management** för hanteringsnätverket och ett för **klusternätverket.**

### <a name="basic-configuration"></a>Grundläggande konfiguration

I det övre avsnittet fyller du i grundläggande information för det nya klustret.

![Information om avsnittet "Grundläggande konfiguration" på guidsidan för webbläsaren. Den visar tre fält (klusternamn, admin lösenord, bekräfta lösenord)](media/fxt-cluster-create/basic-configuration.png) 

* **Klusternamn** - Ange ett unikt namn för klustret.

  Klusternamnet måste uppfylla följande kriterier:
  
  * Längd på 1 till 16 tecken
  * Kan innehålla bokstäver, siffror och tecknen för streck (-) och understreck (_) 
  * Får inte innehålla andra skiljetecken eller specialtecken
  
  Du kan ändra det här namnet senare på konfigurationssidan för > **klustergeneralinställningar.** **Cluster** (Mer information om klusterinställningar finns i [guiden för klusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), som inte ingår i den här dokumentationsuppsättningen.)

  > [!NOTE] 
  > Klusternamnet används för att identifiera systeminformation som överförs till stöd för övervakning eller felsökning, så det är bra att inkludera ditt företagsnamn.

* **Admin lösenord** - Ange lösenordet för `admin`standard administrativ användare, .
  
  Du bör ställa in enskilda användarkonton för varje person som administrerar klustret, men du kan inte ta bort användaren `admin`. Logga in `admin` som om du behöver skapa ytterligare användare.
 
  Du kan ändra `admin` lösenordet för på inställningssidan **för** > **Administrationsanvändare** på kontrollpanelen i klustret. Mer information finns i **dokumentationen till användare** i [guiden för klusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Nätverkskonfiguration

I avsnittet **Nätverk** uppmanas du att ange vilken nätverksinfrastruktur som klustret ska använda. 

Det finns två separata nätverk att konfigurera:

* *Hanteringsnätverket* ger administratörsåtkomst till klustret för konfiguration och övervakning. DEN IP-adress som anges här används vid anslutning till Kontrollpanelen eller för SSH-åtkomst. 

  De flesta kluster använder bara en enda hanterings-IP-adress, men om du vill lägga till gränssnitt kan du göra det när du har skapat klustret.

* *Klusternätverket* används för kommunikation mellan klusternoder och mellan klusternoder och serverdelslagring (kärnfilers).

Det klientinriktade nätverket konfigureras senare, efter att klustret har skapats.

Det här avsnittet innehåller även konfiguration för DNS- och NTP-servrar som används av båda nätverken.

### <a name="configure-the-management-network"></a>Konfigurera hanteringsnätverket

Inställningarna i avsnittet **Hantering** gäller för nätverket som ger administratörsåtkomst till klustret.

![detalj i avsnittet "Hantering", med fält för 5 alternativ och en kryssruta för 1 Gb hanteringsnätverk](media/fxt-cluster-create/management-network-filled.png)

* **Hanterings-IP** - Ange den IP-adress som du ska använda för att komma åt klustrets kontrollpanel. Den här adressen kommer att begäras av klustrets primära nod, men den flyttas automatiskt till en felfri nod om den ursprungliga primära noden blir otillgänglig.

  De flesta kluster använder bara en hanterings-IP-adress. Om du vill ha mer än en kan du lägga till dem när du har skapat klustret med hjälp av inställningssidan för > **klusteradministrationsnätverk.** **Cluster** Läs mer i [guiden för klusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Netmask** - Ange nätmask för hanteringsnätverket.

* **Router** - Ange standardgatewayadressen som används av hanteringsnätverket.

* **VLAN-tagg (valfritt)** - Om klustret använder VLAN-taggar anger du taggen för hanteringsnätverket.

  Ytterligare VLAN-inställningar konfigureras på sidan > **Kluster-VLAN-inställningar.** **Cluster** Läs [Arbeta med VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) och [Cluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) i guiden för klusterkonfiguration om du vill veta mer.

* **MTU** - Justera vid behov den maximala överföringsenheten (MTU) för klustrets hanteringsnätverk.

* **Använd 1 Gb mgmt-nätverk** - Markera den här rutan om du vill tilldela de två 1 GbE-nätverksportarna på dina FXT-noder till endast hanteringsnätverket. (Du måste ha 25 GbE/10GbE-portar tillgängliga för all annan trafik.) Om du inte markerar den här rutan använder hanteringsnätverket den tillgängliga porten med högsta hastighet. 

### <a name="configure-the-cluster-network"></a>Konfigurera klusternätverket 

Klusternätverksinställningarna gäller för trafik mellan klusternoderna och mellan klusternoder och kärnfilers.

![detalj i avsnittet "Kluster", med fält för att ange sex värden](media/fxt-cluster-create/cluster-network-filled.png)

* **Första IP** och **senaste IP** - Ange IP-adresser som definierar det intervall som ska användas för intern klusterkommunikation. IP-adresserna som används här måste vara sammanhängande och inte tilldelas av DHCP.

  Du kan lägga till fler IP-adresser när du har skapat klustret. Använd inställningssidan**för klusternätverksnätverk** **Cluster** > [(dokumentationen till guiden Klusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Värdet i **Antal IPs i intervall** beräknas och visas automatiskt.

* **Icke-mgmt netmask (valfritt)** - Ange nätmask för klusternätverket. 

  Systemet föreslår automatiskt det netmask-värde som du angav för hanteringsnätverket. ändra den om det behövs.

* **Klusterrouter (valfritt)** – Ange den standardgatewayadress som används av klusternätverket. 

  Systemet föreslår automatiskt samma gatewayadress som du angav för hanteringsnätverket.

* **Kluster VLAN-tagg (valfritt)** - Om klustret använder VLAN-taggar anger du taggen för klusternätverket.

* **Icke-mgmt MTU (valfritt)** - Justera vid behov den maximala överföringsenheten (MTU) för klusternätverket.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurera klustret DNS och NTP 

Under avsnittet **Kluster** finns fält för att ange DNS- och NTP-servrar och för att aktivera länkaggregering. Dessa inställningar gäller för alla nätverk som klustret använder.

![Information om avsnittet för DNS/NTP-konfiguration, med tre fält för DNS-servrar, fält för DNS-domän och DNS-sökning, tre fält för NTP-servrar och en nedrullningslig meny för länkaggregeringsalternativ](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-server** ( alla dagar) - Ange IP-adressen för en eller flera DNS-servrar (Domain Name System).

  DNS rekommenderas för alla kluster och krävs om du vill använda SMB, AD eller Kerberos. 
  
  Konfigurera klustrets DNS-server för belastningsutjämning för round-robin enligt beskrivningen i [Konfigurera DNS för Azure FXT Edge Filer-klustret](fxt-configure-network.md#configure-dns-for-load-balancing).

* **DNS-domän** - Ange det nätverksdomännamn som klustret ska använda.

* **DNS-sökning** - Ange eventuellt ytterligare domännamn som systemet bör söka efter för att lösa DNS-frågor. Du kan lägga till upp till sex domännamn, avgränsade med blanksteg.

* **NTP-server ( er)** - Ange antingen en eller tre NTP-servrar (Network Time Protocol) i de angivna fälten. Du kan använda värdnamn eller IP-adresser.

* **Länk aggregering** - Länk aggregering kan du anpassa hur Ethernet-portar på klustret FXT-noder hanterar olika typer av trafik. Mer information finns i [Länkaggregering](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) i guiden för klusterkonfiguration.

### <a name="click-the-create-button"></a>Klicka på knappen Skapa

När du har levererat alla nödvändiga inställningar i formuläret klickar du på knappen **Skapa kluster.**

![längst ned ifyllt formulär med markören över knappen skapa längst ned till höger](media/fxt-cluster-create/create-cluster.png)

Systemet visar ett meddelande när du skapar klustret.

![statusmeddelande för klusterkonfiguration i webbläsaren: "FXT-noden skapar nu klustret. Detta kommer att ta flera minuter. När klustret skapas besöker du den här länken för att slutföra konfigurationen." med hyperlänk på "besök denna länk"](media/fxt-cluster-create/creating-message.png)

Efter en stund kan du klicka på länken i meddelandet för att gå till klustrets Kontrollpanel. (Den här länken tar dig till den IP-adress som du angav i **Hanterings-IP**.) Det tar 15 sekunder till en minut innan länken blir aktiv när du klickar på knappen Skapa. Om webbgränssnittet inte läses in väntar du flera sekunder till och klickar sedan på länken igen. 

Det tar en minut eller mer att skapa kluster, men du kan logga in på Kontrollpanelen medan processen pågår. Det är normalt att kontrollpanelens instrumentpanelssida visar varningar tills processen för att skapa kluster är klar. 

## <a name="open-the-settings-pages"></a>Öppna sidorna Inställningar 

När du har skapat klustret måste du anpassa dess konfiguration för nätverket och arbetsflödet. 

Använd webbgränssnittet på Kontrollpanelen för att konfigurera det nya klustret. Följ länken från statusskärmen för klustergenerering eller bläddra till den hanterings-IP-adress som du anger i klustret.

Logga in på webbgränssnittet `admin` med användarnamnet och lösenordet som du angav när du skapar klustret.

![webbläsare som visar inloggningsfält på kontrollpanelen](media/fxt-cluster-create/admin-login.png)

Kontrollpanelen öppnas och visar **instrumentpanelssidan.** När klusterskapandet är klart bör alla varningsmeddelanden rensas från displayen.

Klicka på fliken **Inställningar** för att konfigurera klustret.

På fliken **Inställningar** visar det vänstra sidofältet en meny med konfigurationssidor. Sidorna är ordnade efter kategori. Klicka på kontrollen + eller - högst upp i kategorinamnet om du vill expandera eller dölja de enskilda sidorna.

![Fliken Inställningar på kontrollpanelen (i webbläsaren) med sidan Kluster > Allmänna inställningar inläst](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Steg för klusterinstallation

Vid denna punkt i processen finns ditt kluster, men det har bara en nod, inga klientinriktade IP-adresser och ingen backend-lagring. Ytterligare installationssteg behövs för att gå från ett nyskapat kluster till ett cachesystem som är redo att hantera arbetsflödet.

### <a name="required-configuration"></a>Nödvändig konfiguration

Dessa steg behövs för de flesta eller alla kluster. 

* Lägga till noder i klustret 

  Tre noder är standard, men många produktionskluster har fler - upp till maximalt 24 noder.

  Läs [Lägg till klusternoder](fxt-add-nodes.md) för att lära dig hur du lägger till andra Azure FXT Edge Filer-enheter i klustret och för att aktivera hög tillgänglighet.

* Ange backend-lagring

  Lägg till *grundläggande filerdefinitioner* för varje backend-lagringssystem som klustret ska använda. Läs [Lägg till serverdelslagring och konfigurera virtuellt namnområde](fxt-add-storage.md#about-back-end-storage) om du vill veta mer.

* Konfigurera klientåtkomst och det virtuella namnområdet 

  Skapa minst en virtuell server (vserver) och tilldela den ett IP-adressintervall för klientdatorer att använda. Du måste också konfigurera klusternamnområdet (kallas ibland global namnområde eller GNS), en virtuell filsystemfunktion som gör att du kan mappa backend-lagringsexport till virtuella sökvägar. Klusternamnområdet ger klienter en konsekvent och tillgänglig filsystemstruktur även om du byter backend-lagringsmedia. Namnområdet kan också tillhandahålla en användarvänlig virtuell lagringshierarki för Azure Blob-behållare eller annan molnobjektlagring som stöds.

  Mer information [finns i Konfigurera namnområdet.](fxt-add-storage.md#configure-the-namespace) Det här steget omfattar:
  * Skapa vservers
  * Ställa in korsningar mellan klientnätverksvyn och backend-lagring 
  * Definiera vilka klient-IP-adresser som betjänas av varje vserver

  > [!Note] 
  > Betydande planering rekommenderas innan du börjar ställa in klustrets GNS. Läs avsnittet [Använda ett globalt namnområde](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) och [skapa och arbeta med VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) i klusterkonfigurationsguiden för hjälp.

* [Justera nätverksinställningar](fxt-configure-network.md)

  Det finns flera nätverksrelaterade inställningar som ska verifieras eller anpassas för ett nytt kluster. Läs [Justera nätverksinställningar](fxt-configure-network.md) för information om följande:

  * Verifiera DNS- och NTP-konfiguration 
  * Konfigurera katalogtjänster om det behövs 
  * Ställa in VLAN
  * Konfigurera proxyservrar
  * Lägga till IP-adresser i klusternätverket
  * Lagra krypteringscertifikat

* [Konfigurera supportövervakning](#enable-support)

  Du måste acceptera sekretesspolicyn för konfigurationsverktyget och du bör konfigurera dina inställningar för supportuppladdning samtidigt.

  Klustret kan automatiskt ladda upp felsökningsdata om klustret, inklusive statistik och felsökning av filer. Med dessa uppladdningar kan Microsofts kundtjänst och support ge bästa möjliga service. Du kan anpassa det som övervakas och aktivera proaktiv support och fjärrfelsökning.  

### <a name="optional-configuration"></a>Valfri konfiguration

Dessa steg krävs inte för alla kluster. De behövs för vissa typer av arbetsflöden eller för vissa klusterhanteringsformat. 

* Anpassa nodinställningar

  Du kan ange nodnamn och konfigurera nod IPMI-portar på klusternivå eller individuellt. Om du konfigurerar dessa inställningar innan du lägger till noder i klustret kan de nya noderna hämta inställningarna automatiskt när de ansluter. Alternativen beskrivs i det äldre klusterskapade dokumentet [Anpassa nodinställningar .](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html)

  > [!TIP]
  > Viss dokumentation för den här produkten är ännu inte tillgänglig på Microsoft Azure-dokumentationswebbplatsen. Länkar till [klusterkonfigurationsguiden](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) och den äldre versionen av [guiden för att skapa kluster](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) tar dig till en separat GitHub-värdwebbplats. 

* Konfigurera SMB

  Om du vill tillåta SMB-åtkomst till klustret och NFS måste du konfigurera SMB och aktivera det. SMB (kallas ibland CIFS) används vanligtvis för att stödja Microsoft Windows-klienter.

  Planering för och konfigurera SMB innebär mer än att klicka på några knappar på Kontrollpanelen. Beroende på systemets krav kan SMB påverka hur du definierar kärnfilers, hur många virtuella servrar du skapar, hur du konfigurerar korsningar och namnområde, åtkomstbehörigheter och andra inställningar.

  Mer information finns i avsnittet Klusterkonfigurationsguide [konfigurera SMB Access.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html)

* Installera ytterligare licenser

  Om du vill använda annan molnlagring än Azure Blob måste du installera ytterligare en funktionslicens. Kontakta din Microsoft-representant för mer information om hur du köper en FlashCloud<sup>TM-licens.</sup> Information förklaras i [Lägg till serverdelslagring och konfigurera virtuellt namnområde](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Aktivera support

Azure FXT Edge Filer-klustret kan automatiskt ladda upp supportdata om ditt kluster. Dessa uppladdningar låter personalen ge bästa möjliga kundservice.

Följ dessa steg för att ställa in supportöverföringar.

1. Navigera till sidan Inställningar för > **klusterstöd.** **Cluster** Acceptera sekretesspolicyn. 

   ![Skärmbild som visar Kontrollpanelen och popup-fönstret med knappen Bekräfta för att acceptera sekretesspolicyn](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Klicka på triangeln till vänster om **Kundinformation** för att expandera avsnittet.
1. Klicka på knappen **Ladda upp information om revalidate.**
1. Ange klustrets supportnamn i **Unikt klusternamn** – se till att det identifierar klustret unikt för att stödja personal.
1. Markera rutorna för **statistikövervakning,** **allmän informationsuppladdning**och **kraschinformationsöverföring**.
1. Klicka på **Skicka**.  

   ![Skärmbild som innehåller avsnittet med slutförd kundinformation på sidan supportinställningar](media/fxt-cluster-create/fxt-support-info.png)

1. Klicka på triangeln till vänster om **SPS (Secure Proactive Support)** för att expandera avsnittet.
1. Markera kryssrutan Aktivera **SPS Link**.
1. Klicka på **Skicka**.

   ![Skärmbild som innehåller det slutförda avsnittet Säker proaktiv support på sidan Supportinställningar](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat det grundläggande klustret och accepterat sekretesspolicyn lägger du till resten av klusternoderna. 

> [!div class="nextstepaction"]
> [Lägga till klusternoder](fxt-add-nodes.md)
