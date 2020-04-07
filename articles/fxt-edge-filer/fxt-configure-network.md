---
title: 'Självstudiekurs: Konfigurera nätverk i ett Azure FXT Edge Filer-kluster'
description: Anpassa nätverksinställningarna när du har skapat Azure FXT Edge Filer-klustret
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 9b0154889544e0054e309cc5f43851b73b4396b4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754687"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Självstudiekurs: Konfigurera klustrets nätverksinställningar

Innan du använder ett nyligen skapat Azure FXT Edge Filer-kluster bör du kontrollera och anpassa flera nätverksinställningar för arbetsflödet. 

I den här självstudien beskrivs de nätverksinställningar som du kan behöva justera för ett nytt kluster. 

Du kommer att lära dig: 

> [!div class="checklist"]
> * Vilka nätverksinställningar som kan behöva uppdateras när du har skapat ett kluster
> * Vilka Azure FXT Edge Filer-användningsfall kräver en AD-server eller en DNS-server 
> * Konfigurera round-robin DNS (RRDNS) för att automatiskt läsa in klientbegäranden för belastningsutjämning till FXT-klustret

Hur lång tid det tar att slutföra dessa steg beror på hur många konfigurationsändringar som behövs i systemet:

* Om du bara behöver läsa igenom handledningen och kontrollera några inställningar, bör det ta 10 till 15 minuter. 
* Om du behöver konfigurera round-robin DNS kan den uppgiften ta en timme eller mer.

## <a name="adjust-network-settings"></a>Justera nätverksinställningar

Flera nätverksrelaterade uppgifter är en del av att konfigurera ett nytt Azure FXT Edge Filer-kluster. Kontrollera den här listan och bestäm vilka som gäller för ditt system.

Mer information om nätverksinställningar för klustret finns i [Konfigurera nätverkstjänster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) i guiden klusterkonfiguration.

* Konfigurera ROUND-robin DNS för det klientinriktade nätverket (valfritt)

  Belastningsjämna klustertrafik genom att konfigurera DNS-systemet enligt beskrivningen i [Konfigurera DNS för FXT Edge Filer-klustret](#configure-dns-for-load-balancing).

* Verifiera NTP-inställningar

* Konfigurera hämtningar av Active Directory och användarnamn/gruppnamn (om det behövs)

  Om nätverksvärdarna använder Active Directory eller någon annan typ av extern katalogtjänst måste du ändra klustrets konfiguration av katalogtjänster för att ställa in hur klustret hämtar användarnamn och gruppinformation. Mer information finns i > **Klusterkatalogtjänster** i guiden Klusterkonfiguration. **Cluster**

  En AD-server krävs om du vill ha SMB-stöd. Konfigurera AD innan du börjar ställa in SMB.

* Definiera VLAN (valfritt)
  
  Konfigurera eventuella ytterligare VLAN som behövs innan du definierar klustrets virtuella servrar och globala namnområde. Läs [Arbeta med VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) i guiden för klusterkonfiguration om du vill veta mer.

* Konfigurera proxyservrar (om det behövs)

  Om klustret använder en proxyserver för att nå externa adresser gör du så här för att konfigurera den:

  1. Definiera proxyservern på sidan **Inställningar för proxykonfiguration**
  1. Använd proxyserverkonfigurationen med sidan **Konfigurera klusterkonfiguration** > **General Setup** eller sidan **Kärnfilerinformation.**
  
  Mer information finns i [Använda webbprogram i](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) klusterkonfigurationsguiden.

* Ladda upp [krypteringscertifikat för klustret](#encryption-certificates) som ska användas (valfritt)

### <a name="encryption-certificates"></a>Krypteringscertifikat

FXT Edge Filer-klustret använder X.509-certifikat för följande funktioner:

* Så här krypterar du klusteradministrationstrafik

* Så här autentiserar du för en klients räkning till KMIP-servrar från tredje part

* För att verifiera molnleverantörers servercertifikat

Om du behöver överföra certifikat till klustret använder du**inställningssidan för klustercertifikat.** **Cluster** >  Information finns på sidan [Kluster > certifikat](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) i guiden klusterkonfiguration.

Om du vill kryptera klusterhanteringskommunikation använder du inställningsinställningssidan **För kluster** > **allmänna** inställningar för att välja vilket certifikat som ska användas för administrativa TLS.

> [!Note] 
> Åtkomstnycklar för molntjänst lagras med hjälp av konfigurationssidan **för molnautentiseringsuppgifter.** Avsnittet [Lägg till en kärna filer](fxt-add-storage.md#add-a-core-filer) ovan visar ett exempel. Läs avsnittet [Molnautentiseringsuppgifter för klusterkonfigurationsguiden](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) för mer information. 

## <a name="configure-dns-for-load-balancing"></a>Konfigurera DNS för belastningsutjämning

I det här avsnittet beskrivs grunderna i att konfigurera ett RRDNS-system (Round-Robin DNS) för att distribuera klientbelastningen mellan alla klientinriktade IP-adresser i FXT Edge Filer-klustret. 

### <a name="decide-whether-or-not-to-use-dns"></a>Bestäm om DNS ska användas eller inte

Belastningsutjämning rekommenderas alltid, men du behöver inte alltid använda DNS. Med vissa typer av klientarbetsflöden kan det till exempel vara mer meningsfullt att använda ett skript för att tilldela kluster-IP-adresser jämnt mellan klienter när de monterar klustret. Vissa metoder beskrivs i [Montera klustret](fxt-mount-clients.md). 

Tänk på följande när du bestämmer dig för om du vill använda en DNS-server eller inte: 

* Om ditt system endast används av NFS-klienter krävs inte DNS. Det är möjligt att ange alla nätverksadresser med numeriska IP-adresser. 

* Om systemet stöder SMB-åtkomst krävs DNS, eftersom du måste ange en DNS-domän för Active Directory-servern.

* DNS krävs om du vill använda Kerberos-autentisering.

### <a name="round-robin-dns-configuration-details"></a>Information om DNS-konfiguration för avrundning

När klienter ansluter till klustret balanserar RRDNS automatiskt sina begäranden mellan alla tillgängliga gränssnitt.

Om du vill ha bästa prestanda konfigurerar du DNS-servern så att den hanterar klientinriktade klusteradresser enligt följande diagram.

En klustervserver visas till vänster och IP-adresser visas i mitten och till höger. Konfigurera varje klientåtkomstpunkt med A-poster och pekare enligt bilden.

![Kluster round-robin DNS-diagram - detaljerad](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
alternativ textlänk följer bild[detaljerad textbeskrivning](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Varje klientvänd IP-adress måste ha ett unikt namn för internt bruk av klustret. (I det här diagrammet heter klient-IP-adresser vs1-client-IP-* för tydlighetens skull, men i produktion bör du förmodligen använda något mer koncist, som klient*.)

Klienter monterar klustret med vservernamnet som serverargument. 

Ändra DNS-serverns ``named.conf`` fil för att ställa in cyklisk ordning för frågor till din vserver. Det här alternativet säkerställer att alla tillgängliga värden växlas igenom. Lägg till ett uttryck som följande:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Följande ``nsupdate`` kommandon ger ett exempel på att konfigurera DNS korrekt:

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

Ange den DNS-server som klustret använder på sidan Inställningar för > **klusteradministrationsnätverk.** **Cluster** Inställningarna på den sidan inkluderar:

* DNS-serveradress
* DNS-domännamn
* DNS-sökdomäner

Mer information finns i [DNS-inställningar](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) i guiden för klusterkonfiguration.

## <a name="next-steps"></a>Nästa steg

Det här är det sista grundläggande konfigurationssteget för Azure FXT Edge Filer-klustret. 

* Lär dig mer om systemets lysdioder och andra indikatorer i [Övervaka maskinvarustatus](fxt-monitor.md).
* Läs mer om hur klienter ska montera FXT Edge Filer-klustret i [Montera klustret](fxt-mount-clients.md). 
* Mer information om hur du använder och hanterar ett FXT Edge Filer-kluster finns i [guiden för klusterkonfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 