---
title: Justera nätverksinställningar för Microsoft Azure FXT Edge Filer-kluster
description: Hur du anpassar nätverksinställningar när du har skapat klustret Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 36ed354304cb1c88e48088f4b36c1ad0350af0dc
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542997"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Självstudier: Konfigurera inställningar för klustrets 

Innan du använder ett nyskapat kluster i Azure FXT Edge Filer bör du kontrollera och anpassa flera nätverksinställningar för arbetsflödet. 

Den här självstudien beskrivs de nätverksinställningar som du kan behöva justera för ett nytt kluster. 

Du kommer att lära dig: 

> [!div class="checklist"]
> * Vilka nätverksinställningar kan behöva uppdateras när du har skapat ett kluster
> * Användningsfall för vilka Azure FXT Edge Filer kräver en AD-server eller en DNS-server 
> * Så här konfigurerar du resursallokering DNS (RRDNS) att automatiskt klienten belastningsutjämna förfrågningar till FXT-kluster

Hur lång tid det tar för att slutföra de här stegen beror på hur många konfigurationsändringar krävs i systemet:

* Om du bara behöver läsa igenom självstudien och kontrollera inställningarna för några, bör det ta 10 – 15 minuter. 
* Om du vill konfigurera DNS-resursallokering kan aktiviteten Ta en timme eller mer.

## <a name="adjust-network-settings"></a>Justera nätverksinställningar

Flera nätverksrelaterade uppgifter är en del av hur du konfigurerar ett nytt kluster i Azure FXT Edge Filer. Kontrollera i listan och avgör vilka som gäller för ditt system.

Mer information om nätverksinställningar för klustret [konfigurera nätverkstjänster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) i konfigurationsguiden för klustret.

* Konfigurera DNS för resursallokering för klientinriktade nätverket (valfritt)

  Läsa in belastningsutjämna trafik genom att konfigurera DNS-systemet enligt beskrivningen i [konfigurera DNS för klustret FXT Edge Filer](#configure-dns-for-load-balancing).

* Kontrollera inställningarna för NTP

* Konfigurera Active Directory och användarnamn/gruppnamn nedladdningar (vid behov)

  Om dina nätverksvärdar använder Active Directory eller en annan typ av extern katalogtjänst, måste du ändra klustrets directory services-konfiguration för att ställa in hur klustret hämtar användarnamn-och gruppinformation. Läs **kluster** > **katalogtjänster** i klustret konfigurationsguide för information.

  En AD-server krävs om du vill ha stöd för SMB. Konfigurera AD innan du börjar konfigurera SMB.

* Definiera VLAN (valfritt)
  
  Konfigurera eventuella ytterligare virtuella lokala nätverk som krävs innan du definierar ditt kluster vservers och globalt namnområde. Läs [arbeta med VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) i klustret konfigurationsguide vill veta mer.

* Konfigurera proxy-servrar (vid behov)

  Om klustret använder en proxyserver för att nå externa adresser, Följ dessa steg för att ställa in:

  1. Definiera proxyservern i den **proxykonfiguration** inställningssidan
  1. Tillämpa proxyserverkonfiguration med den **kluster** > **allmänna installationsalternativ** sidan eller **Core Filer information** sidan.
  
  Mer information finns i [med webbproxyservrar](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) i konfigurationsguiden för klustret.

* Ladda upp [krypteringscertifikat](#encryption-certificates) för klustret ska använda (valfritt)

### <a name="encryption-certificates"></a>Krypteringscertifikat

FXT Edge Filer klustret använder X.509-certifikat för dessa funktioner:

* Att kryptera trafiken för administration av klustret

* Att autentiseras för en klient till KMIP-servrar från tredje part

* För att verifiera servercertifikat för molnproviders

Om du vill ladda upp certifikat till klustret kan använda den **kluster** > **certifikat** inställningssidan. Mer information finns i den [kluster > certifikat](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) sidan i guiden för konfiguration av kluster.

För att kryptera management klusterkommunikation, använda den **kluster** > **allmänna installationsalternativ** inställningssidan för att välja vilka certifikat som ska användas för administrativa SSL.

> [!Note] 
> Cloud service-åtkomstnycklar lagras med hjälp av den **Molnautentiseringsuppgifter** konfigurationssidan. Den [lägga till en core filer](fxt-add-storage.md#add-a-core-filer) ovan visar ett exempel; Läs guiden för konfiguration av kluster [Molnautentiseringsuppgifter](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) information. 

## <a name="configure-dns-for-load-balancing"></a>Konfigurera DNS för belastningsutjämning

Det här avsnittet förklarar grunderna för att konfigurera ett system med resursallokering DNS (RRDNS) för att distribuera klientbelastningen bland alla klientinriktade IP-adresser i klustret FXT Edge-Filer. 

### <a name="decide-whether-or-not-to-use-dns"></a>Bestäm om du använder DNS eller inte

Utjämning av nätverksbelastning rekommenderas alltid, men du behöver alltid använda DNS. Det kan till exempel vara bättre att använda ett skript för att tilldela IP-adresser jämnt mellan klienter när de monterar klustret med vissa typer av arbetsflöden för klienten. Vissa metoder beskrivs i [montera klustret](fxt-mount-clients.md). 

Ha detta i åtanke när du bestämmer huruvida du ska använda en DNS-server: 

* Om datorn används av NFS-klienter, krävs DNS inte. Det är möjligt att ange alla adresser i nätverket med hjälp av numeriska IP-adresser. 

* Om systemet har stöd för SMB (CIFS) åtkomst, krävs DNS, eftersom du måste ange en DNS-domän för Active Directory-servern.

* DNS krävs om du vill använda Kerberos-autentisering.

### <a name="round-robin-dns-configuration-details"></a>Information om DNS-resursallokering

När klienter har åtkomst till klustret, balanserar RRDNS automatiskt sina begäranden mellan alla tillgängliga gränssnitt.

Konfigurera din DNS-server för att hantera klientinriktade kluster-adresser som du ser i följande diagram för optimala prestanda.

En kluster-vserver visas till vänster och IP-adresser visas i mitten och till höger. Konfigurera varje klientåtkomstpunkt med A-poster och pekare som på bilden.

![Klustret resursallokering DNS-diagram - detaljerad alternativtext länk följer bild](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[detaljerad beskrivning](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Varje klientinriktade IP-adress måste ha ett unikt namn för intern användning av klustret. (I det här diagrammet klientens IP-adresser är namngivna vs1-klient - IP-* för tydlighetens skull, men i produktion bör du välja något kortare, som klienten *.)

Klienter montera klustret med namnet vserver som argumentet server. 

Ändra din DNS-server ``named.conf`` fil att ange cykliskt för frågor till dina vserver. Det här alternativet innebär att alla tillgängliga värden gås igenom. Lägg till en instruktion som liknar följande:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Följande ``nsupdate`` kommandon ge ett exempel på hur du konfigurerar DNS korrekt:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>Aktivera DNS i klustret 

Ange DNS-server som klustret använder i den **kluster** > **administrativa nätverket** inställningssidan. Inställningarna på sidan innefattar:

* DNS-serveradress
* DNS-domännamn
* DNS search domains

Mer information finns i [DNS-inställningarna](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) i konfigurationsguiden för klustret.

## <a name="next-steps"></a>Nästa steg

Det här är det sista steget för grundläggande konfiguration för klustret Azure FXT Edge Filer. 

* Läs mer om systemets led: ar och andra indikatorer [övervaka maskinvarustatus](fxt-monitor.md).
* Lär dig mer om hur klienter ska ansluta FXT Edge Filer kluster i [montera klustret](fxt-mount-clients.md). 
* Mer information om använda och hantera ett kluster för FXT Edge Filer finns i den [kluster konfigurationsguide](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 