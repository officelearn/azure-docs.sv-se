---
title: Hantera flera innehavare i Azure Site Recovery med programmet Cloud Solution Providers (CSP) | Microsoft Docs
description: "Beskriver hur du skapar och hanterar klient prenumerationer via CSP och distribuerar Azure Site Recovery i en inställning för flera innehavare"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: manayar
ms.openlocfilehash: a49da33c8038ad467389c66e59727c7e195baf82
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Hantera flera innehavare med Cloud Solution Providers (CSP)-programmet

Den [CSP-programmet](https://partner.microsoft.com/en-US/cloud-solution-provider) bättre tillsammans artiklar för Microsofts molntjänster, inklusive Office 365 Enterprise Mobility Suite och Microsoft Azure kan åstadkomma. Med CSP partners äger slutpunkt till slutpunkt-relation med kunder och blir den primära relation kontaktpunkten. Partners kan distribuera Azure-prenumerationer för kunder och kombinera prenumerationer med sina egna mervärde, anpassade erbjudanden.

Med Azure Site Recovery kan partners hantera den kompletta lösningen för katastrofåterställning för kunder direkt via CSP. Eller CSP kan använda för att ställa in Site Recovery-miljöer och informera kunderna hantera sina egna katastrofåterställning behov på ett sätt för självbetjäning. I båda fallen är partners samverkan mellan Site Recovery och sina kunder. Partners tjänsten customer relationship och debitera kunder för användning i Site Recovery.

Den här artikeln beskrivs hur en partner kan skapa och hantera innehavare prenumerationer via CSP för en VMware-konfiguration med flera innehavare.

## <a name="prerequisites"></a>Krav

- [Förbereda](tutorial-prepare-azure.md) Azure-resurser, inklusive en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
- [Förbereda](tutorial-prepare-on-premises-vmware.md) VMware lokal VMware-servrar och virtuella datorer.
- Skapa en separat hanteringsserver som kan kommunicera med de virtuella klientdatorerna och partnerns vCenter för varje klient. Endast partnern som har behörighet till den här servern. Mer information om olika miljöer med flera klienter finns i den [flera innehavare VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) vägledning.

## <a name="create-a-tenant-account"></a>Skapa ett klient-konto

1. Via [Microsoft Partner Center](https://partnercenter.microsoft.com/), logga in på CSP-konto.

2. På den **instrumentpanelen** väljer du **kunder**.

    ![Länken Microsoft Partner Center-kunder](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. På sidan som öppnas i **Lägg till kunden** knappen.

    ![Knappen Lägg till kund](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. På den **ny kund** sidan, Fyll i informationen kontoinformation för klienten och klicka sedan på **nästa: prenumerationer**.

    ![Sidan kontoinformation](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. På valsidan av prenumerationer väljer du den **Microsoft Azure** kryssrutan. Du kan lägga till andra prenumerationer nu eller vid ett senare tillfälle.

    ![Kryssrutan för Microsoft Azure-prenumeration](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. På den **granska** sidan Bekräfta klient informationen och klicka sedan på **skicka**.

    ![Sidan Granska](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    När du har skapat kontot klienten en bekräftelse visas med information om standardkontot och lösenordet för den prenumerationen.

7. Spara informationen och ändra lösenord senare vid behov via Azure portal-inloggningssidan.  

    Du kan dela information med innehavaren som är eller du kan skapa och dela ett särskilt konto om det behövs.

## <a name="access-the-tenant-account"></a>Få åtkomst till kontot för klient

Du kan använda klientens prenumerationen via Microsoft Partner Center Dashboard som beskrivs i ”steg 1: skapa ett klient-konto”.

1. Gå till den **kunder** , och klickar sedan på namnet på klient-konto.

2. På den **prenumerationer** sidan för klient-konto kan du övervaka de befintliga prenumerationerna för kontot och lägga till flera prenumerationer efter behov. Om du hanterar klientens disaster recovery-åtgärder, välja **alla resurser (Azure portal)**.

    ![Länken alla resurser](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Klicka på **alla resurser** ger dig åtkomst till klientens Azure-prenumerationer. Du kan verifiera åtkomst genom att klicka på Azure Active Directory-länken längst upp i Azure-portalen.

    ![Azure Active Directory-länk](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

Du kan nu utföra alla site recovery-åtgärder för klient via Azure portal och hantera åtgärder för katastrofåterställning. Följ beskrivits tidigare processen för klient-prenumerationen via CSP för hanterade katastrofåterställning.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Distribuera resurser till klientprenumeration
1. Skapa en resursgrupp i Azure-portalen och sedan distribuera Recovery Services-valvet vanliga processer.

2. Ladda ned valvregistreringsnyckeln.

3. Registrera CS för klienten med hjälp av valvregistreringsnyckeln.

4. Ange autentiseringsuppgifterna för de två kontona: vCenter åtkomstkonto och virtuell dator åtkomst till kontot.

    ![Serverkonton med configuration Manager](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Registrera Site Recovery-infrastruktur till Recovery Services-valvet
1. I Azure-portalen på valvet som du skapade tidigare, registrerar vCenter-servern till CS som du har registrerat i ”steg 3: distribuera resurser till klientprenumeration”. Använd vCenter åtkomstkonto för det här ändamålet.
2. Avsluta ”Förbered infrastruktur”-processen för Site Recovery vanliga processer.
3. De virtuella datorerna är nu redo att replikeras. Kontrollera att endast klientorganisationens virtuella datorer som visas på den **Välj virtuella datorer** bladet under den **replikera** alternativet.

    ![Klient VMs lista på bladet välj virtuella datorer](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Tilldela klienten åtkomst till prenumerationen

För självbetjäning katastrofåterställning förse klienten Kontodetaljer som anges i steg 6 i den ”steg 1: skapa ett klient-konto” avsnittet. Utföra denna åtgärd när partnern som har konfigurerat infrastrukturen för katastrofåterställning. Om katastrofåterställning är hanterade eller självbetjäning måste partners åtkomst till klient prenumerationer via CSP-portalen. De konfigurera ägs av partner-valvet och registrera infrastruktur för klient-prenumerationer.

Partners kan också lägga till en ny användare klientprenumeration CSP-portalen genom att göra följande:

1. Gå till prenumerationssidan för klientens CSP och välj sedan den **användare och licenser** alternativet.

    ![Klientens CSP prenumerationssidan](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    Du kan nu skapa en ny användare genom att ange relevant information och välja behörigheter, eller genom att ladda upp en lista över användare i en CSV-fil.

2. När du har skapat en ny användare gå tillbaka till Azure portal och klickar sedan på den **prenumeration** bladet väljer du den relevanta prenumerationen.

3. På bladet som öppnas väljer **Access Control (IAM)**, och klicka sedan på **Lägg till** att lägga till en användare med relevanta åtkomstnivå.      
    Användare som har skapats via portalen CSP visas automatiskt i bladet som öppnas när du klickar på åtkomstnivå.

    ![Lägga till en användare](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    För de flesta hanteringsåtgärder på *deltagare* roll är tillräckliga. Användare med den här åtkomstnivån kan göra allt på en prenumeration förutom ändra åtkomstnivåerna (som *ägare*-åtkomst krävs).

  Azure Site Recovery har tre [fördefinierad användarroller](site-recovery-role-based-linked-access-control.md) som kan användas för att ytterligare begränsa åtkomstnivåer som krävs.

## <a name="next-steps"></a>Nästa steg
  [Lär dig mer](site-recovery-role-based-linked-access-control.md) om rollbaserad åtkomstkontroll hantera distributioner av Azure Site Recovery.

  [Hantera VMware-miljöer med flera innehavare](site-recovery-multi-tenant-support-vmware-using-csp.md)
