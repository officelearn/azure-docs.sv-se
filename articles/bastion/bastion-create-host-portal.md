---
title: Skapa en Azure skydds-värd | Microsoft Docs
description: I den här artikeln lär du dig hur du skapar en Azure skydds-värd
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: 7838e1930a8ccd190c95740a6a8e3dadc68149f0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422127"
---
# <a name="create-an-azure-bastion-host"></a>Skapa en Azure skydds-värd

Den här artikeln visar hur du skapar en Azure skydds-värd. När du etablerar Azure skydds-tjänsten i det virtuella nätverket är den sömlösa RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Distributionen görs per nätverk, inte per prenumeration/konto eller virtuell dator.

Du kan skapa en skydds-värd resurs på två sätt:

* Skapa en skydds-resurs med hjälp av Azure Portal.
* Skapa en skydds-resurs i Azure Portal med hjälp av befintliga VM-inställningar.

## <a name="before-you-begin"></a>Innan du börjar

Skydds finns i följande offentliga Azure-regioner:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Skapa en skydds-värd

Det här avsnittet hjälper dig att skapa en ny Azure skydds-resurs från Azure Portal.

1. På [Azure Portal](https://portal.azure.com) -menyn eller på **Start** sidan väljer du **skapa en resurs**.

1. På sidan **ny** i fältet *Sök på Marketplace skriver du* **skydds**och klickar sedan på **RETUR** för att komma till Sök resultaten.

1. Klicka på **skydds**i resultatet. Kontrol lera att utgivaren är *Microsoft* och att kategorin är *nätverk*.

1. På sidan **skydds** klickar du på **skapa** för att öppna sidan **skapa en skydds** .

1. På sidan **skapa en skydds** konfigurerar du en ny skydds-resurs. Ange konfigurations inställningarna för skydds-resursen.

    ![skapa en skydds](./media/bastion-create-host-portal/settings.png)

    * **Prenumeration**: den Azure-prenumeration som du vill använda för att skapa en ny skydds-resurs.
    * **Resurs grupp**: Azure-resurs gruppen där den nya skydds-resursen ska skapas i. Om du inte har en befintlig resurs grupp kan du skapa en ny.
    * **Namn**: namnet på den nya skydds-resursen
    * **Region**: den offentliga Azure-region som resursen ska skapas i.
    * **Virtuellt nätverk**: det virtuella nätverk där skydds-resursen ska skapas i. Du kan skapa ett nytt virtuellt nätverk i portalen under den här processen, om du inte har eller inte vill använda ett befintligt virtuellt nätverk. Om du använder ett befintligt virtuellt nätverk kontrollerar du att det befintliga virtuella nätverket har tillräckligt med ledigt adress utrymme för att uppfylla skydds-undernätets krav.
    * **Undernät**: under nätet i det virtuella nätverket som den nya skydds-värd resursen ska distribueras till. Du måste skapa ett undernät med namnet Value **AzureBastionSubnet**. Med det här värdet kan Azure veta vilket undernät som skydds-resurserna ska distribueras till. Detta skiljer sig från ett Gateway-undernät. Du måste använda ett undernät med minst ett/27 eller större undernät (/27,/26 osv.). Skapa **AzureBastionSubnet** utan några routningstabeller eller delegeringar. När du använder nätverks säkerhets grupper på **AzureBastionSubnet**, se [arbeta med NSG: er](bastion-nsg.md).
    * **Offentlig IP-adress**: den offentliga IP-adressen för den skydds-resurs som RDP/SSH kommer att få åtkomst till (via port 443). Skapa en ny offentlig IP-adress eller Använd en befintlig. Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar.
    * **Namn på offentlig IP-adress**: namnet på den offentliga IP-adressresursen.
    * **SKU för offentlig IP-adress**: förifyllt som standard till **standard**. Azure skydds använder/stöder bara standard-offentliga IP SKU.
    * **Tilldelning**: förifyllt som standard till **statisk**.

1. När du är klar med att ange inställningarna klickar du på **Granska + skapa**. Detta validerar värdena. När verifieringen är klar kan du börja skapa processen.
1. På sidan Skapa en skydds klickar du på **skapa**.
1. Ett meddelande visas där du vet att distributionen pågår. Statusen visas på den här sidan när resurserna skapas. Det tar ungefär 5 minuter för skydds-resursen att skapas och distribueras.

## <a name="createvmset"></a>Skapa en skydds-värd med VM-inställningar

Om du skapar en skydds-värd i portalen genom att använda en befintlig virtuell dator, kommer olika inställningar att automatiskt motsvara den virtuella datorn och/eller det virtuella nätverket.

1. Öppna [Azure-portalen](https://portal.azure.com). Gå till den virtuella datorn och klicka sedan på **Anslut**.

   ![Anslut till virtuell dator](./media/bastion-create-host-portal/vmsettings.png)
1. Klicka på **skydds**på höger marginal list och **Använd sedan skydds**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. På sidan skydds fyller du i följande inställnings fält:

   * **Namn**: namnet på den skydds-värd som du vill skapa.
   * **Undernät**: under nätet i det virtuella nätverket som skydds-resursen ska distribueras till. Under nätet måste skapas med namnet **AzureBastionSubnet**. På så sätt kan Azure veta vilket undernät som ska användas för att distribuera skydds-resursen. Detta skiljer sig från ett Gateway-undernät. Klicka på **Hantera under näts konfiguration** för att skapa Azure skydds-undernätet. Vi rekommenderar starkt att du använder minst ett/27 eller större undernät (/27,/26 osv.). Skapa **AzureBastionSubnet** utan några nätverks säkerhets grupper, routningstabeller eller delegeringar. Klicka på **skapa** för att skapa under nätet och fortsätt sedan med nästa inställningar.
   * **Offentlig IP-adress**: den offentliga IP-adressen för den skydds-resurs som RDP/SSH kommer att få åtkomst till (via port 443). Skapa en ny offentlig IP-adress eller Använd en befintlig. Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar.
   * **Namn på offentlig IP-adress**: namnet på den offentliga IP-adressresursen.
1. På validerings skärmen klickar du på **skapa**. Vänta tills 5 minuter för att skydds-resursen ska skapas och distribueras.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md)