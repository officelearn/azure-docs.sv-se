---
title: Azure Hybrid-förmån och virtuella Linux-datorer
description: Med Azure Hybrid-förmån kan du spara pengar på dina virtuella Linux-datorer som körs på Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: feaa2471f2867257deb06ab32ed5fc0a26a0d37e
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443440"
---
# <a name="public-preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Offentlig för hands version: Azure Hybrid-förmån – hur det gäller för Virtuella Linux-datorer

## <a name="overview"></a>Översikt

Azure Hybrid-förmån gör att du enkelt kan migrera dina lokala Red Hat Enterprise Linux (RHEL) och SUSE Linux Enterprise Server (virtuella datorer) till Azure med hjälp av din egen befintliga Red Hat-eller SUSE-programprenumeration. Med den här förmånen betalar du bara för infrastruktur kostnaderna för din virtuella dator eftersom program varu avgiften täcks av din RHEL-eller SLES-prenumeration. Förmånen gäller för alla PAYG-avbildningar (RHEL-och SLES Marketplace).

> [!IMPORTANT]
> Azure Hybrid-förmån för virtuella Linux-datorer är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefit-description"></a>Förmåns Beskrivning

Med hjälp av Azure Hybrid-förmån kan du lättare migrera dina lokala RHEL-och SLES-servrar till Azure genom att konvertera befintliga RHEL-och SLES PAYG-datorer på Azure för att få en BYOS-fakturering. Vanligt vis debiteras virtuella datorer som distribueras från PAYG-avbildningar på Azure både en infrastruktur avgift och en program varu avgift. Med Azure Hybrid-förmån kan virtuella datorer konverteras till en BYOS-fakturerings modell utan att omdistribueras, vilket undviker eventuell stillestånds risk.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid-förmån kostnads visualisering på virtuella Linux-datorer.":::

När du aktiverar fördelen med en RHEL-eller SLES-VM debiteras du inte längre för den ytterligare program varu avgiften som normalt uppstår på en PAYG VM. I stället påbörjas den virtuella datorn med en BYOS avgift som bara omfattar beräknings maskin varu avgiften och ingen program varu avgift.

Om du vill kan du också konvertera en virtuell dator som har förmånen aktiverat på den till en PAYG fakturerings modell.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Omfattning för Azure Hybrid-förmån berättigande för virtuella Linux-datorer

Azure Hybrid-förmån är tillgängligt för alla RHEL-och SLES Marketplace-PAYG-avbildningar. Förmånen är ännu inte tillgänglig för RHEL-eller SLES Marketplace-BYOS-bilder eller anpassade avbildningar.

Reserverade instanser, dedikerade värdar och SQL hybrid-förmåner är inte berättigade till Azure Hybrid-förmån om du redan använder fördelarna med virtuella Linux-datorer.

## <a name="how-to-get-started"></a>Så här kommer du igång

Azure Hybrid-förmån finns för närvarande i en för hands version av virtuella Linux-datorer. När du får åtkomst till förhands granskningen kan du aktivera förmånen med Azure CLI.

### <a name="public-preview"></a>Offentlig för hands version

Azure Hybrid-förmån (för Linux) finns nu i en offentlig för hands versions fas. Du kan använda stegen nedan för att aktivera förmånerna för Red Hat-och SUSE-distributioner. 

### <a name="red-hat-customers"></a>Red Hat-kunder

1.    Registrera dig för [Red Hat Cloud Access-programmet](https://aka.ms/rhel-cloud-access)
1.    Aktivera din Azure-prenumeration (er) för moln åtkomst och aktivera prenumerationerna som innehåller de virtuella datorer som du vill använda förmånen med
1.    Tillämpa förmånen för dina befintliga virtuella datorer via Azure CLI
1.    Registrera dina virtuella datorer som tar del av förmånen med en separat källa till uppdateringar


### <a name="suse-customers"></a>SUSE kunder

1.    Registrera dig för det offentliga moln programmet för SUSE
1.    Tillämpa förmånen för dina befintliga virtuella datorer via Azure CLI
1.    Registrera dina virtuella datorer som tar del av förmånen med en separat källa till uppdateringar


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Aktivera och inaktivera förmånen i Azure CLI

Du kan använda kommandot "AZ VM Update" för att uppdatera befintliga virtuella datorer. För virtuella RHEL-datorer kör du kommandot med parametern--licens typ för RHEL_BYOS. För virtuella SLES-datorer kör du kommandot med parametern--licens typ för SLES_BYOS.

#### <a name="cli-example-to-enable-the-benefit"></a>CLI-exempel för att aktivera förmånen:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>CLI-exempel för att inaktivera förmånen:
Om du vill inaktivera förmånen använder du värdet none för licens typ
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-exempel för att aktivera förmånen för ett stort antal virtuella datorer
Om du vill aktivera förmånen för ett stort antal virtuella datorer kan du använda- `--ids` parametern i Azure CLI.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

I följande exempel visas två metoder för att hämta en lista över resurs-ID: n – en på resurs grupps nivå, en på prenumerations nivå.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Kontrol lera AHB status för en virtuell dator
Du kan visa status för AHB för en virtuell dator på två sätt: använda Azure CLI eller använda Azure-Instance Metadata Service (Azure IMDS).


### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`Kommandot kan användas för detta ändamål. Leta efter ett licenseType-fält i svaret. Om fältet licenseType finns och värdet är "RHEL_BYOS" eller "SLES_BYOS" har den virtuella datorn förmånen aktive rad.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Från den virtuella datorn kan du fråga IMDS-bestyrkade metadata för att fastställa den virtuella datorns licenseType. LicenseType-värdet RHEL_BYOS eller SLES_BYOS anger att den virtuella datorn har förmånen aktive rad. Läs mer om attestering av metadata [här](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Efterlevnad

### <a name="red-hat"></a>Red Hat

För att kunna använda Azure Hybrid-förmån för dina virtuella RHEL-datorer måste du först vara registrerad med Red Hat Cloud Access-programmet. Du kan göra detta via webbplatsen för Red Hat Cloud Access här. När du har aktiverat förmånen på den virtuella datorn måste du registrera den virtuella datorn med din egen källa med uppdateringar antingen med Red Hat-prenumerations hanteraren eller Red Hat satellit. Om du registrerar dig för uppdateringar ser du till att det finns ett tillstånd som stöds.

### <a name="suse"></a>SUSE

För att kunna använda Azure Hybrid-förmån för dina virtuella SLES-datorer måste du först vara registrerad med det offentliga SUSE moln programmet. Läs mer om programmet här. När du har köpt SUSE-prenumerationer måste du registrera dina virtuella datorer med dessa prenumerationer på din egen källa med uppdateringar med hjälp av antingen SUSE kund Center, prenumerations hanterings verktygets Server eller SUSE Manager.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
*F: kan jag använda licens typen "RHEL_BYOS" med en SLES-avbildning eller vice versa?*

A: Nej, du kan inte. Försök att ange en licens typ som felaktigt matchar den distribution som körs på den virtuella datorn kommer inte att uppdatera några fakturerings-metadata. Men om du av misstag anger fel licens typ kan du fortfarande aktivera förmånen om du uppdaterar den virtuella datorn igen till rätt licens typ.

*F: Jag har registrerat dig med Red Hat Cloud Access men det går fortfarande inte att aktivera förmånen för mina virtuella RHEL-datorer. Vad gör jag?*

A: det kan ta lite tid för prenumerations registreringen av Red Hat Cloud Access att sprida sig från Red Hat till Azure. Kontakta Microsoft-supporten om du fortfarande ser felet efter en arbets dag.

## <a name="common-issues"></a>Vanliga problem
Det här avsnittet innehåller en lista över vanliga problem som kan påträffas och åtgärder för att minska.

| Fel | Åtgärd |
| ----- | ---------- |
| "Åtgärden kunde inte slutföras eftersom våra poster visar att du inte har aktiverat Red Hat Cloud Access på din Azure-prenumeration..." | För att kunna använda förmånen med virtuella datorer i RHEL måste du först registrera din Azure-prenumeration (er) med Red Hat Cloud Access. Besök den här länken om du vill veta mer om hur du registrerar Azure-prenumerationer för Red Hat Cloud Access

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar och uppdaterar virtuella datorer och lägger till licens typer (RHEL_BYOS SLES_BYOS) för Azure Hybrid-förmån med hjälp av [Azure CLI här.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)
