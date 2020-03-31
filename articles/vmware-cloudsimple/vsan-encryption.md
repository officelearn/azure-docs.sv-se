---
title: Azure VMware-lösning från CloudSimple – Konfigurera vSAN-kryptering för privat moln
description: Beskriver hur du konfigurerar vSAN-programkrypteringsfunktionen så att ditt CloudSimple Private Cloud-moln kan fungera med en nyckelhanteringsserver som körs i ditt virtuella Azure-nätverk.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020649"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Konfigurera vSAN-kryptering för CloudSimple Private Cloud

Du kan konfigurera vSAN-programkrypteringsfunktionen så att ditt CloudSimple Private Cloud kan arbeta med en nyckelhanteringsserver som körs i ditt virtuella Azure-nätverk.

VMware kräver användning av ett externt KMIP 1.1-kompatibelt KMS-verktyg (Third-Part Key Management Server) när du använder vSAN-kryptering. Du kan utnyttja alla KMS som stöds och som är certifierade av VMware och som är tillgängliga för Azure.

Den här guiden beskriver hur du använder HyTrust KeyControl KMS som körs i ett virtuellt Azure-nätverk. En liknande metod kan användas för alla andra certifierade KMS-lösningar från tredje part för vSAN.

Denna KMS-lösning kräver att du:

* Installera, konfigurera och hantera ett VMware-certifierat KMS-verktyg från tredje part i ditt virtuella Azure-nätverk.
* Ange dina egna licenser för KMS-verktyget.
* Konfigurera och hantera vSAN-kryptering i ditt privata moln med hjälp av kms-verktyget från tredje part som körs i ditt virtuella Azure-nätverk.

## <a name="kms-deployment-scenario"></a>KMS-distributionsscenario

KMS-serverklustret körs i ditt virtuella Azure-nätverk och kan nås ip från Private Cloud vCenter via den konfigurerade Azure ExpressRoute-anslutningen.

![.. /media/KMS-kluster i virtuella Azure-nätverk](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Så här distribuerar du lösningen

Distributionsprocessen har följande steg:

1. [Kontrollera att förutsättningarna är uppfyllda](#verify-prerequisites-are-met)
2. [CloudSimple-portal: Hämta ExpressRoute Peering-information](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure-portal: Anslut ditt virtuella nätverk till det privata molnet](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure-portal: Distribuera ett HyTrust KeyControl-kluster i ditt virtuella nätverk](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Konfigurera KMIP-server](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI: Konfigurera vSAN-kryptering för att använda KMS-kluster i ditt virtuella Azure-nätverk](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Kontrollera att förutsättningarna är uppfyllda

Kontrollera följande före distributionen:

* Den valda KMS-leverantören, verktyget och versionen finns i vSAN-kompatibilitetslistan.
* Den valda leverantören stöder en version av verktyget för att köras i Azure.
* Azure-versionen av KMS-verktyget är KMIP 1.1-kompatibel.
* En Azure Resource Manager och ett virtuellt nätverk har redan skapats.
* Ett CloudSimple Private Cloud har redan skapats.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple-portal: Skaffa ExpressRoute-peering-information

För att fortsätta installationen behöver du auktoriseringsnyckeln och peer circuit URI för ExpressRoute plus åtkomst till din Azure-prenumeration. Den här informationen är tillgänglig på sidan Anslutning till virtuellt nätverk i CloudSimple-portalen. Instruktioner finns i [Konfigurera en virtuell nätverksanslutning till det privata molnet](virtual-network-connection.md). Om du har några problem med att få informationen öppnar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure-portal: Anslut ditt virtuella nätverk till ditt privata moln

1. Skapa en virtuell nätverksgateway för ditt virtuella nätverk genom att följa instruktionerna i [Konfigurera en virtuell nätverksgateway för ExpressRoute med Azure-portalen](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Länka ditt virtuella nätverk till CloudSimple ExpressRoute-kretsen genom att följa instruktionerna i [Anslut ett virtuellt nätverk till en ExpressRoute-krets med hjälp av portalen](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Använd cloudsimple ExpressRoute-kretsinformationen som tas emot i ditt välkomstmeddelande från CloudSimple för att länka ditt virtuella nätverk till CloudSimple ExpressRoute-kretsen i Azure.
4. Ange auktoriseringsnyckeln och peer circuit-URI: n, ge anslutningen ett namn och klicka på **OK**.

![Ange CS peer circuit URI när du skapar det virtuella nätverket](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure-portal: Distribuera ett HyTrust KeyControl-kluster i Azure Resource Manager i ditt virtuella nätverk

Om du vill distribuera ett HyTrust KeyControl-kluster i Azure Resource Manager i det virtuella nätverket utför du följande uppgifter. Mer information finns i [HyTrust-dokumentationen.](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. Skapa en Azure-nätverkssäkerhetsgrupp (nsg-hytrust) med angivna inkommande regler genom att följa instruktionerna i HyTrust-dokumentationen.
2. Generera ett SSH-nyckelpar i Azure.
3. Distribuera den första KeyControl-noden från avbildningen på Azure Marketplace.  Använd den offentliga nyckeln för nyckelparet som genererades och välj **nsg-hytrust** som nätverkssäkerhetsgrupp för KeyControl-noden.
4. Konvertera keycontrols privata IP-adress till en statisk IP-adress.
5. SSH till KeyControl VM med hjälp av sin offentliga IP-adress och den privata nyckeln för det tidigare nämnda nyckelparet.
6. När du uppmanas att göra `No` SSH-skalet väljer du att ange noden som den första KeyControl-noden.
7. Lägg till ytterligare KeyControl-noder genom att upprepa steg 3-5 i den här proceduren och välja `Yes` när du uppmanas att lägga till i ett befintligt kluster.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Konfigurera KMIP-servern

Gå till https://*public-ip*, där public-ip är den offentliga *IP-adressen* för KeyControl-noden VM. Följ dessa steg från [HyTrust-dokumentationen](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Konfigurera en KMIP-server](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Skapa ett certifikatpaket för VMware-kryptering](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI: Konfigurera vSAN-kryptering för att använda KMS-kluster i ditt virtuella Azure-nätverk

Följ HyTrust-instruktionerna för att [skapa ett KMS-kluster i vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Lägga till KMS-klusterinformation i vCenter](media/vsan-config01.png)

Gå till Kluster **> Konfigurera** och välj **Allmänt** alternativ för vSAN i vCenter. Aktivera kryptering och välj DET KMS-kluster som tidigare har lagts till i vCenter.

![Aktivera vSAN-kryptering och konfigurera KMS-kluster i vCenter](media/vsan-config02.png)

## <a name="references"></a>Referenser

### <a name="azure"></a>Azure

[Konfigurera en virtuell nätverksgateway för ExpressRoute med Azure-portalen](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Länka ett virtuellt nätverk till en ExpressRoute-krets med Azure-portalen](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust (hytrust)

[HyTrust DataControl och Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Konfigurera en KMPI-server](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Skapa ett certifikatpaket för VMware-kryptering](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Skapa KMS-klustret i vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
