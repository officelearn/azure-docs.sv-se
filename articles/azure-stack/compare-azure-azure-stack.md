---
title: Jämför Azure Stack och globala Azure | Microsoft Docs
description: Lär dig hur Microsoft ger Azure och Azure Stack-familj av tjänster i ett Azure-ekosystem
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650112"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Skillnader mellan globala Azure, Azure Stack och Azure Stack HCL

Microsoft tillhandahåller Azure och Azure Stack-familj av tjänster i en Azure-ekosystemet. Använd samma programmodell, självbetjäningsportaler och API: er med Azure Resource Manager för att leverera moln-baserade funktioner om ditt företag använder globala Azure eller lokala resurser.

Den här artikeln beskriver globala funktioner för Azure, Azure Stack och Azure Stack HCL och innehåller vanliga scenariot rekommendationer som hjälper dig att fatta det bästa valet för att leverera Microsoft molnbaserade tjänster för din organisation.

![Översikt över Azure-ekosystemet](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globalt Azure

Microsoft Azure är en ständigt växande uppsättning molntjänster som hjälper din organisation att hantera affärsutmaningar. Det är friheten att skapa, hantera och distribuera program i ett enormt globalt nätverk med hjälp av dina favoritverktyg och -ramverk.

Global Azure erbjuder fler än 100 tjänster som är tillgängliga i 54 regioner runt om i världen. Den mest aktuella listan över globala Azure-tjänster finns det [ *produkttillgänglighet per region*](https://azure.microsoft.com/regions/services). Tjänsterna som är tillgängliga i Azure är listade efter kategori, samt som om de är allmänt tillgängliga eller tillgänglig som förhandsversion.

Mer information om globala Azure-tjänster finns i [Kom igång med Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

Azure Stack är en utökning av Azure som ger flexibilitet och innovationer i molnbaserad databehandling till din lokala miljö. Distribueras lokalt, Azure Stack kan användas för att tillhandahålla Azure-kompatibla tjänster antingen är anslutna till internet (och Azure) eller i frånkopplade miljöer utan internet-anslutning. Azure Stack använder samma underliggande teknik som global Azure, vilket innefattar kärnkomponenterna angående Infrastructure-as-a-Service (IaaS), Software-as-a-Service (SaaS), och valfria Platform-as-a-Service (PaaS)-funktioner, inklusive:

- Azure Virtual Machines för Windows och Linux
- Azure Web Apps och Functions
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Containrar
- Azure IoT Hub och Event Hubs
- Administrationsverktyg (planer, erbjudanden, RBAC, osv.)

PaaS-funktioner i Azure Stack är valfria, eftersom Azure Stack inte drivs av Microsoft – den drivs av våra kunder. Det innebär att du kan erbjuda oavsett PaaS-tjänst som du vill att slutanvändarna om du är beredd att abstrahera underliggande infrastruktur och processer från användaren. Azure Stack omfattar flera valfria PaaS-leverantörer som t.ex. App Service, SQL-databaser och MySQL-databaser. Det här är levereras som resursproviders, så att de är flera innehavare är klart, uppdaterade över tid med standard uppdaterar Azure Stack, visas i Azure Stack-portalen och väl integrerad med Azure Stack.

Förutom de resursprovidrar som beskrivs ovan, finns ytterligare PaaS-tjänster tillgängliga och testade som [Azure Resource Manager-mall-baserade lösningar](https://github.com/Azure/AzureStack-QuickStart-Templates) som körs i IaaS, men du som ett Azure Stack-operatör kan erbjuda dem som PaaS-tjänster till dina användare, inklusive:

- Service Fabric
- Kubernetes Container Service
- IoT Hub och Event Hub
- Etherium Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Exempel på användningsområden för Azure Stack:

- Finansiell modellering
- Kliniska och anspråk
- Analytics för IoT-enhet
- Detaljhandel sortimentet optimering
- Optimering av leveranskedja
- Branschanpassat IoT
- Förebyggande underhåll
- Smart stad
- Medborgarengagemanget

Läs mer om Azure Stack på [vad är Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Azure Stack HCL-lösningar kan du köra virtuella datorer på plats och enkelt ansluta till Azure med en lösning för hyperkonvergerat-infrastruktur (HCL). Skapa och kör molnprogram med hjälp av konsekventa Azure-tjänster lokalt för att uppfylla regelmässiga eller tekniska krav. Förutom körs virtualiserade program lokalt kan Azure Stack HCL du ersätta och konsolidera föråldras serverinfrastruktur och ansluter till Azure för cloud services med Windows Admin Center.

Azure Stack-HCL innehåller verifierade HCL lösningar som drivs av Hyper-V och Lagringsdirigering med Windows Server 2019 programvarudefinierade Datacenter med. Windows Admin Center används för hantering och integrerade åtkomst till Azure-tjänster som:

- Azure Backup
- Azure Site Recovery
- Azure Monitor och uppdatering

För en uppdaterad lista över Azure-tjänster som du kan ansluta Azure Stack HCL att se [ansluter till Azure hybrid-tjänster i Windows Server](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Exempel på användningsområden för Azure Stack HCL
- Fjärr- eller gren office-system
- Datacenter konsolidering
- Virtuell datorinfrastruktur
- Affärskritisk infrastruktur
- Billigare lagring
- Hög tillgänglighet och katastrofåterställning recovery i molnet
- Företagsappar som SQL Server

Gå till den [Azure Stack HCL webbplats](https://azure.microsoft.com/overview/azure-stack/hci/) att visa 70 + Azure Stack HCL lösningar för närvarande tillgängliga från Microsofts partner.

## <a name="next-steps"></a>Nästa steg

[Azure Stack administration grunder](azure-stack-manage-basics.md)

[Snabbstart: Använd Azure Stack-administrationsportalen](azure-stack-manage-portals.md)
