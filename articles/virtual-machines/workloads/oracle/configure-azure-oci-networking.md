---
title: Anslut Azure ExpressRoute med Oracles molninfrastruktur | Microsoft-dokument
description: Anslut Azure ExpressRoute med Oracle Cloud Infrastructure (OCI) FastConnect för att aktivera Oracle-programlösningar över moln
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: 70556cbbfefd6ad22ef96ee16065209031ea456c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683760"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Konfigurera en direkt sammanlänkning mellan Azure och Oracles molninfrastruktur  

För att skapa en [integrerad multimolnupplevelse](oracle-oci-overview.md)erbjuder Microsoft och Oracle direkt sammankoppling mellan Azure och Oracle Cloud Infrastructure (OCI) via [ExpressRoute](../../../expressroute/expressroute-introduction.md) och [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Genom sammankopplingen Av ExpressRoute och FastConnect kan kunderna uppleva låg latens, högt dataflöde, privat direktanslutning mellan de två molnen.

> [!IMPORTANT]
> Oracle kommer att certifiera dessa program för att köras i Azure när du använder Azure / Oracle Cloud sammanlänkningslösningen senast i maj 2020.
> * Svit E-Business
> * JD Edwards EnterpriseOne
> * Peoplesoft
> * Oracle Retail-program
> * Oracle Hyperion ekonomisk förvaltning

Följande bild visar en översikt på hög nivå av samtrafik:

![Nätverksanslutning över moln](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Krav

* För att upprätta anslutning mellan Azure och OCI måste du ha en aktiv Azure-prenumeration och en aktiv OCI-arrende.

* Anslutning är endast möjlig om en Azure ExpressRoute-peering-plats är i närheten av eller på samma peering-plats som OCI FastConnect. Se [Regiontillgänglighet](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurera direkt anslutning mellan ExpressRoute och FastConnect

1. Skapa en standard ExpressRoute-krets på din Azure-prenumeration under en resursgrupp. 
    * När du skapar ExpressRoute väljer du **Oracle Cloud FastConnect** som tjänsteleverantör. Om du vill skapa en ExpressRoute-krets läser du [steg-för-steg-guiden](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * En Azure ExpressRoute-krets ger detaljerade bandbreddsalternativ, medan FastConnect stöder 1, 2, 5 eller 10 Gbit/s. Därför rekommenderas att välja ett av dessa matchande bandbreddsalternativ under ExpressRoute.

    ![Skapa ExpressRoute-krets](media/configure-azure-oci-networking/exr-create-new.png)
1. Anteckna din **ExpressRoute-tjänstnyckel**. Du måste ange nyckeln när du konfigurerar FastConnect-kretsen.

    ![ExpressRoute-tjänstnyckel](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Du debiteras för ExpressRoute-avgifter så snart ExpressRoute-kretsen har etablerats (även om **providerstatus** **inte har etablerats).**

1. Skapa två privata IP-adressutrymmen på /30 som inte överlappar med ditt virtuella Azure-nätverk eller OCI-IP-adressutrymme för virtuella molnnätverk. Vi kommer att hänvisa till det första IP-adressutrymmet som primärt adressutrymme och det andra IP-adressutrymmet som sekundärt adressutrymme. Anteckna adresserna som du behöver när du konfigurerar FastConnect-kretsen.
1. Skapa en dynamisk routningsgateway (DRG). Du behöver detta när du skapar din FastConnect-krets. Mer information finns i dokumentationen [för dynamisk routningsgateway.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Skapa en FastConnect-krets under din Oracle-klient. Mer information finns i [Oracle-dokumentationen](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Under FastConnect-konfiguration väljer du **Microsoft Azure: ExpressRoute** som leverantör.
    * Välj den dynamiska routningsgateway som du har etablerat i föregående steg.
    * Välj den bandbredd som ska etableras. För optimal prestanda måste bandbredden matcha den bandbredd som valts när ExpressRoute-kretsen skapas.
    * Klistra in Servicenyckeln ExpressRoute i **Provider Service Key.**
    * Använd det första /30 privata IP-adressutrymmet som snidats ut i ett tidigare steg för den **primära BGP-IP-adressen** och det andra /30 privata IP-adressutrymmet för den **sekundära BGP-IP-adressen.**
        * Tilldela den första användbara adressen för de två intervallen för Oracle BGP IP-adress (primär och sekundär) och den andra adressen till kundens BGP IP-adress (från ett FastConnect-perspektiv). Den första användbara IP-adressen är den andra IP-adressen i adressutrymmet /30 (den första IP-adressen är reserverad av Microsoft).
    * Klicka på **Skapa**.
1. Slutför länkningen av FastConnect till det virtuella molnet under oracle-klienten via Dynamic Routing Gateway med hjälp av rutttabell.
1. Navigera till Azure och se till att **providerstatus** för din ExpressRoute-krets har **ändrats** till Etablerad och att en peering av typen **Azure private** har etablerats. Detta är en förutsättning för följande steg.

    ![ExpressRoute-providerstatus](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klicka på **Azure private** peering. Peering-informationen visas automatiskt baserat på den information du angav när du konfigurerade FastConnect-kretsen.

    ![Privata peering-inställningar](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Ansluta virtuellt nätverk till ExpressRoute

1. Skapa ett virtuellt nätverk och en virtuell nätverksgateway om du inte redan har gjort det. Mer information finns i [steg-för-steg-guiden](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Konfigurera anslutningen mellan den virtuella nätverksgatewayen och ExpressRoute-kretsen genom att köra [Terraform-skriptet](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) eller genom att köra PowerShell-kommandot för att [konfigurera ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

När du har slutfört nätverkskonfigurationen kan du verifiera konfigurationens giltighet genom att klicka på **Hämta ARP-poster** och **Hämta vägtabell** under ExpressRoute Private peering-bladet i Azure-portalen.

## <a name="automation"></a>Automation

Microsoft har skapat Terraform-skript för att möjliggöra automatisk distribution av nätverksanslutningen. Terraform-skripten måste autentisera med Azure före körningen, eftersom de kräver tillräcklig behörighet för Azure-prenumerationen. Autentisering kan utföras med [ett huvudnamn för Azure Active Directory-tjänsten](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) eller med hjälp av Azure CLI. Mer information finns i [Terraform-dokumentationen](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Terraform-skripten och tillhörande dokumentation för att distribuera inter-connect finns i den här [GitHub-databasen](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Övervakning

Installera agenter på båda molnen kan du utnyttja Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) för att övervaka prestanda för det end-to-end-nätverket. NPM hjälper dig att enkelt identifiera nätverksproblem och hjälper till att eliminera dem.

## <a name="delete-the-interconnect-link"></a>Ta bort länken för sammanlänkning

För att ta bort sammankopplingen måste följande steg följas i den angivna ordningen. Underlåtenhet att göra detta kommer att resultera i en "misslyckat tillstånd" ExpressRoute krets.

1. Ta bort ExpressRoute-anslutningen. Ta bort anslutningen genom att klicka på ikonen **Ta bort** på sidan för anslutningen. Mer information finns i [ExpressRoute-dokumentationen](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Ta bort Oracle FastConnect från Oracles molnkonsol.
1. När Oracle FastConnect-kretsen har tagits bort kan du ta bort Azure ExpressRoute-kretsen.

Nu är borttagnings- och avetableringsprocessen klar.

## <a name="next-steps"></a>Nästa steg

* Mer information om anslutning mellan moln mellan OCI och Azure finns i [Oracle-dokumentationen](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Använd [Terraform-skript](https://aka.ms/azureociinterconnecttf) för att distribuera infrastruktur för riktade Oracle-program via Azure och konfigurera nätverksanslutningen. 
