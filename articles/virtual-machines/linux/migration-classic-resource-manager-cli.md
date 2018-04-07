---
title: Migrera virtuella datorer till Resource Manager med hjälp av Azure CLI | Microsoft Docs
description: Den här artikeln innehåller stegvisa migreringen plattform som stöds av resurser från klassiska till Azure Resource Manager med hjälp av Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: f986246e74305789eb2978a95fd6a3e51accd25e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure CLI
Dessa steg visar hur du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att migrera infrastruktur som en tjänst (IaaS)-resurser från den klassiska distributionsmodellen till Azure Resource Manager-distributionsmodellen. Artikeln kräver den [Azure CLI 1.0](../../cli-install-nodejs.md). Eftersom Azure CLI 2.0 gäller endast för Azure Resource Manager-resurser, kan inte användas för den här migreringen.

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotent. Om du har problem med än en funktion som inte stöds eller ett konfigurationsfel rekommenderar vi att du försöka med att förbereda, avbrytas eller genomföras igen. Plattformen försök sedan igen.
> 
> 

<br>
Här är ett flödesschema för att identifiera den ordning som steg måste utföras under en migreringsprocessen

![Skärmbild som visar migreringsstegen](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Steg 1: Förbered för migrering
Här följer några metodtips som vi rekommenderar medan du utvärderar migrera IaaS-resurser från klassiska till Resource Manager:

* Läs igenom den [lista över konfigurationer som inte stöds eller funktioner](../windows/migration-classic-resource-manager-overview.md). Om du har virtuella datorer som använder icke-stödda konfigurationer eller funktioner, rekommenderar vi att du väntar funktionen/konfigurationsstöd meddelas. Du kan också ta bort funktionen eller flytta från den konfigurationen du aktiverar migreringen om den passar dina behov.
* Om du har automatiserat skript som distribuerar din infrastruktur och dina program idag, försök att skapa en liknande inställningar med hjälp av dessa skript för migrering. Du kan också ställa in provet miljöer med hjälp av Azure portal.

> [!IMPORTANT]
> Programgatewayer stöds inte för migrering från classic till Resource Manager. Ta bort gatewayen innan du kör en Förberedelseåtgärden om du vill flytta nätverket för att migrera ett klassiskt virtuellt nätverk med en Programgateway. När du har slutfört migreringen återansluta gateway i Azure Resource Manager. 
>
>ExpressRoute-gatewayer som ansluter till ExpressRoute-kretsar i en annan prenumeration som inte kan migreras automatiskt. I sådana fall kan ta bort ExpressRoute-gatewayen, migrera det virtuella nätverket och skapa gatewayen på nytt. Se [migrera ExpressRoute-kretsar och associerade virtuella nätverk från klassiskt till Resource Manager-distributionsmodellen](../../expressroute/expressroute-migration-classic-resource-manager.md) för mer information.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Steg 2: Ange din prenumeration och registrera providern
För Migreringsscenarier, måste du konfigurera din miljö för både klassiska och Resource Manager. [Installera Azure CLI](../../cli-install-nodejs.md) och [väljer din prenumeration](/cli/azure/authenticate-azure-cli).

Logga in på ditt konto.

    azure login

Välj den Azure-prenumerationen med hjälp av följande kommando.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Registreringen är en tid step men måste göras en gång innan du försöker migrera. Utan att registrera visas följande felmeddelande 
> 
> *BadRequest: Prenumerationen har inte registrerats för migrering.* 
> 
> 

Registrera med resursprovidern migrering med hjälp av följande kommando. Observera att i vissa fall kan det här kommandot på grund av timeout. Registreringen kommer dock att lyckas.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Vänta 5 minuter att slutföra registreringen. Du kan kontrollera status för godkännande med hjälp av följande kommando. Se till att RegistrationState `Registered` innan du fortsätter.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Växla CLI för att den `asm` läge.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Steg 3: Kontrollera att du har tillräckligt med Azure Resource Manager-dator vCPUs i Azure-regionen för din aktuella distributionen eller virtuella nätverk
För det här steget måste du växla till `arm` läge. Du kan göra detta med följande kommando.

```
azure config mode arm
```

Du kan använda kommandot CLI för att kontrollera det aktuella antalet vCPUs som du har i Azure Resource Manager. Mer information om vCPU kvoter finns [gränser och Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

När du är klar verifiera det här steget kan du växla tillbaka till `asm` läge.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Steg 4: Alternativ 1 – migrera virtuella datorer i en tjänst i molnet
Med följande kommando för att hämta listan över molntjänster och välj sedan den molntjänst som du vill migrera. Observera att om de virtuella datorerna i Molntjänsten är i ett virtuellt nätverk eller om de har web/worker-roller, du får ett felmeddelande.

    azure service list

Kör följande kommando för att hämta distributionens namn för Molntjänsten från utförlig utdata. I de flesta fall är distributionens namn samma som namnet på molnet.

    azure service show <serviceName> -vv

Verifiera först om du kan migrera Molntjänsten med hjälp av följande kommandon:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Förbered de virtuella datorerna i Molntjänsten för migrering. Du har två alternativ att välja mellan.

Använd följande kommando om du vill migrera de virtuella datorerna till ett virtuellt nätverk skapas av plattform.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Använd följande kommando om du vill migrera till ett befintligt virtuellt nätverk i Resource Manager-distributionsmodellen.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

Efter att Förberedelseåtgärden har lyckats, kan du titta igenom utförlig utdata till migrering tillståndet för de virtuella datorerna och se till att de är i den `Prepared` tillstånd.

    azure vm show <vmName> -vv

Kontrollera konfigurationen för de förberedda resurserna med hjälp av CLI eller Azure-portalen. Om du inte är klar för migrering och du vill gå tillbaka till ett tidigare tillstånd, använder du följande kommando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Om förberedda konfigurationen ser bra ut kan du gå vidare och genomför resurser med hjälp av följande kommando.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Steg 4: Alternativ 2 – migrera virtuella datorer i ett virtuellt nätverk
Välj det virtuella nätverk som du vill migrera. Observera att om det virtuella nätverket innehåller web/worker-roller eller virtuella datorer med konfigurationer som inte stöds, visas ett felmeddelande för verifiering.

Hämta alla virtuella nätverk i prenumerationen med hjälp av följande kommando.

    azure network vnet list

Resultatet ser ut ungefär så här:

![Skärmbild av kommandoraden med hela virtuella nätverksnamnet markerat.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

I exemplet ovan är de **virtualNetworkName** hela namnet **”grupp classicubuntu16 classicubuntu16”**.

Verifiera först om du kan migrera det virtuella nätverket med hjälp av följande kommando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Förbered det virtuella nätverket önskat för migrering med hjälp av följande kommando.

    azure network vnet prepare-migration <virtualNetworkName>

Kontrollera konfigurationen för förberedda virtuella datorer med hjälp av CLI eller Azure-portalen. Om du inte är klar för migrering och du vill gå tillbaka till ett tidigare tillstånd, använder du följande kommando.

    azure network vnet abort-migration <virtualNetworkName>

Om förberedda konfigurationen ser bra ut kan du gå vidare och genomför resurser med hjälp av följande kommando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Steg 5: Migrera ett lagringskonto
När du är klar migrering av virtuella datorer, rekommenderar vi du migrera lagringskontot.

Förbereda lagringskontot för migrering med hjälp av följande kommando

    azure storage account prepare-migration <storageAccountName>

Kontrollera konfigurationen för förberedda storage-konto med hjälp av CLI eller Azure-portalen. Om du inte är klar för migrering och du vill gå tillbaka till ett tidigare tillstånd, använder du följande kommando.

    azure storage account abort-migration <storageAccountName>

Om förberedda konfigurationen ser bra ut kan du gå vidare och genomför resurser med hjälp av följande kommando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformar som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använda PowerShell för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassiska till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
