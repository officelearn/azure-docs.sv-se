---
title: Microsoft Azure FXT Edge Filer klustret har skapats
description: Så här skapar du ett cachekluster för lagring av hybrid med Azure FXT Edge-Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 1bfe8f0efce0a844263fc65df0ad927114886769
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450543"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Självstudier: Skapa Filer för Azure FXT Edge-kluster

När du installerar och initiera maskinvarunoder Azure FXT Edge Filer för ditt cacheminne, använder du FXT klusterprogramvaran för att skapa cacheklustret. 

Den här självstudien vägleder dig genom stegen för att konfigurera din maskinvarunoder som ett kluster. 

I den här kursen lär du dig: 

> [!div class="checklist"]
> * Vilken information som krävs innan du börjar skapa klustret
> * Skillnaden mellan klustrets hanteringsnätverk, ett klusternätverk och klientinriktade nätverk
> * Hur du ansluter till en klusternod 
> * Så här skapar du ett första-kluster med en nod i Azure FXT Edge Filer
> * Logga in i klustret på Kontrollpanelen för att konfigurera klusterinställningarna

Den här proceduren tar mellan 15 och 45 minuter beroende på hur mycket forskning som du behöver göra för att identifiera IP-adresser och nätverksresurser.

## <a name="prerequisites"></a>Förutsättningar

Slutföra förutsättningarna innan du påbörjar den här självstudiekursen:

* Installera minst tre Azure FXT Edge Filer maskinvarusystem i ditt datacenter 
* Anslut lämplig kraft och nätverk-kablar i systemet  
* Starta minst en nod i Azure FXT Edge Filer och [Ställ in dess rotlösenordet](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Samla in information för klustret 

Du behöver följande information för att skapa klustret Azure FXT Edge Filer:

* Klusternamn

* Lösenordet för administratörer att ange för klustret

* IP-adresser:

  * En IP-adress för hantering av kluster, och nätmask och router för hanteringsnätverket
  * De första och sista IP-adresserna i ett sammanhängande intervall av IP-adresser för klusterkommunikation (nod till nod). Se [IP-adress-distribution](#ip-address-distribution)nedan för information. 
  * (Klientinriktade IP-adresser anges när klustret har skapats.)

* Information om nätverk infrastruktur:

  * IP-adressen för en DNS-server för klustret
  * Namnet på DNS-domän för klustret
  * Namn eller IP-adress för klustret NTP-servrar (antingen en server eller tre eller fler) 
  * Om du vill aktivera IEEE 802.1ax-2008 länka sammanställningar för klustrets gränssnitt
  * Om du aktiverar Länkaggregering, om du använder IEEE 802.3ad (LACP) eller inte dynamisk sammansättning

Du kan konfigurera dessa nätverk infrastruktur när du skapar klustret, men det är bättre att göra det vid tidpunkten för skapandet. 

### <a name="ip-address-distribution"></a>IP-adress-distribution

Azure FXT Edge Filer hybrid lagringsklustret cache använder IP-adresser i tre kategorier:

* Hantering av IP: En IP-adress för klusterhantering

  Den här adressen fungerar som startpunkt för att få åtkomst till konfigurationsverktygen kluster (webbaserade Kontrollpanelen eller XML-RPC-API). Den här adressen tilldelas automatiskt till den primära noden i klustret och den flyttas automatiskt om den primära noden ändras.

  Andra IP-adresser kan användas för åtkomst till Kontrollpanelen, men den IP-adressen för hantering har utformats för att ge åtkomst även om enskilda noder växlar över.

* Klusternätverk: Ett intervall med IP-adresser för klusterkommunikation

  Klusternätverket används för kommunikation mellan klusternoder och att hämta filer från serverdelslagring (core-filter).

  **Rekommendation:** Tilldela en IP-adress per fysiska port som används för klusterkommunikation på varje nod i Azure FXT Edge Filer. Klustret tilldelar automatiskt adresser i det angivna intervallet till enskilda noder.

* Klientinriktade nätverk: Intervall med IP-adresser som klienter använder för begäran och skriva filer

  Klienten nätverksadresser som används av klienter på dataåtkomst core filer i klustret. Till exempel kan en NFS-klient montera någon av dessa adresser.

  **Rekommendation:** Tilldela en IP-adress per fysiska port som används för klientkommunikation i varje FXT Series-nod.

  Klustret distribuerar klientinriktade IP-adresser i dess ingående noder så jämnt som möjligt.

  För enkelhetens skull konfigurera många administratörer ett enda DNS-namn med resursallokering DNS (RRDNS) konfiguration för att göra det enklare att distribuera klientbegäranden över adressintervallet. Den här konfigurationen kan också alla klienter använder samma mount-kommando för åtkomst till klustret. Läs [konfigurera DNS](fxt-configure-network.md#configure-dns-for-load-balancing) för mer information.

Hantering av IP-adress och en intervallet av nätverksadresser för klustret måste anges för att skapa ett nytt kluster. Klientinriktade adresser anges när klustret har skapats.

## <a name="connect-to-the-first-node"></a>Ansluta till den första noden

Du kan ansluta till någon av de installera FXT noderna och använda dess OS-programmet för att konfigurera klustret.

Om du inte redan har gjort det, ström på minst en av noderna FXT för klustret, och kontrollera att den har en nätverksanslutning och en IP-adress. Du måste ange ett nytt rotlösenord för att aktivera noden, så Följ stegen i [ange maskinvara lösenord](fxt-node-password.md) om du inte redan har gjort.

Se till att nodens nätverkslänken led: ar belyses (och eventuellt indikatorer på nätverket växla till som den är ansluten) om du vill kontrollera nätverksanslutningen. Övervakningsindikatorer beskrivs i [övervaka Azure FXT Edge Filer maskinvarustatus](fxt-monitor.md).

När noden startar den kommer att begära en IP-adress. Om den är ansluten till en DHCP-server, godkänner IP-adress har angetts via DHCP. (Den här IP-adressen är tillfällig. Den ändras när du skapar klustret.)

Om den inte är ansluten till en DHCP-server eller ett svar inte emot använder noden Bonjour programvara för att ange en egen tilldelade IP-adress i formatet 169.254. \*. \*. Dock bör du ange en tillfällig statisk IP-adress på en av nodens nätverkskort innan du använder det för att skapa ett kluster. Instruktioner finns i den här äldre dokumentet. Kontakta Microsoft Service och Support för uppdaterad information: [Bilaga A: Ange en statisk IP-adress på en nod FXT](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Hitta IP-adress

Ansluta till noden Azure FXT Edge Filer att hitta dess IP-adress. Du kan använda en seriekabel direkt anslutning till USB- och VGA-portar, eller ansluta via en KVM-växel. (Portanslutning information finns i [Ange inledande lösenord](fxt-node-password.md).)

När du ansluter kan logga in med användarnamnet `root` och lösenordet som du angav när du startade noden för första gången.  

Efter inloggningen måste du bestämma nodens IP-adress.

Använd kommandot `ifconfig` att se de adresserna som tilldelats till det här systemet.

Till exempel kommandot `ifconfig | grep -B5 inet` söker efter portar med internet-adresser och ger fem rader med kontext för att visa identifieraren.

Anteckna IP-adresser visas i ifconfig-rapporten. Adresser som visas i listan med portnamn som e0a eller e0b alternativen är bra. Använd inte alla IP-adresser som visas i listan med e7 * namn, eftersom dessa namn används bara för IPMI portar inte vanlig nätverksportar.  

## <a name="load-the-cluster-configuration-wizard"></a>Läsa in guiden Konfigurera kluster

Använd konfigurationsverktyget webbläsarbaserade kluster för att skapa klustret. 

Ange IP-adressen för noden i en webbläsare. Om webbläsaren ger ett meddelande om webbplatsen är betrodd, fortsätter du till webbplatsen ändå. (Noder för enskilda Azure FXT Edge Filer har inte CA-angivna säkerhetscertifikat.)

![Kontrollpanelen på inloggningssidan i webbläsarfönster](media/fxt-cluster-create/unconfigured-node-gui.png)

Lämna den **användarnamn** och **lösenord** fält tomt. Klicka på **inloggning** att läsa in sidan för att skapa klustret.

![Inledande konfiguration av företagsåtkomst för en Okonfigurerade nod webbläsarbaserade GUI på Kontrollpanelen. Den visar alternativ för att uppdatera programvara, konfigurera ett kluster manuellt eller konfigurera ett kluster från en installationsfil.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Skapa klustret

Kluster-konfigurationsverktyget vägleder dig genom en uppsättning skärmar för att skapa klustret Azure FXT Edge Filer. Kontrollera att du har den [nödvändig information](#gather-information-for-the-cluster) redo innan du börjar. 

### <a name="creation-options"></a>Alternativ för att skapa

Den första skärmen finns tre alternativ. Använd alternativet för manuell konfiguration om du inte har särskilda instruktioner från supportpersonalen.

Klicka på **jag konfigurerar klustret manuellt** att läsa in det nya klustret alternativ konfigurationsskärmen. 

De andra alternativen används sällan:

* ”Uppdatera en operativsystemavbildning” uppmanas du att installera nya OS-programvara innan du skapar klustret. (Den aktuella installerade programvaruversionen visas överst på skärmen.) Du måste ange paketfilen programvara – antingen en URL och användarnamn/lösenord eller genom att överföra en fil från datorn. 

* Klustret filen installationsalternativet används ibland av Microsofts kundservice och Support. 

## <a name="cluster-options"></a>Klusteralternativ

Nästa sida uppmanas du att konfigurera alternativ för det nya klustret.

Sidan är uppdelad i två huvudavsnitt **grundkonfiguration** och **nätverkskonfiguration**. Konfigurationsavsnittet nätverk har också underavsnitt: en för den **Management** nätverk och en för den **kluster** nätverk.

### <a name="basic-configuration"></a>Grundläggande konfiguration

I det översta avsnittet fyller du i grundläggande information för det nya klustret.

![Information om ”grundläggande” konfigurationsavsnittet i Webbläsarsida GUI. Den visar tre fält (klustrets namn, administratörslösenord, bekräfta lösenord)](media/fxt-cluster-create/basic-configuration.png) 

* **Klusternamn** -ange ett unikt namn för klustret.

  Klusternamnet måste uppfylla följande kriterier:
  
  * Längden på 1 och 16 tecken
  * Kan innehålla bokstäver, siffror och bindestreck (-) och understreck (_) 
  * Får inte innehålla andra skiljetecken eller specialtecken
  
  Du kan ändra det här namnet senare på den **kluster** > **allmänna installationsalternativ** konfigurationssidan. (Mer information om inställningarna för den [kluster konfigurationsguide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), som inte är en del av den här dokumentationsuppsättningen.)

  > [!NOTE] 
  > Klusternamnet används för att identifiera systeminformation som laddats upp för att ge stöd för övervakning och felsökning, så är det bra att inkludera namnet på ditt företag.

* **Adminlösenord** -ange lösenordet för den administrativa användaren i standard `admin`.
  
  Du bör konfigurera enskilda användarkonton för varje person som administrerar klustret, men du kan inte ta bort användaren `admin`. Logga in som `admin` om du vill skapa ytterligare användare.
 
  Du kan ändra lösenordet för `admin` i den **Administration** > **användare** inställningssidan i klustret på Kontrollpanelen. Mer information finns i **användare** dokumentationen i den [kluster konfigurationsguide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Nätverkskonfiguration

Den **nätverk** avsnittet uppmanas du att ange nätverksinfrastrukturen som klustret ska använda. 

Det finns två separata nätverk för att konfigurera:

* Den *hanteringsnätverket* ger administratören tillgång till klustret för konfiguration och övervakning. IP-adressen som anges här används när du ansluter till Kontrollpanelen eller för SSH-åtkomst. 

  De flesta kluster använda endast en enda IP-adress för hantering, men om du vill lägga till gränssnitt kan du göra det när du har skapat klustret.

* Den *klusternätverk* används för kommunikation mellan klusternoder och mellan klusternoder och backend-lagring (core-filter).

Klientinriktade nätverket konfigureras senare, när klustret har skapats.

Det här avsnittet innehåller även konfigurationen för DNS- och NTP-servrar som används av båda nätverken.

### <a name="configure-the-management-network"></a>Konfigurera hanteringsnätverket

Inställningarna i den **Management** avsnittet avser det nätverk som tillhandahåller administratörsåtkomst till klustret.

![information om ”Management”-avsnittet, med fält för 5 alternativ och en kryssruta för 1Gb hanteringsnätverket](media/fxt-cluster-create/management-network-filled.png)

* **Hantering av IP** -IP-adressen som du använder för att få åtkomst till klustret på Kontrollpanelen. Den här adressen kommer att krävas av klustrets primära noden, men den flyttas automatiskt till en felfri nod om den ursprungliga primära noden blir otillgänglig.

  De flesta kluster använda endast en IP-adress för hantering. Om du vill ha mer än en, du kan lägga till dem när du har skapat klustret med hjälp av den **kluster** > **administrativa nätverket** inställningssidan. Läs mer i den [kluster konfigurationsguide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Nätmask** – ange nätmasken för hanteringsnätverket.

* **Router** -Ange standard-gateway-adress som används av hanteringsnätverket.

* **VLAN-tagg (valfritt)** – om klustret använder VLAN-taggar, ange taggen för hanteringsnätverket.

  Ytterligare VLAN-inställningar har konfigurerats i den **kluster** > **VLAN** inställningssidan. Läs [arbeta med VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) och [kluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) i klustret konfigurationsguide vill veta mer.

* **MTU** – om det behövs justera den högsta överföringsenheten (MTU) för hantering av klusternätverk.

* **Använd 1Gb mgmt nätverk** – den här kryssrutan om du vill tilldela två 1GbE nätverket portar på dina FXT noder till hanteringsnätverket endast. Om du inte markerar kryssrutan, använder hanteringsnätverket det högsta hastighet portnummer som är tillgängliga. 

### <a name="configure-the-cluster-network"></a>Konfigurera klusternätverket 

Nätverksinställningarna för klustret gäller för trafik mellan noder i klustret och mellan klusternoder och core filter.

![information om ”kluster”-avsnittet, med fält som ska ange sex värden](media/fxt-cluster-create/cluster-network-filled.png)

* **Första IP-Adressen** och **senaste IP** – ange de IP-adresser som definierar adressintervallet som ska användas för intern klusterkommunikation. IP-adresser som används här måste vara sammanhängande och inte tilldelad av DHCP.

  Du kan lägga till fler IP-adresser när du har skapat klustret. Använd den **kluster** > **klusternätverk** inställningssidan ([kluster konfigurationsguide dokumentation](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Värdet i **tal med IP-adresser i intervallet** beräknas och visas automatiskt.

* **Icke-mgmt-nätmask (valfritt)** – ange nätmasken för ett klusternätverk. 

  Systemet föreslår automatiskt nätmaskvärdet för som du angav för hanteringsnätverket; ändra det om det behövs.

* **Kluster-router (valfritt)** -ange standardgatewayadressen som används av nätverket för klustret. 

  Systemet föreslår automatiskt samma gatewayadress som du angav för hanteringsnätverket.

* **Kluster-VLAN-tagg (valfritt)** – om klustret använder VLAN-taggar, ange taggen för nätverket för klustret.

* **Icke-mgmt-MTU (valfritt)** – om det behövs justera maximal överföringsenhet (MTU) för ditt klusternätverk.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurera DNS-och NTP 

Under den **kluster** avsnittet det finns fält för att ange DNS- och NTP-servrar och för att aktivera Länkaggregering. Dessa inställningar gäller för alla nätverk som klustret använder.

![Detalj i avsnittet för DNS-/ NTP-konfiguration, med tre fält för DNS-servrar, fält för DNS-domän och DNS-sökning, tre fält för NTP-servrar, och en nedrullningsbar meny för aggregering Länkalternativen](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-servrar** – ange IP-adressen för en eller flera domain name system DNS-servrar.

  DNS är rekommenderas för alla kluster och krävs om du vill använda SMB, AD- eller Kerberos. 
  
  För optimala prestanda, konfigurera klustrets DNS-server för belastningsutjämning (round-robin) enligt beskrivningen i [konfigurera DNS för Azure FXT Edge Filer klustret](fxt-configure-network.md#configure-dns-for-load-balancing).

* **DNS-domän** -ange domännamnet nätverk i klustret ska använda.

* **DNS-sökning** – du kan också ange ytterligare domännamn som systemet ska söka för att matcha DNS-frågor. Du kan lägga till upp till sex domännamn, avgränsade med blanksteg.

* **NTP-servrar** -ange ett eller tre network time protocol (NTP) servrar i fälten. Du kan använda värdnamn eller IP-adresser.

* **Länka aggregering** -Link aggregation kan du anpassa hur ethernet-portar på noderna i FXT hanterar olika typer av trafik. Mer information, [Link Aggregation](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) i konfigurationsguiden för klustret.

### <a name="click-the-create-button"></a>Klicka på Skapa

När du har angett alla nödvändiga inställningar i formuläret klickar du på den **Skapa kluster** knappen.

![längst ned på ifyllda formuläret med markören över knappen Skapa längst ned till höger](media/fxt-cluster-create/create-cluster.png)

Ett meddelande visas när du skapade klustret.

![klustret configuration statusmeddelande i webbläsaren: ”Noden FXT är nu att skapa klustret. Detta kan ta flera minuter. När klustret har skapats Besök den här länken om du vill slutföra konfigurationen ”. med hyperlänk på ”Gå till den här länken”](media/fxt-cluster-create/creating-message.png)

Efter en liten stund kan du klicka på länken i meddelandet för att gå till Kontrollpanelen-klustret. (Den här länken tar dig till IP-adressen som du angav i **hantering IP**.) Det tar 15 sekunder till en minut för att länken ska aktiveras när du klickar på knappen Skapa. Om webbgränssnittet läses inte in, vänta flera sekunder och sedan klickar du på länken igen. 

Skapa kluster tar en minut eller mer, men du kan logga in på Kontrollpanelen när processen pågår. Det är normalt i Kontrollpanelen instrumentpanelssidan att visa varningar tills klusterskapningsprocessen har slutförts. 

## <a name="open-the-settings-pages"></a>Öppna Inställningssidorna 

Du behöver anpassa konfigurationen för ditt nätverk och ett arbetsflöde när du har skapat klustret. 

Använda Kontrollpanelen Webbgränssnitt för att ställa in det nya klustret. Följ länken från skärmen med registreringsstatus ditt kluster skapas eller bläddra till hantering av IP-adressen som du angett för klustret.

Logga in på webbgränssnitt med användarnamnet `admin` och lösenordet som du anger när du skapar klustret.

![webbläsaren och visar panelen inloggningen fält för kontroll](media/fxt-cluster-config/admin-login.png)

Kontrollpanelen öppnas och visar den **instrumentpanelen** sidan. Eftersom det är klar att skapa ett kluster, avmarkera alla varningsmeddelanden som visas.

Klicka på den **inställningar** fliken för att konfigurera klustret.

På den **inställningar** fliken i vänster Sidopanel visas en meny med konfigurationssidor. Sidorna är ordnade efter kategori. Klicka på + eller - kontroll högst upp på kategorinamn för att visa eller dölja enskilda sidor.

![Inställningsfliken i Kontrollpanelen (i webbläsaren) med klustret > Allmänt-konfigurationssidan läses in](media/fxt-cluster-config/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Steg för konfiguration av kluster

I det här läget i processen är ditt kluster finns, men den har endast en nod, inga klientinriktade IP-adresser och ingen backend-lagring. Steg för ytterligare konfiguration krävs för att gå från ett nyskapat kluster till ett cache-system som är redo att hantera ditt arbetsflöde.

### <a name="required-configuration"></a>Konfiguration som krävs

Dessa steg behövs för de flesta eller alla kluster. 

* Lägga till noder i klustret 

  Tre noder som är standard, men många produktionskluster har mer – upp till högst 24 noder.

  Läs [lägger till klusternoder](fxt-add-nodes.md) att lära dig hur du lägger till andra Filer för Azure FXT Edge-enheter i ditt kluster och aktivera hög tillgänglighet.

* Ange backend-lagring

  Lägg till *viktiga filer* definitioner för varje backend-lagringssystemet som klustret ska använda. Läs [lägga till backend-lagring och konfigurera virtuella namnområde](fxt-add-storage.md#about-back-end-storage) vill veta mer.

* Konfigurera klientåtkomst och virtuella namnområdet 

  Skapa minst en virtuell server (vserver) och tilldela den ett IP-adressintervall för klientdatorer som du använder. Du måste också konfigurera kluster-namnområdet (kallas ibland globala Namespace eller GN), ett virtuellt filsystem-funktion som du kan mappa backend-lagring exporter till virtuella sökvägar. Kluster-namnområde ger klienter en konsekvent och komma åt filsystemet struktur även om du växlar backend-lagringsmedia. Namnområdet kan också tillhandahålla en användarvänliga virtuell lagring hierarki för Azure Blob-behållare eller andra stöds molnobjektlagring.

  Läs [konfigurera namnområdet](fxt-add-storage.md#configure-the-namespace) mer information. Det här steget omfattar:
  * Skapa vservers
  * Konfigurera vägkorsningar mellan den klienten nätverkslagring vy och serverdelen 
  * Definiera vilka klient-IP hanteras adresser av varje vserver

  > [!Note] 
  > Betydande planera rekommenderas innan du börjar konfigurera klustrets GN. Läs den [med hjälp av en Global Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) och [skapa och arbeta med VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) avsnitt i konfigurationsguiden för klustret om du behöver hjälp.

* [Justera nätverksinställningar](fxt-configure-network.md)

  Det finns flera nätverksrelaterade inställningar som ska verifieras eller som är anpassad efter ett nytt kluster. Läs [justera nätverksinställningar](fxt-configure-network.md) mer information om de här objekten:

  * Verifiera DNS- och NTP-konfiguration 
  * Konfigurera katalogtjänster, om det behövs 
  * Konfigurera virtuella lokala nätverk
  * Konfigurera proxy-servrar
  * Att lägga till IP-adresser till ett klusternätverk
  * Lagra krypteringscertifikat

* [Konfigurera stöd för övervakning](#enable-support)

  Du måste acceptera sekretesspolicy för configuration tool och bör du konfigurera dina inställningar för överföring av support på samma gång.

  Klustret kan automatiskt ladda upp felsökning data om ditt kluster, inklusive statistik och felsökning filer. Dessa överföringar kan Microsofts kundservice och Support som ger bästa möjliga service. Du kan anpassa vad övervakas och välja att aktivera proaktiv support och felsökning fjärrtjänsten.  

### <a name="optional-configuration"></a>Valfri konfiguration

Dessa steg krävs inte för alla kluster. De krävs för vissa typer av arbetsflöden eller för vissa format för hantering av kluster. 

* Anpassa inställningar för noden

  Du kan ange nodnamn och konfigurera noden IPMI portar på ett kluster hela nivå eller individuellt. Om du konfigurerar inställningarna innan du lägger till noder i klustret, kan de nya noderna ta över inställningarna automatiskt när de ansluter. Alternativen beskrivs i dokumentet för äldre kluster skapas [Anpassa inställningar för noden](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Viss dokumentation för den här produkten ännu inte finns tillgänglig på webbplatsen Microsoft Azure-dokumentationen. Länkar till den [kluster konfigurationsguide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) och den äldre versionen av den [Guide för skapande av kluster](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) tar dig till en separat GitHub-värdbaserade webbplats. 

* Konfigurera SMB

  Om du vill tillåta SMB-åtkomst till ditt kluster, samt NFS, måste du konfigurera SMB och aktivera den. SMB (kallas ibland CIFS) används vanligtvis för Microsoft Windows-klienter.

  Planera och konfigurera SMB innebär att mer än att klicka på några knappar i Kontrollpanelen. Beroende på systemkrav, SMB kan påverka hur du definierar core filter, hur många vservers som du skapar, hur du konfigurerar din vägkorsningar och namnrymd, behörigheter och andra inställningar.

  Mer information finns i konfigurationsguiden för klustret [konfigurera SMB-åtkomst](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) avsnittet.

* Installera ytterligare licenser

  Om du vill använda molnlagring än Azure Blob, måste du installera en ytterligare funktion-licens. Kontakta din Microsoft-representant för mer information om hur du köper en FlashCloud<sup>TM</sup> licens. Information om beskrivs i [lägga till backend-lagring och konfigurera virtuella namnområde](fxt-add-storage.md#about-back-end-storage).


### <a name="enable-support"></a>Aktivera stöd för

Azure FXT Edge Filer klustret kan automatiskt ladda upp supportinformation om ditt kluster. Dessa överföringar kan personal som ger bästa möjliga kundservice.

Följ dessa steg för att konfigurera stöd för överföringar.

1. Navigera till den **kluster** > **Support** inställningssidan. Godkänn sekretesspolicyn. 

   ![Skärmbild som visar på Kontrollpanelen och popup-fönster med bekräfta-knappen för att acceptera sekretesspolicy](media/fxt-cluster-config/fxt-privacy-policy.png)

1. Klicka på triangeln till vänster om **kundinformation** att expandera avsnittet.
1. Klicka på den **Revalidate överföringsinformation** knappen.
1. Ange klusternamnet stöd i **unika klusternamnet** – Kontrollera att den identifierar ditt kluster för att stödja personal.
1. Markera kryssrutorna för **statistik övervakning**, **allmän Information överför**, och **krascha ladda upp Information**.
1. Klicka på **Skicka**.  

   ![Skärmbild som innehåller slutfört kunden info-avsnittet i inställningssidan för support](media/fxt-cluster-config/fxt-support-info.png)

1. Klicka på triangeln till vänster om **skydda proaktiv Support (Service Pack)** att expandera avsnittet.
1. Markera kryssrutan för **aktivera Service Pack-länk**.
1. Klicka på **Skicka**.

   ![Skärmbild som innehåller slutfört skydda proaktiv stöder avsnittet på inställningssidan för support](media/fxt-cluster-config/fxt-support-sps.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat grundläggande klustret och accepterat sekretesspolicy, lägga till resten av noderna i klustret. 

> [!div class="nextstepaction"]
> [Lägg till noder](fxt-add-nodes.md)
