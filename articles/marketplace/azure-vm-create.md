---
title: Skapa ett erbjudande för virtuell dator på Azure Marketplace.
description: Lär dig hur du skapar ett erbjudande för virtuella datorer på Microsofts kommersiella marknads plats.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: bfd67688ce30b62002e26cea9f7be4df1cb6e622
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126455"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Så här skapar du ett erbjudande för virtuella datorer på Azure Marketplace

Den här artikeln beskriver hur du skapar ett erbjudande för virtuella Azure-datorer för [Azure Marketplace](https://azuremarketplace.microsoft.com/). Den behandlar både Windows-baserade och Linux-baserade virtuella datorer som innehåller ett operativ system, en virtuell hård disk (VHD) och upp till 16 data diskar.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](partner-center-portal/create-account.md). Se till att ditt konto har registrerats i programmet för kommersiella marknads platser.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har gjort det ännu kan du gå igenom [planera ett erbjudande för virtuella datorer](marketplace-virtual-machines.md). Den förklarar de tekniska kraven för den virtuella datorn och listar den information och de till gångar du behöver när du skapar ditt erbjudande. 

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. I det vänstra fönstret väljer du **kommersiell Marketplace** -  >  **Översikt** .
3. På sidan **Översikt** väljer du **nytt erbjudande** för  >  **Azure virtuell dator** .

    ![Skärm bild som visar meny alternativ för det vänstra fönstret och knappen "nytt erbjudande".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> När ditt erbjudande har publicerats visas alla ändringar som du gör i Partner Center på Azure Marketplace först när du har publicerat om erbjudandet. Se till att alltid publicera om ett erbjudande när du har gjort ändringar i det.

Ange ett **erbjudande-ID** . Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Azure Marketplace-erbjudandet och i Azure PowerShell och Azure CLI, om tillämpligt.
- Använd bara gemena bokstäver och siffror. ID: t kan innehålla bindestreck och under streck, men inte blank steg och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudande-1** , är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa** .

Ange ett **erbjudande alias** . Erbjudandets alias är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Azure Marketplace. Det skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.

## <a name="enable-a-test-drive-optional"></a>Aktivera en testen het (valfritt)

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem till gång till en förkonfigurerad miljö för ett fast antal timmar. Att erbjuda en testen het resulterar i en ökad konverterings takt och genererar hög kvalificerade leads. Mer information om test enheter finns i [Vad är en test enhet?](./what-is-test-drive.md).

> [!TIP]
> En testen het skiljer sig från en kostnads fri utvärderings version. Du kan erbjuda antingen en testad enhet, en kostnads fri utvärderings version eller både och. De förser båda kunderna med din lösning för en fast tids period. Men en testenhet innehåller även en praktisk guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementerings scenario.

**Så här aktiverar du en testen het**
1.  Under **test enhet** markerar du kryss rutan **Aktivera en testenhet** .
1.  Välj typen av test enhet i listan som visas.

## <a name="configure-lead-management"></a>Konfigurera hantering av leads

När du publicerar erbjudandet till den kommersiella marknads platsen med partner Center ansluter du det till ditt CRM-system (Customer Relations hip Management). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten. Att ansluta till en CRM krävs om du vill aktivera en testen het (se föregående avsnitt). Annars är det valfritt att ansluta till en CRM.

1. Välj länken **Anslut** under **kund leads** .
1. I dialog rutan **anslutnings information** väljer du ett lead-mål i listan.
1. Fyll i fälten som visas. Detaljerade anvisningar finns i följande artiklar:

   - [Konfigurera ditt erbjudande för att skicka leads till Azure-tabellen](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 kund engagemang](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (tidigare Dynamics CRM Online)
   - [Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurera ditt erbjudande för att skicka leads till Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurera ditt erbjudande för att skicka leads till Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Om du vill verifiera konfigurationen som du har angett väljer du länken **Verifiera** .
1. Stäng dialog rutan genom att välja **OK** .

## <a name="resell-through-csps"></a>Att sälja via CSP

Utöka räckvidden för ditt erbjudande genom att göra det tillgängligt för partner i program varan [Cloud Solution Provider (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) . Alla BYOL-planer () är automatiskt inloggade i programmet. Du kan också välja att välja icke-BYOL planer.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera egenskaper för erbjudande för virtuell dator](azure-vm-create-properties.md)
- [Metodtips för erbjudandelistor](gtm-offer-listing-best-practices.md)