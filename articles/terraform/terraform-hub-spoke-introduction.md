---
title: Självstudiekurs - Skapa en hubb- och ekerhybridnätverkstopologi i Azure med Terraform
description: Självstudiekurs som illustrerar hur du skapar en hel hybridnätverksreferensarkitektur i Azure med Terraform
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472187"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Självstudiekurs: Skapa en hubb- och ekerhybridnätverkstopologi i Azure med Terraform

Den här självstudieserien visar hur du använder Terraform för att implementera en [hubb och ekernätverkstopologi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)i Azure . 

Ett nav och en talade topologi är ett sätt att isolera arbetsbelastningar samtidigt som gemensamma tjänster delas. Dessa tjänster omfattar identitet och säkerhet. Navet är ett virtuellt nätverk (VNet) som fungerar som en central anslutningspunkt till ett lokalt nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben. Delade tjänster distribueras i navet, medan enskilda arbetsbelastningar distribueras i ekernätverk.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att lägga ut hubben och ekrar referensarkitekturresurser för hybridnätverk
> * Använda Terraform för att skapa resurser för navnätverksinstallationer
> * Använd Terraform för att skapa navnätverk i Azure för att fungera som en gemensam punkt för alla resurser
> * Använd Terraform för att skapa enskilda arbetsbelastningar som eker-virtuella nätverk i Azure
> * Använd Terraform för att upprätta gateways och anslutningar mellan lokala nätverk och Azure-nätverk
> * Använd Terraform för att skapa VNet-peerings till ekernätverk

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration:** Om du inte redan har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Installera och konfigurera Terraform:** Om du vill etablera virtuella datorer och annan infrastruktur i Azure [installerar och konfigurerar du Terraform](terraform-install-configure.md)

## <a name="hub-and-spoke-topology-architecture"></a>Hub- och talade topologiarkitektur

I navet och talade topologi, är navet ett VNet. Det virtuella nätverket fungerar som en central anslutningspunkt till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben och som kan användas till att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och hubben via en ExpressRoute- eller VPN-gatewayanslutning. Följande bild visar komponenterna i ett nav och talade topologi:

![Hubb- och talade topologiarkitektur i Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Fördelar med navet och talade topologi

En hubb och ekrad nätverkstopologi är ett sätt att isolera arbetsbelastningar samtidigt som gemensamma tjänster delas. Dessa tjänster omfattar identitet och säkerhet. Navet är ett virtuella nätverk som fungerar som en central anslutningspunkt till ett lokalt nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben. Delade tjänster distribueras i navet, medan enskilda arbetsbelastningar distribueras i ekernätverk. Här är några fördelar med navet och talade nätverk topologi:

- **Kostnadsbesparingar** genom att centralisera tjänster på en enda plats som kan delas av flera arbetsbelastningar. Dessa arbetsbelastningar omfattar virtuella nätverksinstallationer och DNS-servrar.
- **Hantera prenumerationsgränser** genom att peer-koppla virtuella nätverk från olika prenumerationer till den centrala hubben.
- **Uppdelning** mellan central IT (SecOps, InfraOps) och arbetsbelastningar (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typiska användningsområden för hubb- och ekerarkitekturen

Några av de typiska användningsområdena för ett nav och ekerarkitektur är:

- Många kunder har arbetsbelastningar som distribueras i olika miljöer. Dessa miljöer omfattar utveckling, testning och produktion. Många gånger måste dessa arbetsbelastningar dela tjänster som DNS, IDS, NTP eller AD DS. Dessa delade tjänster kan placeras i hubb-nätverket. På så sätt distribueras varje miljö till en eker för att upprätthålla isoleringen.
- Arbetsbelastningar som inte kräver anslutning till varandra, men som kräver åtkomst till delade tjänster.
- Företag som behöver central kontroll över säkerhetsaspekter.
- Företag som kräver segregerad hantering för arbetsbelastningarna i varje eker.

## <a name="preview-the-demo-components"></a>Förhandsgranska demokomponenterna

När du arbetar igenom varje självstudiekurs i den här serien definieras olika komponenter i olika Terraform-skript. Demoarkitekturen som skapas och distribueras består av följande komponenter:

- **Lokalt nätverk**. Ett privat lokalt nätverk som körs med en organisation. För hubb- och ekrarreferensarkitektur används ett virtuella nätverk i Azure för att simulera ett lokalt nätverk.

- **VPN-enhet**. En VPN-enhet eller -tjänst tillhandahåller extern anslutning till det lokala nätverket. VPN-enheten kan vara en maskinvaruapparat eller en programvarulösning. 

- **Virtuellt hubbnätverk**. Navet är den centrala anslutningspunkten till det lokala nätverket och en plats där tjänsterna är värd. Dessa tjänster kan förbrukas av de olika arbetsbelastningar som finns i eker-virtuella nätverk.

- **Gateway-undernät**. VNet-gateways hålls i samma undernät.

- **Virtuella ekernätverk**. Ekrar kan användas för att isolera arbetsbelastningar i egna virtuella nätverk, som hanteras separat från andra ekrar. Varje arbetsbelastning kan innehålla flera nivåer, med flera undernät som är anslutna via Azure-lastbalanserare. 

- **VNet-peering**. Två virtuella nätverk kan anslutas med hjälp av en peering-anslutning. Peering-anslutningar är icke-transitiva anslutningar med låg latens mellan virtuella nätverk. När vnäten har peerats utbyter de trafik med hjälp av Azure-stamnätet utan att behöva en router. I en hubb och ekernätverkstopologi används VNet-peering för att ansluta navet till varje eker. Du kan peer-virtuella nätverk i samma region eller olika regioner.

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

Skapa katalogen som innehåller dina Terraform-konfigurationsfiler för demon.

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog som heter `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Deklarera Azure-providern

Skapa Terraform-konfigurationsfilen som deklarerar Azure-providern.

1. Öppna en ny fil med `main.tf`namnet .

    ```bash
    code main.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Spara filen och avsluta redigeringsprogrammet.

## <a name="create-the-variables-file"></a>Skapa variabler filen

Skapa konfigurationsfilen för Terraform för vanliga variabler som används i olika skript.

1. Öppna en ny fil med `variables.tf`namnet .

    ```bash
    code variables.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Spara filen och avsluta redigeringsprogrammet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa lokalt virtuellt nätverk med Terraform i Azure](./terraform-hub-spoke-on-prem.md)