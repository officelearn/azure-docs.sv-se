---
title: Ansluta med ExpressRoute i Azure med Oracle-Molninfrastruktur | Microsoft Docs
description: Anslut Azure ExpressRoute med molnet infrastruktur OCI (Oracle) FastConnect att aktivera molnöverskridande Oracle-programlösningar
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707586"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Konfigurera en direkt gränssnittet mellan Azure och Oracle-Molninfrastruktur  

Skapa en [integrerad upplevelse i flera moln](oracle-oci-overview.md) (förhandsversion), Microsoft och Oracle erbjuder direkt gränssnittet mellan Azure och molnet infrastruktur OCI (Oracle) via [ExpressRoute](../../../expressroute/expressroute-introduction.md) och [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Via ExpressRoute och FastConnect gränssnittet får kunder med låg latens, högt dataflöde, privata direkt anslutning mellan de två moln.

> [!IMPORTANT]
> Anslutningen mellan Microsoft Azure och OCI är i Förhandsgranska. Om du vill aktivera svarstider anslutningar mellan Azure och OCI måste Azure-prenumeration och OCI innehavare först vara godkänd för den här funktionen.

Följande bild visar en översikt över gränssnittet:

![Nätverksanslutningen mellan moln](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Förutsättningar

* För att upprätta anslutningar mellan Azure och OCI, måste du ha en aktiv Azure-prenumeration och en aktiv OCI innehavare.

* Anslutningen är endast möjligt där en peering Azure ExpressRoute-plats är i närhet eller i samma peering plats som OCI FastConnect. Se [begränsningar i förhandsversionen](oracle-oci-overview.md#preview-limitations).

* Din Azure-prenumeration måste vara godkänd för den här funktionen i förhandsversionen. Kontakta din Microsoft-representant om du vill aktivera den här funktionen på din prenumeration.

* OCI-innehavare måste vara godkänd för den här funktionen i förhandsversionen. Kontakta din Oracle-representant för mer information.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurera direkt anslutning mellan ExpressRoute och FastConnect

1. Skapa en ExpressRoute-krets som standard på din Azure-prenumeration under en resursgrupp. 
    * När du skapar ExpressRoute, Välj **Oracle Cloud FastConnect** som tjänstleverantören. För att skapa en ExpressRoute-krets, se den [stegvis guide](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * En Azure ExpressRoute-krets ger detaljerade bandbreddsalternativ, medan FastConnect stöder 1, 2, 5 eller 10 Gbit/s. Vi rekommenderar därför att välja ett av alternativen med matchande bandbredd under ExpressRoute.

    ![Skapa ExpressRoute-krets](media/configure-azure-oci-networking/exr-create-new.png)
1. Anteckna din ExpressRoute **tjänstnyckeln**. Du måste ange nyckeln när du konfigurerar din FastConnect-krets.

    ![ExpressRoute Service nyckel](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Du kommer att debiteras för ExpressRoute-avgifter när ExpressRoute-kretsen har etablerats (även om den **Providerstatus** är **inte etablerats**).

1. Få två privata IP-adressutrymmena/30 varje som inte överlappar med ditt Azure-nätverk eller molnnätverk för OCI virtuella IP-adressutrymme. Vi ska referera till det första IP-adressutrymmet som primär adressutrymme och andra IP-adressutrymmet som sekundär adressutrymme. Anteckna de adresser som du behöver när du konfigurerar din FastConnect-krets.
1. Skapa en dynamisk Routningsgateway (DRG). Du behöver detta när du skapar din FastConnect-krets. Mer information finns i den [Dynamic Routing Gateway](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) dokumentation.
1. Skapa en FastConnect krets under Oracle-klient. Mer information finns i den [Oracle-dokumentationen](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Välj under FastConnect konfiguration, **Microsoft Azure: ExpressRoute** som provider.
    * Välj Dynamic Routing Gateway som du etablerade i föregående steg.
    * Välj bandbredden som ska etableras. Bandbredden måste matcha den bandbredd som är markerade när du skapade ExpressRoute-kretsen för optimala prestanda.
    * I **Provider Tjänstnyckeln**, klistra in nyckeln för ExpressRoute-tjänsten.
    * Använd det första/30 privat IP-adressutrymme som vis i föregående steg för den **primära BGP IP-adressen** och det andra/30 privata IP-adressutrymmet för det **sekundära BGP IP** adress.
        * Tilldela den första riktlinje adressen för de två områdena för Oracle BGP IP-adress (primär och sekundär) och den andra adressen för kunden BGP IP-adress (från en FastConnect perspektiv). Den första riktlinje IP-adressen är den andra IP-adressen i/30-adressutrymmen (den första IP-adressen är reserverad av Microsoft).
    * Klicka på **Skapa**.
1. Slutför länka FastConnect till virtuellt molnnätverk under din Oracle-klient via Dynamic Routing Gateway, med hjälp av routningstabellen.
1. Gå till Azure och se till att den **Providerstatus** för din ExpressRoute-krets har ändrats till **etablerad** och som en peering av typen **privat Azure** har etableras. Det här är ett krav för följande steg.

    ![Status för ExpressRoute-provider](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klicka på den **privat Azure** peering. Peering-information automatiskt konfigurerade utifrån den information som du angav när du ställer in din FastConnect krets visas.

    ![Privata peering-inställningar](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Ansluta virtuella nätverk till ExpressRoute

1. Skapa ett virtuellt nätverk och virtuella nätverksgateway, om du inte redan har gjort. Mer information finns i [stegvis guide](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Konfigurera anslutningen mellan den virtuella nätverksgatewayen och ExpressRoute-kretsen genom att köra den [Terraform skriptet](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) eller genom att köra PowerShell-kommando till [konfigurera ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

När du har slutfört nätverkskonfigurationen, du kan kontrollera giltigheten för din konfiguration genom att klicka på **hämta ARP-poster** och **Get routningstabellen** under bladet ExpressRoute privat peering i Azure-portalen.

## <a name="automation"></a>Automation

Microsoft har skapat Terraform skript om du vill aktivera automatisk distribution av network sammankoppling. Terraform-skript måste autentisera med Azure innan körningen, eftersom de kräver behörighet på Azure-prenumeration. Autentisering kan utföras med hjälp av en [Azure Active Directory-tjänstobjekt](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) eller med hjälp av Azure CLI. Mer information finns i den [dokumentation om Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Terraform skript och relaterad dokumentation för att distribuera inter-connect finns i den här [GitHub-lagringsplatsen](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Övervakning

Installera agenter på båda molnen, kan du utnyttja Azure [nätverk prestanda Övervakare (NPM)](../../../expressroute/how-to-npm.md) att övervaka prestanda för slutpunkt till slutpunkt-nätverk. NPM hjälper dig att lätt identifiera nätverksproblem och hjälper till att eliminera dem.

## <a name="delete-the-interconnect-link"></a>Ta bort länken sammankoppling

Ta bort sammankopplingen genom måste följande steg följas, i den specifika ordning. Det gick inte att göra det resulterar i en ”felaktigt tillstånd” ExpressRoute-krets.

1. Ta bort ExpressRoute-anslutning. Ta bort anslutningen genom att klicka på den **ta bort** ikonen på sidan för din anslutning. Mer information finns i den [dokumentation om ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Ta bort Oracle FastConnect från Oracle Cloud-konsolen.
1. När Oracle FastConnect kretsen har tagits bort, kan du ta bort Azure ExpressRoute-krets.

Nu har kommer att tas bort och avetablering processen slutförts.

## <a name="next-steps"></a>Nästa steg

* Mer information om molnöverskridande anslutningen mellan OCI och Azure finns i den [Oracle-dokumentationen](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Använd [Terraform skript](https://aka.ms/azureociinterconnecttf) att distribuera infrastrukturen för berörda Oracle-program via Azure och konfigurera nätverket sammankoppling. 