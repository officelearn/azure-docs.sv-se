---
title: Ställ in VMware-replikering till Azure i multitenans miljö med Site Recovery och programmet Cloud Solution Providers (CSP) | Microsoft Docs
description: Beskriver hur du skapar och hanterar klient prenumerationer via CSP och distribuerar Azure Site Recovery i en inställning för flera innehavare
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 58ea2e7c387137f974425464ef2c9d17f438ba7c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071867"
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Ställ in VMware-replikering i en miljö med flera innehavare med Cloud Solution Providers (CSP)-programmet

Den [CSP-programmet](https://partner.microsoft.com/en-US/cloud-solution-provider) bättre tillsammans artiklar för Microsofts molntjänster, inklusive Office 365 Enterprise Mobility Suite och Microsoft Azure kan åstadkomma. Med CSP partners äger slutpunkt till slutpunkt-relation med kunder och blir den primära relation kontaktpunkten. Partners kan distribuera Azure-prenumerationer för kunder och kombinera prenumerationer med sina egna mervärde, anpassade erbjudanden.

Med [Azure Site Recovery](site-recovery-overview.md), som partner kan du hantera katastrofåterställning för kunder direkt via CSP. Alternativt kan du använda CSP för att ställa in Site Recovery-miljöer och informera kunderna hantera sina egna disaster recovery behov på ett sätt för självbetjäning. I båda fallen är partners samverkan mellan Site Recovery och sina kunder. Partners tjänsten customer relationship och debitera kunder för användning i Site Recovery.

Den här artikeln beskriver hur du som partner kan skapa och hantera innehavare prenumerationer via CSP för ett scenario med flera innehavare VMware-replikering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera VMware-replikering måste du göra följande:

- [Förbereda](tutorial-prepare-azure.md) Azure-resurser, inklusive en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
- [Förbereda](vmware-azure-tutorial-prepare-on-premises.md) lokal VMware-servrar och virtuella datorer.
- Skapa en separat hanteringsserver som kan kommunicera med de virtuella klientdatorerna och vCenter-servrar för varje klient. Bara du som partner bör ha åtkomsträttigheter till den här hanteringsservern. Lär dig mer om [miljöer med flera innehavare](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Skapa ett klient-konto

1. Via [Microsoft Partner Center](https://partnercenter.microsoft.com/), logga in på CSP-konto.
2. På den **instrumentpanelen** väljer du **kunder**.
3. På sidan som öppnas i **Lägg till kunden** knappen.
4. I **ny kund** sidan, fyller i information kontoinformation för klienten.

    ![Sidan kontoinformation](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Klicka på **nästa: prenumerationer**.
6. På valsidan av prenumerationer väljer **Microsoft Azure** kryssrutan. Du kan lägga till andra prenumerationer nu eller vid ett senare tillfälle.
7. På den **granska** sidan Bekräfta klient informationen och klicka sedan på **skicka**.
8. När du har skapat kontot klienten en bekräftelse visas med information om standardkontot och lösenordet för den prenumerationen. Spara informationen och ändra lösenord senare vid behov via Azure portal-inloggningssidan.

Du kan dela information med innehavaren som är eller du kan skapa och dela ett särskilt konto om det behövs.

## <a name="access-the-tenant-account"></a>Få åtkomst till kontot för klient

Du kan använda klientens prenumerationen via Microsoft Partner Center Dashboard.

1. På den **kunder** klickar du på namnet på klient-konto.
2. I den **prenumerationer** sidan för klient-konto kan du övervaka de befintliga prenumerationerna för kontot och lägga till flera prenumerationer efter behov.
3. Om du hanterar klientens disaster recovery-åtgärder, välja **alla resurser (Azure portal)**. Detta ger dig åtkomst till klientens Azure-prenumerationer.

    ![Länken alla resurser](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Du kan verifiera åtkomst genom att klicka på Azure Active Directory-länken längst upp i Azure-portalen.

    ![Azure Active Directory-länk](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Du kan nu utföra och hantera alla Site Recovery-åtgärder för klienten i Azure-portalen. Följ beskrivits tidigare processen för klient-prenumerationen via CSP för hanterade katastrofåterställning.

## <a name="assign-tenant-access-to-the-subscription"></a>Tilldela klienten åtkomst till prenumerationen

1. Se till att disaster recovery-infrastruktur är korrekt. Partners åtkomst till klient prenumerationer via CSP-portalen, oavsett om katastrofåterställning hanteras eller självbetjäning. Ställ in valvet och registrera infrastruktur för klient-prenumerationer.
2. Ge innehavare med den [konto som du skapade](#create-a-tenant-account).
3. Du kan lägga till en ny användare klientprenumeration CSP-portalen på följande sätt:

    en) Gå till prenumerationssidan för klientens CSP och välj sedan den **användare och licenser** alternativet.

      ![Klientens CSP prenumerationssidan](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

      b) skapa en ny användare nu genom att ange relevant information och välja behörigheter, eller genom att ladda upp en lista över användare i en CSV-fil.
    c) när du har skapat en ny användare kan gå tillbaka till Azure-portalen. I den **prenumeration** markerar du den aktuella prenumerationen.
    d) Välj **Access Control (IAM)**, och klicka sedan på **Lägg till**, för att lägga till en användare med relevanta åtkomstnivå. Användare som har skapats via portalen CSP visas automatiskt på sidan som öppnas när du klickar på åtkomstnivå.

      ![Lägga till en användare](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- För de flesta hanteringsåtgärder på *deltagare* roll är tillräckliga. Användare med den här åtkomstnivån kan göra allt på en prenumeration förutom ändra åtkomstnivåerna (som *ägare*-åtkomst krävs).
- Site Recovery har tre [fördefinierade användarroller](site-recovery-role-based-linked-access-control.md), som kan användas för att ytterligare begränsa åtkomstnivåer som krävs.

## <a name="multi-tenant-environments"></a>Miljöer med flera innehavare

Det finns tre huvudsakliga modeller för flera innehavare:

* **Delade värd Services Provider (HSP)**: partnern som äger den fysiska infrastrukturen och använder delade resurser (vCenter, Datacenter, fysisk lagring och så vidare) som värd för flera innehavare virtuella datorer på samma infrastruktur. Partnern kan tillhandahålla katastrofåterställning hantering som en hanterad tjänst eller klienten kan äga katastrofåterställning som en lösning för självbetjäning.

* **Dedikerad värd tjänsteleverantör**: partnern som äger den fysiska infrastrukturen, men använder dedicerade resurser (flera Vcenter fysiska datastores och så vidare) som värd för varje klient virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla katastrofåterställning hantering som en hanterad tjänst eller klienten kan äga som en lösning för självbetjäning.

* **Hanterade Services Provider (MSP)**: kunden äger den fysiska infrastrukturen som är värd för de virtuella datorerna och partnern tillhandahåller katastrofåterställning aktivering och hantering.

Genom att ställa in klient prenumerationer som beskrivs i den här artikeln, kan du snabbt börja aktiverar kunder i någon av relevanta flera innehavare. Du kan lära dig mer om de olika modellerna för flera innehavare och aktivera lokala åtkomstkontroller [här](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [rollbaserad åtkomstkontroll](site-recovery-role-based-linked-access-control.md) hantera distributioner av Azure Site Recovery.
- Mer information om VMware till Azure [replikeringsarkitektur](vmware-azure-architecture.md).
- [Gå igenom kursen](vmware-azure-tutorial.md) för att replikera virtuella VMware-datorer till Azure.
Lär dig mer om [miljöer med flera innehavare](vmware-azure-multi-tenant-overview.md) för att replikera virtuella VMware-datorer till Azure.
