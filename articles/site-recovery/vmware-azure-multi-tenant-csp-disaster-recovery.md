---
title: Konfigurera VMware-haveriberedskap till Azure i en miljö med flera innehavare med hjälp av Site Recovery och CSP-programmet (Cloud Solution Provider) | Microsoft-dokument
description: Beskriver hur du konfigurerar VMware-haveriberedskap i en miljö med flera innehavare med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60461027"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Konfigurera VMware-haveriberedskap i en flerargsmiljö med CSP-programmet (Cloud Solution Provider)

[CSP-programmet](https://partner.microsoft.com/en-US/cloud-solution-provider) främjar bättre sammansvärjningar för Microsofts molntjänster, inklusive Office 365, Enterprise Mobility Suite och Microsoft Azure. Med CSP äger partnerna relationen från slutpunkt till slutpunkt med kunder och blir den primära relationskontaktpunkten. Partner kan distribuera Azure-prenumerationer för kunder och kombinera prenumerationerna med sina egna mervärdesanpassade, anpassade erbjudanden.

Med [Azure Site Recovery](site-recovery-overview.md)kan du som partner hantera haveriberedskap för kunder direkt via CSP. Alternativt kan du använda CSP för att konfigurera site recovery-miljöer och låta kunderna hantera sina egna behov av haveriberedskap på ett självbetjäningssätt. I båda scenarierna är partnerna länken mellan Site Recovery och deras kunder. Partner bes servar kundrelationen och fakturerar kunder för användning av webbplatsåterställning.

I den här artikeln beskrivs hur du som partner kan skapa och hantera klientprenumerationer via CSP, för ett VMware-replikeringsscenario med flera innehavare.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera VMware-replikering måste du göra följande:

- [Förbered](tutorial-prepare-azure.md) Azure-resurser, inklusive en Azure-prenumeration, ett virtuellt Azure-nätverk och ett lagringskonto.
- [Förbered](vmware-azure-tutorial-prepare-on-premises.md) lokala VMware-servrar och virtuella datorer.
- Skapa en separat hanteringsserver som kan kommunicera med klientens virtuella datorer och dina vCenter-servrar för varje klient. Endast du som partner bör ha åtkomsträttigheter till den här hanteringsservern. Läs mer om [miljöer med flera innehavare](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Skapa ett klientkonto

1. Logga in på ditt CSP-konto via [Microsoft Partner Center.](https://partnercenter.microsoft.com/)
2. Välj **Kunder**på **Instrumentpanelsmenyn** .
3. Klicka på knappen Lägg **till kund** på sidan som öppnas.
4. På sidan **Ny kund** fyller du i kontoinformationsinformationen för klienten.

    ![Sidan Kontoinformation](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Klicka sedan på **Nästa: Prenumerationer**.
6. Markera kryssrutan **Microsoft Azure** på urvalssidan för prenumerationer. Du kan lägga till andra prenumerationer nu eller när som helst.
7. Bekräfta klientinformationen på sidan **Granska** och klicka sedan på **Skicka**.
8. När du har skapat klientkontot visas en bekräftelsesida som visar information om standardkontot och lösenordet för prenumerationen. Spara informationen och ändra lösenordet senare efter behov via inloggningssidan för Azure Portal.

Du kan dela den här informationen med klienten som den är, eller så kan du skapa och dela ett separat konto om det behövs.

## <a name="access-the-tenant-account"></a>Få tillgång till klientkontot

Du kan komma åt klientens prenumeration via Instrumentpanelen för Microsoft Partner Center.

1. Klicka på namnet på klientkontot på sidan **Kunder.**
2. På sidan **Prenumerationer** på klientkontot kan du övervaka befintliga kontoprenumerationer och lägga till fler prenumerationer efter behov.
3. Om du vill hantera klientens haveriberedskapsoperationer väljer du **Alla resurser (Azure-portalen).** Detta ger dig åtkomst till klientens Azure-prenumerationer.

    ![Länken Alla resurser](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Du kan verifiera åtkomst genom att klicka på Azure Active Directory-länken längst upp till höger i Azure-portalen.

    ![Azure Active Directory-länk](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Du kan nu utföra och hantera alla site recovery-åtgärder för klienten i Azure-portalen. För att komma åt klientprenumerationen via CSP för hanterad haveriberedskap, följ den tidigare beskrivna processen.

## <a name="assign-tenant-access-to-the-subscription"></a>Tilldela klientåtkomst till prenumerationen

1. Se till att infrastrukturen för haveriberedskap har konfigurerats. Partner får åtkomst till klientprenumerationer via CSP-portalen, oavsett om haveriberedskap hanteras eller självbetjäning. Ställ in arkiv- och registerinfrastrukturen till klientprenumerationerna.
2. Förse klienten med [det konto som du skapade](#create-a-tenant-account).
3. Du kan lägga till en ny användare i klientprenumerationen via CSP-portalen på följande sätt:

    a) Gå till klientens CSP-prenumerationssida och välj sedan alternativet **Användare och licenser.**

      ![Hyresgästens CSP-prenumerationssida](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Skapa nu en ny användare genom att ange relevant information och välja behörigheter, eller genom att ladda upp listan över användare i en CSV-fil.
    
    c) När du har skapat en ny användare går du tillbaka till Azure-portalen. Välj relevant prenumeration på sidan **Prenumeration.**

    d) Välj **Åtkomstkontroll (IAM)** och klicka sedan på **Rolltilldelningar**.

    e) Klicka på **Lägg till rolltilldelning** om du vill lägga till en användare med relevant åtkomstnivå. De användare som har skapats via CSP-portalen visas på fliken Rolltilldelningar.

      ![Lägga till en användare](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- För de flesta hanteringsåtgärder är *rollen Deltagare* tillräcklig. Användare med den här åtkomstnivån kan göra allt på en prenumeration förutom ändringsåtkomstnivåer (för vilka åtkomst på *ägarnivå*krävs).
- Site Recovery har också tre [fördefinierade användarroller](site-recovery-role-based-linked-access-control.md), som kan användas för att ytterligare begränsa åtkomstnivåer efter behov.

## <a name="multi-tenant-environments"></a>Miljöer med flera innehavare

Det finns tre stora modeller för flera innehavare:

* **HSP (Shared Hosting Services Provider)**: Partnern äger den fysiska infrastrukturen och använder delade resurser (vCenter, datacenter, fysisk lagring och så vidare) för att vara värd för flera virtuella klientenheter på samma infrastruktur. Partnern kan tillhandahålla hantering av katastrofåterställning som en hanterad tjänst, eller så kan klienten äga haveriberedskap som en självbetjäningslösning.

* **Dedikerad värdtjänstleverantör:** Partnern äger den fysiska infrastrukturen, men använder dedikerade resurser (flera vCenters, fysiska datalager och så vidare) för att vara värd för varje klients virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla hantering av katastrofåterställning som en hanterad tjänst, eller så kan klienten äga den som en självbetjäningslösning.

* **Managed Services Provider (MSP)**: Kunden äger den fysiska infrastruktur som är värd för de virtuella datorerna och partnern tillhandahåller aktivering och hantering av katastrofåterställning.

Genom att ställa in klientprenumerationer enligt beskrivningen i den här artikeln kan du snabbt börja aktivera kunder i någon av de relevanta modellerna för flera innehavare. Du kan läsa mer om de olika modellerna för flera innehavare och aktivera lokala åtkomstkontroller [här](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [rollbaserad åtkomstkontroll](site-recovery-role-based-linked-access-control.md) för att hantera Azure Site Recovery-distributioner.
- Läs mer om VMware till Azure [replication-arkitektur](vmware-azure-architecture.md).
- [Granska självstudien](vmware-azure-tutorial.md) för att replikera virtuella virtuella datorer med VMware till Azure.
Läs mer om [miljöer med flera innehavare](vmware-azure-multi-tenant-overview.md) för att replikera virtuella virtuella datorer med VMware till Azure.
