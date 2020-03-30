---
title: Azure VMware Solution by CloudSimple – Använd Private Cloud som katastrofplats för lokala arbetsbelastningar
description: Beskriver hur du konfigurerar ditt CloudSimple Private Cloud som en plats för haveriberedskap för lokala VMware-arbetsbelastningar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083144"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Konfigurera CloudSimple Private Cloud som en plats för haveriberedskap för lokala VMware-arbetsbelastningar

Ditt CloudSimple Private Cloud kan konfigureras som en återställningsplats för lokala program för att ge affärskontinuitet i händelse av en katastrof. Återställningslösningen baseras på Zerto Virtual Replication som replikerings- och orkestreringsplattform. Kritisk infrastruktur och virtuella programdatorer kan replikeras kontinuerligt från ditt lokala vCenter till ditt privata moln. Du kan använda ditt privata moln för redundanstestning och för att säkerställa tillgängligheten för ditt program under en katastrof. En liknande metod kan följas för att ställa in det privata molnet som en primär plats som skyddas av en återställningsplats på en annan plats.

> [!NOTE]
> Se Zerto-dokumentet [Storleksöverväganden för Zerto Virtual Replication](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) för riktlinjer för hur du dimensionerar din katastrofåterställningsmiljö.

CloudSimple-lösningen:

* Eliminerar behovet av att konfigurera ett datacenter specifikt för haveriberedskap (DR).
* Gör att du kan utnyttja Azure-platser där CloudSimple distribueras för global geografisk återhämtning.
* Ger dig möjlighet att minska distributionskostnaderna och den totala ägandekostnaden för DR.

Lösningen kräver att du:

* Installera, konfigurera och hantera Zerto i ditt privata moln.
* Ange dina egna licenser för Zerto när det privata molnet är den skyddade platsen. Du kan para ihop Zerto som körs på CloudSimple-webbplatsen med din lokala webbplats för licensiering.

Följande bild visar arkitekturen för Zerto-lösningen.

![Arkitektur](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Så här distribuerar du lösningen

I följande avsnitt beskrivs hur du distribuerar en DR-lösning med Zerto Virtual Replication i ditt privata moln.

1. [Krav](#prerequisites)
2. [Valfri konfiguration på CloudSimple Private Cloud](#optional-configuration-on-your-private-cloud)
3. [Konfigurera ZVM och VRA på CloudSimple Private Cloud](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Konfigurera Zerto-gruppen för virtuellt skydd](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Krav

Om du vill aktivera Zerto Virtual Replication från din lokala miljö till ditt privata moln slutför du följande förutsättningar.

1. [Konfigurera en VPN-anslutning från plats till plats mellan det lokala nätverket och ditt CloudSimple Private Cloud](set-up-vpn.md).
2. [Konfigurera DNS-sökning så att dina privata molnhanteringskomponenter vidarebefordras till Privata Cloud DNS-servrar](on-premises-dns-setup.md).  Om du vill aktivera vidarebefordran av DNS-sökning skapar du en `*.cloudsimple.io` vidarebefordranszonpost i den lokala DNS-servern för cloudsimple DNS-servrar.
3. Konfigurera DNS-sökning så att lokala vCenter-komponenter vidarebefordras till lokala DNS-servrar.  DNS-servrarna måste kunna nås från ditt CloudSimple Private Cloud via Site-to-Site VPN. Om du behöver hjälp skickar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)med följande information.  

    * Lokalt DNS-domännamn
    * Lokala DNS-server-IP-adresser

4. Installera en Windows-server i ditt privata moln. Servern används för att installera Zerto Virtual Manager.
5. [Eskalera dina CloudSimple-privilegier](escalate-private-cloud-privileges.md).
6. Skapa en ny användare på ditt privata moln vCenter med den administrativa rollen att använda som tjänstkonto för Zerto Virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Valfri konfiguration på ditt privata moln

1. Skapa en eller flera resurspooler på ditt privata moln vCenter som du kan använda som målresurspooler för virtuella datorer från din lokala miljö.
2. Skapa en eller flera mappar på ditt privata moln vCenter som ska användas som målmappar för virtuella datorer från din lokala miljö.
3. Skapa VLAN för redundansnätverk och konfigurera brandväggsregler. Öppna en [supportbegäran om](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) hjälp.
4. Skapa distribuerade portgrupper för redundansnätverk och testnätverk för testning av redundans för virtuella datorer.
5. Installera [DHCP- och DNS-servrar](dns-dhcp-setup.md) eller använd en Active Directory-domänkontrollant i din private cloud-miljö.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Konfigurera ZVM och VRA i ditt privata moln

1. Installera Zerto Virtual Manager (ZVM) på Windows-servern i ditt privata moln.
2. Logga in på ZVM med hjälp av det tjänstkonto som skapats i tidigare steg.
3. Konfigurera licensiering för Zerto Virtual Manager.
4. Installera Zerto Virtual Replication Appliance (VRA) på ESXi-värdarna för ditt privata moln.
5. Para ihop din privata Cloud ZVM med din lokala ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Konfigurera Zerto-gruppen för virtuellt skydd

1. Skapa en ny virtuell skyddsgrupp (VPG) och ange prioriteten för VPG.
2. Välj de virtuella datorer som kräver skydd för affärskontinuitet och anpassa startordningen om det behövs.
3. Välj återställningsplatsen som ditt privata moln och standardåterställningsservern som private cloud-klustret eller resursgruppen som du skapade. Välj **vsanDatastore** för återställningsdatalagringen i ditt privata moln.

    ![VPG (VPG)](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Du kan anpassa värdalternativet för enskilda virtuella datorer under alternativet Vm-inställningar.

4. Anpassa lagringsalternativen efter behov.
5. Ange vilka återställningsnätverk som ska användas för redundansnätverk och redundanstestnätverk som de distribuerade portgrupper som skapats tidigare och anpassa återställningsskripten efter behov.
6. Anpassa nätverksinställningarna för enskilda virtuella datorer om det behövs och skapa VPG.
7. Testa redundans när replikeringen är klar.

## <a name="reference"></a>Referens

[Zerto Dokumentation](https://www.zerto.com/myzerto/technical-documentation/)
