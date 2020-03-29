---
title: Migrera virtuella datorer till Resource Manager med Azure CLI
description: Den här artikeln går igenom den plattformsstödda migreringen av resurser från klassisk till Azure Resource Manager med hjälp av Azure CLI
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: c41292a05e5c857cd0b1c120784a400f2f5410ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945357"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrera IaaS-resurser från klassisk distribution till Azure Resource Manager med hjälp av Azure CLI

> [!IMPORTANT]
> Idag använder cirka 90 % av virtuella IaaS virtuella datorer [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att vara helt pensionerade den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur det påverkar dig](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

De här stegen visar hur du använder CLI-kommandon (Azure Command-Line Interface) för att migrera infrastruktur som en tjänst (IaaS) resurser från den klassiska distributionsmodellen till Azure Resource Manager-distributionsmodellen. Artikeln kräver [Azure klassiska CLI](../../cli-install-nodejs.md). Eftersom Azure CLI endast gäller för Azure Resource Manager-resurser kan den inte användas för den här migreringen.

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotenta. Om du har ett annat problem än en funktion som inte stöds eller ett konfigurationsfel rekommenderar vi att du försöker förbereda, avbryta eller utföra åtgärden. Plattformen kommer sedan att försöka åtgärden igen.
> 
> 

<br>
Här är ett flödesschema för att identifiera i vilken ordning stegen måste utföras under en migreringsprocess

![Skärmbild som visar migreringsstegen](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Steg 1: Förbered för migrering
Här är några metodtips som vi rekommenderar när du utvärderar migrerande IaaS-resurser från klassiska till Resource Manager:

* Läs igenom [listan över konfigurationer eller funktioner som inte stöds](../windows/migration-classic-resource-manager-overview.md). Om du har virtuella datorer som använder konfigurationer eller funktioner som inte stöds rekommenderar vi att du väntar på att stöd för funktion/konfiguration ska meddelas. Du kan också ta bort den funktionen eller flytta ut ur den konfigurationen för att aktivera migrering om den passar dina behov.
* Om du har automatiserade skript som distribuerar din infrastruktur och dina program idag kan du försöka skapa en liknande testkonfiguration med hjälp av skripten för migrering. Du kan också ställa in exempelmiljöer med hjälp av Azure-portalen.

> [!IMPORTANT]
> Programgateways stöds för närvarande inte för migrering från klassisk till Resurshanteraren. Om du vill migrera ett klassiskt virtuellt nätverk med en programgateway tar du bort gatewayen innan du kör en Prepare-åtgärd för att flytta nätverket. När du har slutfört migreringen återansluter du gatewayen i Azure Resource Manager. 
>
>ExpressRoute-gateways som ansluter till ExpressRoute-kretsar i en annan prenumeration kan inte migreras automatiskt. I sådana fall tar du bort ExpressRoute-gatewayen, migrerar det virtuella nätverket och återskapar gatewayen. Se [Migrera ExpressRoute-kretsar och associerade virtuella nätverk från klassikern till Resurshanterarens distributionsmodell](../../expressroute/expressroute-migration-classic-resource-manager.md) för mer information.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Steg 2: Ange din prenumeration och registrera leverantören
För migreringsscenarier måste du konfigurera din miljö för både klassisk och Resurshanteraren. [Installera Azure CLI](../../cli-install-nodejs.md) och [välj din prenumeration](/cli/azure/authenticate-azure-cli).

Logga in på ditt konto.

    azure login

Välj Azure-prenumerationen med hjälp av följande kommando.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Registrering är ett engångssteg, men det måste göras en gång innan du försöker migrering. Utan att registrera dig visas följande felmeddelande 
> 
> *BadRequest : Prenumeration registreras inte för migrering.* 
> 
> 

Registrera dig hos migreringsresursprovidern med hjälp av följande kommando. Observera att i vissa fall time time out det här kommandot. Registreringen kommer dock att lyckas.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Vänta fem minuter för registreringen att avsluta. Du kan kontrollera status för godkännandet med hjälp av följande kommando. Kontrollera att RegistrationState `Registered` är innan du fortsätter.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Växla nu CLI `asm` till läget.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Steg 3: Se till att du har tillräckligt med virtuella azure Resource Manager-virtuella dator-processorer i Azure-regionen för din aktuella distribution eller VNET
För det här steget måste `arm` du växla till läge. Gör detta med följande kommando.

```
azure config mode arm
```

Du kan använda följande CLI-kommando för att kontrollera det aktuella antalet virtuella processorer som du har i Azure Resource Manager. Mer information om vCPU-kvoter finns i [Gränser och Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

När du är klar med att verifiera det `asm` här steget kan du växla tillbaka till läge.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Steg 4: Alternativ 1 - Migrera virtuella datorer i en molntjänst
Hämta listan över molntjänster med hjälp av följande kommando och välj sedan den molntjänst som du vill migrera. Observera att om de virtuella datorerna i molntjänsten finns i ett virtuellt nätverk eller om de har webb-/arbetsroller får du ett felmeddelande.

    azure service list

Kör följande kommando för att hämta distributionsnamnet för molntjänsten från den utförliga utdata. I de flesta fall är distributionsnamnet detsamma som molntjänstnamnet.

    azure service show <serviceName> -vv

Kontrollera först om du kan migrera molntjänsten med följande kommandon:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Förbered de virtuella datorerna i molntjänsten för migrering. Du har två alternativ att välja mellan.

Om du vill migrera de virtuella datorerna till ett virtuellt nätverk som skapats av plattformen använder du följande kommando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Om du vill migrera till ett befintligt virtuellt nätverk i resurshanterarens distributionsmodell använder du följande kommando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

När förbereda åtgärden har lyckats kan du titta igenom den utförliga utdata för att få `Prepared` migreringstillståndet för de virtuella datorerna och se till att de är i tillståndet.

    azure vm show <vmName> -vv

Kontrollera konfigurationen för de förberedda resurserna med hjälp av CLI eller Azure-portalen. Om du inte är redo för migrering och vill gå tillbaka till det gamla tillståndet använder du följande kommando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Om den förberedda konfigurationen ser bra ut kan du gå framåt och arkivera resurserna med hjälp av följande kommando.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Steg 4: Alternativ 2 - Migrera virtuella datorer i ett virtuellt nätverk
Välj det virtuella nätverk som du vill migrera. Observera att om det virtuella nätverket innehåller webb-/arbetsroller eller virtuella datorer med konfigurationer som inte stöds får du ett felmeddelande om validering.

Hämta alla virtuella nätverk i prenumerationen med hjälp av följande kommando.

    azure network vnet list

Utdata ser ut ungefär så här:

![Skärmbild av kommandoraden med hela det virtuella nätverksnamnet markerat.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

I exemplet ovan är **virtualNetworkName** hela namnet **"Gruppklassicicubuntu16 classicubuntu16".**

Kontrollera först om du kan migrera det virtuella nätverket med följande kommando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Förbered det virtuella nätverk som du väljer för migrering med hjälp av följande kommando.

    azure network vnet prepare-migration <virtualNetworkName>

Kontrollera konfigurationen för de förberedda virtuella datorerna med hjälp av CLI eller Azure-portalen. Om du inte är redo för migrering och vill gå tillbaka till det gamla tillståndet använder du följande kommando.

    azure network vnet abort-migration <virtualNetworkName>

Om den förberedda konfigurationen ser bra ut kan du gå framåt och arkivera resurserna med hjälp av följande kommando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Steg 5: Migrera ett lagringskonto
När du har migrerat de virtuella datorerna rekommenderar vi att du migrerar lagringskontot.

Förbereda lagringskontot för migrering med hjälp av följande kommando

    azure storage account prepare-migration <storageAccountName>

Kontrollera konfigurationen för det förberedda lagringskontot med hjälp av CLI eller Azure-portalen. Om du inte är redo för migrering och vill gå tillbaka till det gamla tillståndet använder du följande kommando.

    azure storage account abort-migration <storageAccountName>

Om den förberedda konfigurationen ser bra ut kan du gå framåt och arkivera resurserna med hjälp av följande kommando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
