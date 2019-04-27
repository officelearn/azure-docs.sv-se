---
title: Skapa ett nav och ekrar hybrid nätverkets topologi med Terraform i Azure
description: Självstudie som illustrerar hur du skapar en hela hybridnätverksarkitektur för referens i Azure med Terraform
services: terraform
ms.service: azure
keywords: terraform, hub and spoke, networks, hybrid networks, devops, virtual machine, azure,  vnet peering, network virtual appliance
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 648369d89bd2b5b08171e1f6f5482c81bfba3c66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884733"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Självstudier: Skapa ett nav och ekrar hybrid nätverkets topologi med Terraform i Azure

Den här självstudien visar hur du använder Terraform implementerar i Azure en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

En topologi med nav och ekrar är ett sätt att isolera arbetsbelastningar samtidigt som vanliga tjänster. Tjänsterna omfattar identitet och säkerhet. Hubben är ett virtuellt nätverk (VNet) som fungerar som en central anslutningspunkt till ett lokalt nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben. Delade tjänster distribueras i hubben, medan individuella arbetsbelastningar distribueras i eker-nätverk.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använda HCL (HashiCorp Language) för att utforma NAV och ekrar hybrid referens arkitektur nätverksresurser
> * Använd Terraform till att skapa navnätverket installation resurser
> * Använd Terraform till att skapa navnätverket i Azure så att den fungerar som gemensam tidpunkt för alla resurser
> * Använd Terraform till att skapa individuella arbetsbelastningar som eker virtuella nätverk i Azure
> * Använd Terraform till att upprätta gateways och anslutningar mellan lokalt och Azure-nätverk
> * Använd Terraform till att skapa VNet-peering till ekernätverk

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**: Om du inte redan har en Azure-prenumeration kan du skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Installera och konfigurera Terraform**: Att etablera virtuella datorer och annan infrastruktur i Azure, [installera och konfigurera Terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>NAV och ekrar topologi-arkitektur

Hubben är ett virtuellt nätverk i topologin NAV och ekrar. Det virtuella nätverket fungerar som en central plats för anslutning till det lokala nätverket. Ekrarna är virtuella nätverk som peer-kopplas med hubben och som kan användas till att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och hubben via en ExpressRoute- eller VPN-gatewayanslutning. Följande bild visar komponenterna i en nav och ekrar topologi:

![NAV och ekrar topologi arkitektur i Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Fördelarna med nav och eker-topologi

En nätverkstopologi med nav och ekrar är ett sätt att isolera arbetsbelastningar samtidigt som vanliga tjänster. Tjänsterna omfattar identitet och säkerhet. Hubben är ett virtuellt nätverk som fungerar som en central anslutningspunkt till ett lokalt nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben. Delade tjänster distribueras i hubben, medan individuella arbetsbelastningar distribueras i eker-nätverk. Här följer några av fördelarna med nav och ekrar nätverkets topologi:

- **Besparingar** genom att centralisera tjänster på en enda plats som kan delas av flera arbetsbelastningar. De här arbetsbelastningarna inkluderar virtuella nätverksinstallationer och DNS-servrar.
- **Hantera prenumerationsgränser** genom att peer-koppla virtuella nätverk från olika prenumerationer till den centrala hubben.
- **Uppdelning** mellan central IT (SecOps, InfraOps) och arbetsbelastningar (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Vanliga användningsområden för arkitekturen NAV och ekrar

Några vanliga användningsområden för en arkitektur med nav och ekrar är:

- Många kunder har arbetsbelastningar som distribueras i olika miljöer. Dessa miljöer innehåller utveckling, testning och produktion. Många gånger behöver dessa arbetsbelastningar dela tjänster, till exempel DNS, ID: N, NTP eller AD DS. De här delade tjänsterna kan placeras i det virtuella hubbnätverket. På så sätt kan distribueras varje miljö till en eker att bibehålla isolering.
- Arbetsbelastningar som inte kräver anslutning till varandra, men kräver åtkomst till delade tjänster.
- Företag som kräver central kontroll över säkerhetsaspekter.
- Företag som kräver fördelad hantering för arbetsbelastningarna i varje eker.

## <a name="preview-the-demo-components"></a>Förhandsgranska demo-komponenter

Medan du arbetar med varje guide i den här serien definieras olika komponenter i olika Terraform-skript. Demo-arkitekturen skapas och distribueras består av följande komponenter:

- **Lokalt nätverk**. Ett privat lokalt nätverk som körs med en organisation. För NAV och ekrar Referensarkitektur är ett virtuellt nätverk i Azure används för att simulera ett lokalt nätverk.

- **VPN-enhet**. En VPN-enhet eller tjänst tillhandahåller extern anslutning till det lokala nätverket. VPN-enheten kan vara en installation för maskinvara eller en programvarulösning. 

- **Virtuellt hubbnätverk**. Hubben är den centrala punkten för anslutning till ditt lokala nätverk och en plats för tjänster. Dessa tjänster kan användas av olika arbetsbelastningar som finns i de virtuella ekernätverken.

- **Gateway-undernät**. VNet-gateways lagras i samma undernät.

- **Virtuella ekernätverk**. Ekrar kan användas för att isolera arbetsbelastningar i egna virtuella nätverk, som hanteras separat från andra ekrar. Varje arbetsbelastning kan innehålla flera nivåer, med flera undernät som är anslutna via Azure-lastbalanserare. 

- **VNET-peering**. Två virtuella nätverk kan anslutas med hjälp av en peering-anslutning. Peering-anslutningar är icke-transitiva anslutningar med låg latens mellan virtuella nätverk. När peer-kopplats utbyter de virtuella nätverken trafik med hjälp av Azure-stamnätet, utan att behöva en router. I en nav och ekrar nätverkets topologi används VNet-peering för att ansluta hubben till varje eker. Du kan peerkoppla virtuella nätverk i samma region eller olika regioner.

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

Skapa den katalog som innehåller Terraform konfigurationsfilerna för demon.

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

1. Öppna en ny fil med namnet i Cloud Shell `main.tf`.

    ```bash
    code main.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```JSON
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Spara filen och avsluta redigeraren.

## <a name="create-the-variables-file"></a>Skapa filen variabler

Skapa Terraform-konfigurationsfil för vanliga variabler som används i olika skript.

1. Öppna en ny fil med namnet i Cloud Shell `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```JSON
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

1. Spara filen och avsluta redigeraren.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en lokal virtuellt nätverk med Terraform i Azure](./terraform-hub-spoke-on-prem.md)
