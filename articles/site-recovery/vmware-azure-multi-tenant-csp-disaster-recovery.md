---
title: Konfigurera VMware-replikering till Azure i miljö för flera innehavare med hjälp av Site Recovery och programmet Cloud Solution Provider (CSP) | Microsoft Docs
description: Beskriver hur du skapar och hanterar klient prenumerationer via sin Molnlösningsleverantör och distribuerar Azure Site Recovery i en inställning för flera innehavare
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: d384a77054cdcab0305b9e6d3fe5bb824e49bb16
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916810"
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Konfigurera VMware-replikering i en miljö med flera innehavare med programmet Cloud Solution Provider (CSP)

Den [CSP-programmet](https://partner.microsoft.com/en-US/cloud-solution-provider) kan åstadkomma bättre tillsammans berättelser för Microsofts molntjänster, inklusive Office 365, Enterprise Mobility Suite och Microsoft Azure. Med CSP partner äger slutpunkt till slutpunkt-relation med kunder och bli den primära relation kontaktpunkten. Partners kan distribuera Azure-prenumerationer för kunder och kombinera prenumerationer med sina egna försäljning, anpassade erbjudanden.

Med [Azure Site Recovery](site-recovery-overview.md), som partner kan du hantera haveriberedskap för kunder direkt via CSP. Alternativt kan du använda CSP för att konfigurera Site Recovery-miljöer och låt kunderna hantera sina egna disaster recovery behov på ett sätt för självbetjäning. I båda fallen är partner samverkan mellan Site Recovery och sina kunder. Partner tjänsten kundrelationen och debitera kunder för Site Recovery-användning.

Den här artikeln beskrivs hur du som partner kan skapa och hantera klient prenumerationer via sin Molnlösningsleverantör, för ett scenario med flera innehavare och VMware-replikering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera VMware-replikering måste du göra följande:

- [Förbereda](tutorial-prepare-azure.md) Azure-resurser, inklusive en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
- [Förbereda](vmware-azure-tutorial-prepare-on-premises.md) lokala VMware-servrar och virtuella datorer.
- Skapa en separat hanteringsserver som kan kommunicera med de virtuella klientdatorerna och vCenter-servrar för varje klient. Endast du som partner bör ha åtkomsträttigheter till den här hanteringsservern. Läs mer om [miljöer för flera innehavare](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Skapa ett klientkonto

1. Via [Microsoft Partner Center](https://partnercenter.microsoft.com/), logga in på din CSP-konto.
2. På den **instrumentpanelen** menyn och välj **kunder**.
3. På sidan som öppnas, klickar du på den **Lägg till kunden** knappen.
4. I **ny kund** fyller du i information kontoinformation för klienten.

    ![Sidan kontoinformation](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Klicka sedan på **nästa: prenumerationer**.
6. På prenumerationssidan, Välj **Microsoft Azure** markerar du kryssrutan. Du kan lägga till andra prenumerationer nu eller vid en annan tidpunkt.
7. På den **granska** sidan Bekräfta klient informationen och klicka sedan på **skicka**.
8. När du har skapat kontot klient en bekräftelse visas med information om standardkontot och lösenordet för den aktuella prenumerationen. Spara informationen och ändra lösenordet senare vid behov, via Azure portal-inloggningssidan.

Du kan dela information med klient som är eller du kan skapa och dela ett separat konto om det behövs.

## <a name="access-the-tenant-account"></a>Åtkomst till klient-kontot

Du kan komma åt den innehavarens prenumeration via Microsoft Partner Center-instrumentpanelen.

1. På den **kunder** klickar du på namnet på klientkonto.
2. I den **prenumerationer** sidan för klientkonto som du kan övervaka de befintliga prenumerationerna för kontot och lägga till flera prenumerationer, efter behov.
3. För att hantera klientens disaster recovery-åtgärder, Välj **alla resurser (Azure portal)**. Detta ger dig åtkomst till klientens Azure-prenumerationer.

    ![Länken alla resurser](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Du kan verifiera åtkomst genom att klicka på Azure Active Directory-länken längst upp till höger på Azure-portalen.

    ![Azure Active Directory-länk](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Du kan nu skapa och hantera alla Site Recovery-åtgärder för klienten i Azure-portalen. Följ tidigare beskrivna processen för att komma åt klientprenumeration via CSP för hanterade disaster recovery.

## <a name="assign-tenant-access-to-the-subscription"></a>Tilldela klienten åtkomst till prenumerationen

1. Se till att disaster recovery-infrastruktur är korrekt. Partner åtkomst till klient prenumerationer via CSP-portalen, oavsett om haveriberedskap hanteras eller självbetjäning. Ställa in valvet och registrera infrastruktur till klient-prenumerationer.
2. Ange klient med den [kontot som du skapade](#create-a-tenant-account).
3. Du kan lägga till en ny användare till klient-prenumerationen via CSP-portalen på följande sätt:

    (a) Gå till sidan för klientens CSP-prenumeration och välj sedan den **användare och licenser** alternativet.

      ![Klientens prenumerationssidan för CSP](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      b) skapa en ny användare nu genom att ange relevant information och välja behörigheter eller genom att ladda upp en lista över användare i en CSV-fil.
    c) när du har skapat en ny användare, gå tillbaka till Azure-portalen. I den **prenumeration** väljer du prenumerationen som är relevanta.
    d) Välj **åtkomstkontroll (IAM)**, och klicka sedan på **Lägg till**, lägga till en användare med den relevanta åtkomstnivån. De användare som har skapats via CSP-portalen visas automatiskt på sidan som öppnas när du klickar på åtkomstnivå.

      ![Lägga till en användare](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- För de flesta hanteringsåtgärderna i *deltagare* roll är tillräckliga. Användare med den här åtkomstnivån kan göra allt på en prenumeration men ändrar åtkomstnivåer (som *ägare*-åtkomst krävs).
- Site Recovery har tre [fördefinierade användarroller](site-recovery-role-based-linked-access-control.md), som kan användas för att ytterligare begränsa åtkomstnivåer som krävs.

## <a name="multi-tenant-environments"></a>Miljöer för flera innehavare

Det finns tre större modeller för flera innehavare:

* **Delade som är värd för Services Provider (HSP)**: partnern äger den fysiska infrastrukturen och använder delade resurser (vCenter, Datacenter, fysiska lagringsutrymmet och så vidare) som värd för flera virtuella klientdatorer på samma infrastruktur. Partnern kan tillhandahålla katastrofåterställning management som en hanterad tjänst eller klienten kan äga haveriberedskap som en lösning för självbetjäning.

* **Dedikerad som är värd för tjänsteleverantör**: partnern äger den fysiska infrastrukturen, men använder dedikerade resurser (flera vCenters fysiska datalager och så vidare) som värd för varje klient virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla katastrofåterställning management som en hanterad tjänst eller klienten kan äga den som en lösning för självbetjäning.

* **Managed Services Provider (MSP)**: kunden äger den fysiska infrastrukturen som är värd för de virtuella datorerna och partnern som tillhandahåller katastrofåterställning aktivering och hantering.

Genom att konfigurera klient prenumerationer enligt beskrivningen i den här artikeln, kan du snabbt starta och kunderna i någon av de relevanta modellerna för flera innehavare. Du kan läsa mer om de olika modellerna för flera innehavare och aktivera lokalt åtkomstkontroller [här](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [rollbaserad åtkomstkontroll](site-recovery-role-based-linked-access-control.md) hantera distributioner av Azure Site Recovery.
- Mer information om VMware till Azure [replikeringsarkitektur](vmware-azure-architecture.md).
- [Gå igenom självstudien](vmware-azure-tutorial.md) för att replikera virtuella VMware-datorer till Azure.
Läs mer om [miljöer för flera innehavare](vmware-azure-multi-tenant-overview.md) för att replikera virtuella VMware-datorer till Azure.
