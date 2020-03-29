---
title: Avere vFXT DNS - Azure
description: Konfigurera en DNS-server för lastutjämningsutjämning av round-robin med Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153793"
---
# <a name="avere-cluster-dns-configuration"></a>Konfiguration av DNS för Avere-kluster

I det här avsnittet beskrivs grunderna i att konfigurera ett DNS-system för belastningsutjämning av Avere vFXT-klustret.

Det här dokumentet *innehåller inga* instruktioner för hur du konfigurerar och hanterar en DNS-server i Azure-miljön.

I stället för att använda round-robin DNS för att ladda-balansera ett vFXT-kluster i Azure, överväg att använda manuella metoder för att tilldela IP-adresser jämnt mellan klienter när de är monterade. Flera metoder beskrivs i [Mount Avere-klustret](avere-vfxt-mount-clients.md).

Tänk på följande när du bestämmer dig för om du vill använda en DNS-server eller inte:

* Om ditt system endast används av NFS-klienter krävs det inte att använda DNS - det är möjligt att ange alla nätverksadresser med hjälp av numeriska IP-adresser.

* Om systemet stöder SMB-åtkomst krävs DNS, eftersom du måste ange en DNS-domän för Active Directory-servern.

* DNS krävs om du vill använda Kerberos-autentisering.

## <a name="load-balancing"></a>Belastningsutjämning

Om du vill distribuera den totala belastningen konfigurerar du DNS-domänen så att den använder lastdistribution av avrundning för klientinriktade IP-adresser.

## <a name="configuration-details"></a>Konfigurationsinformation

När klienter ansluter till klustret balanserar RRDNS automatiskt sina begäranden mellan alla tillgängliga gränssnitt.

Om du vill ha bästa prestanda konfigurerar du DNS-servern så att den hanterar klientinriktade klusteradresser enligt följande diagram.

En klustervserver visas till vänster och IP-adresser visas i mitten och till höger. Konfigurera varje klientåtkomstpunkt med A-poster och pekare enligt bilden.

![Avere kluster round-robin DNS-diagram](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

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

## <a name="cluster-dns-settings"></a>Dns-inställningar för kluster

Ange den DNS-server som vFXT-klustret använder på sidan Inställningar för > **klusteradministrationsnätverk.** **Cluster** Inställningarna på den sidan inkluderar:

* DNS-serveradress
* DNS-domännamn
* DNS-sökdomäner

Läs [DNS-inställningar](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) i Avere Cluster Configuration Guide för mer information om hur du använder den här sidan.
