---
title: Självstudie – Skapa en nav och eker hybrid nätverkstopologi i Azure med terraform
description: Självstudie som illustrerar hur du skapar en hel referens arkitektur för Hybrid nätverk i Azure med terraform
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 8d85163e746f1d2d0713a9a4f247a2061e0029b8
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969399"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Självstudie: skapa en nav och eker hybrid nätverkstopologi i Azure med terraform

Den här själv studie serien visar hur du använder terraform för att implementera i Azure a [hubb och ekrar i ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

En nav-och eker-topologi är ett sätt att isolera arbets belastningar samtidigt som du delar vanliga tjänster. Dessa tjänster omfattar identitet och säkerhet. Hubben är ett virtuellt nätverk (VNet) som fungerar som en central anslutnings punkt till ett lokalt nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben. Delade tjänster distribueras i hubben, medan enskilda arbets belastningar distribueras i eker-nätverk.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att utforma nav och ekrar hybrid nätverks referens arkitektur resurser
> * Använd terraform för att skapa resurser för hubb nätverks utrustning
> * Använd terraform för att skapa Hubbs nätverk i Azure för att fungera som gemensam plats för alla resurser
> * Använd terraform för att skapa enskilda arbets belastningar som eker-virtuella nätverk i Azure
> * Använd terraform för att upprätta gatewayer och anslutningar mellan lokala nätverk och Azure-nätverk
> * Använd terraform för att skapa VNet-peering till ekrarade nätverk

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**: om du inte redan har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Installera och konfigurera terraform**: för att etablera virtuella datorer och annan infrastruktur i Azure, [Installera och konfigurera terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Arkitektur för nav och eker-topologi

I nav-och eker-topologin är hubben ett VNet. VNet fungerar som en central punkt för anslutningen till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer-kopplas med navet och som kan användas till att isolera arbetsbelastningar. Trafiken flödar mellan det lokala datacentret och navet via en ExpressRoute- eller VPN-gatewayanslutning. Följande bild visar komponenterna i en nav-och eker-topologi:

![Arkitektur för hubb och eker-topologi i Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Fördelar med nav-och eker-topologin

En nätverkstopologi med nav och ekrar är ett sätt att isolera arbets belastningar samtidigt som du delar vanliga tjänster. Dessa tjänster omfattar identitet och säkerhet. Hubben är ett VNet som fungerar som en central anslutnings punkt till ett lokalt nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben. Delade tjänster distribueras i hubben, medan enskilda arbets belastningar distribueras i eker-nätverk. Här är några fördelar med nätverk sto pol Ogin nav och ekrar:

- **Kostnads besparingar** genom att centralisera tjänster på en enda plats som kan delas av flera arbets belastningar. Dessa arbets belastningar inkluderar virtuella nätverks enheter och DNS-servrar.
- **Hantera prenumerationsgränser** genom att peer-koppla virtuella nätverk från olika prenumerationer till den centrala hubben.
- **Uppdelning** mellan central IT (SecOps, InfraOps) och arbetsbelastningar (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typiska användnings områden för hubb och eker-arkitekturen

Några vanliga användnings områden för hubb och eker-arkitektur är:

- Många kunder har arbets belastningar som distribueras i olika miljöer. I dessa miljöer ingår utveckling, testning och produktion. Många gånger måste dessa arbets belastningar dela tjänster som DNS, ID, NTP eller AD DS. Dessa delade tjänster kan placeras i hubbens VNet. På så sätt distribueras varje miljö till en eker för att upprätthålla isoleringen.
- Arbets belastningar som inte kräver anslutning till varandra, men som kräver åtkomst till delade tjänster.
- Företag som kräver Central kontroll över säkerhets aspekter.
- Företag som kräver en isolerad hantering för arbets belastningarna i varje eker.

## <a name="preview-the-demo-components"></a>Förhandsgranska demo komponenterna

När du arbetar med varje självstudie i den här serien definieras olika komponenter i distinkta terraform-skript. Demo arkitekturen som skapas och distribueras består av följande komponenter:

- **Lokalt nätverk**. Ett privat lokalt lokalt nätverk som körs med en organisation. För referens arkitektur för hubb och eker används ett VNet i Azure för att simulera ett lokalt nätverk.

- **VPN-enhet**. En VPN-enhet eller tjänst tillhandahåller extern anslutning till det lokala nätverket. VPN-enheten kan vara en maskin varu enhet eller en program varu lösning. 

- **Virtuellt hubbnätverk**. Hubben är den centrala punkten för anslutning till ditt lokala nätverk och en plats som är värd för tjänster. Dessa tjänster kan användas av de olika arbets belastningar som finns i eker-virtuella nätverk.

- **Gateway-undernät**. VNet-gatewayerna lagras i samma undernät.

- **Virtuella ekernätverk**. Ekrar kan användas för att isolera arbetsbelastningar i egna virtuella nätverk, som hanteras separat från andra ekrar. Varje arbetsbelastning kan innehålla flera nivåer, med flera undernät som är anslutna via Azure-lastbalanserare. 

- **VNET-peering**. Två virtuella nätverk kan anslutas med hjälp av en peering-anslutning. Peering-anslutningar är icke-transitiva anslutningar med låg latens mellan virtuella nätverk. När peer-kopplats virtuella nätverk Exchange-trafik med hjälp av Azures stamnät, utan att behöva en router. I en nätverkstopologi med nav och ekrar används VNet-peering för att ansluta hubben till varje eker. Du kan peer-virtuella nätverk i samma region eller i olika regioner.

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

Skapa den katalog som innehåller dina terraform-konfigurationsfiler för demonstrationen.

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

1. Öppna en ny fil med namnet `main.tf`i Cloud Shell.

    ```bash
    code main.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Spara filen och avsluta redigeraren.

## <a name="create-the-variables-file"></a>Skapa variabeln-filen

Skapa konfigurations filen terraform för vanliga variabler som används i olika skript.

1. Öppna en ny fil med namnet `variables.tf`i Cloud Shell.

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

1. Spara filen och avsluta redigeraren.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa lokalt virtuellt nätverk med terraform i Azure](./terraform-hub-spoke-on-prem.md)