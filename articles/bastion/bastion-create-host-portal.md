---
title: Skapa en Azure-Skyddsmiljö-värd | Microsoft Docs
description: I den här artikeln lär du dig hur du skapar en Azure-Skyddsmiljö-värd
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4a52383e6ab24c6ae1e2be0b67293d65dfa04466
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477887"
---
# <a name="create-an-azure-bastion-host-preview"></a>Skapa en Azure-Skyddsmiljö-värd (förhandsversion)

Den här artikeln visar hur du skapar en Azure-Skyddsmiljö-värd. När du etablerar tjänsten Azure Skyddsmiljö i det virtuella nätverket kan är smidigt i RDP/SSH tillgänglig för alla dina virtuella datorer i samma virtuella nätverk. Distributionen görs per nätverk, inte per prenumeration/konto eller virtuell dator.

Det finns två sätt som du kan skapa en resurs för Skyddsmiljö-värd:

* Skapa en Skyddsmiljö-resurs med hjälp av Azure portal.
* Skapa en Skyddsmiljö-resurs i Azure-portalen med hjälp av inställningarna för befintliga virtuella datorer.

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Den offentliga förhandsversionen är begränsad till följande Azure-offentliga regioner:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Skapa en skyddsmiljö-värd

Det här avsnittet hjälper dig att skapa en ny Azure Skyddsmiljö-resurs från Azure-portalen.

1. På startsidan i den [Azure-portalen – förhandsversion av Skyddsmiljö](https://aka.ms/BastionHost), klickar du på **+ skapa en resurs**. Kontrollera att du använder länken för att få åtkomst till portalen för den här förhandsversionen kan inte vanliga Azure-portalen.

1. På den **New** sidan den *Sök på Marketplace* skriver **Skyddsmiljö**, klicka sedan på **RETUR** till sökresultaten.

1. Från resultatet klickar du på **Skyddsmiljö (förhandsversion)** . Kontrollera att utgivaren är *Microsoft* och kategorin är *nätverk*.

1. På den **Skyddsmiljö (förhandsversion)** klickar du på **skapa** att öppna den **skapa en skyddsmiljö** sidan.

1. På den **skapa en skyddsmiljö** konfigurerar du en ny resurs i Skyddsmiljön. Ange konfigurationsinställningar för Skyddsmiljön.

    ![Skapa en skyddsmiljö](./media/bastion-create-host-portal/settings.png)

    * **Prenumeration**: Den Azure-prenumeration som du vill använda för att skapa en ny resurs i Skyddsmiljön.
    * **Resursgrupp**: Azure-resursgruppen där den nya Skyddsmiljö-resursen skapas i. Om du inte har en befintlig resursgrupp, kan du skapa en ny.
    * **Namn på**: Namnet på den nya skyddade resursen
    * **Region**: Azure offentlig-regionen som resursen ska skapas i.
    * **Virtuellt nätverk**: Det virtuella nätverket där den skyddade resursen skapas i. Du kan skapa ett nytt virtuellt nätverk i portalen under den här processen om du inte har eller inte vill använda ett befintligt virtuellt nätverk. Om du använder ett befintligt virtuellt nätverk kan du kontrollera att det befintliga virtuella nätverket har tillräckligt utrymme för kostnadsfri-adress för Skyddsmiljö undernät kraven.
    * **Undernät**: Undernät i ditt virtuella nätverk som den nya Skyddsmiljö-värdresursen ska distribueras. Du måste skapa ett undernät med namnvärdet **AzureBastionSubnet**. Det här värdet kan Azure vet vilka undernätet för att distribuera Skyddsmiljö resurserna till. Detta skiljer sig från ett Gateway-undernät. Vi rekommenderar starkt att du använder minst en/27 eller större undernät (/ 27, / 26, och så vidare). Skapa den **AzureBastionSubnet** utan någon Nätverkssäkerhetsgrupper dirigera tabeller eller delegering.
    * **Offentlig IP-adress**: Den offentliga IP-Adressen för den skyddade resursen som RDP/SSH används (via port 443). Skapa en ny offentlig IP-adress eller Använd en befintlig. Den offentliga IP-adressen måste vara i samma region som den skyddade resursen som du skapar.
    * **Namn på offentlig IP-adress**: Namnet på den offentliga IP-adressresursen.
    * **Offentlig IP-adress SKU**: Innehåller som standard att **Standard**.
    * **Tilldelning av**: Innehåller som standard att **Statiska**.

1. När du är klar med att ange inställningarna klickar du på **granska + skapa**. Detta verifierar värdena. När valideringen är godkänd, du kan påbörja skapandeprocessen.
1. På sidan Skapa en skyddsmiljö, klickar du på **skapa**.
1. Du ser ett meddelande som informerar dig om att distributionen pågår. Status visas på den här sidan när resurserna som skapas. Det tar cirka 5 minuter för den skyddade resursen skapas och distribueras.

## <a name="createvmset"></a>Skapa en skyddsmiljö-värd med hjälp av inställningarna för virtuella datorer

Om du skapar en skyddsmiljö-värd i portalen genom att använda en befintlig virtuell dator, kommer olika inställningar automatiskt som standard för din virtuella dator och/eller virtuellt nätverk.

1. I den [Azure-portalen – förhandsversion av Skyddsmiljö](https://aka.ms/BastionHost), navigera till den virtuella datorn och sedan på **Connect**.

    ![VM-anslutning](./media/bastion-create-host-portal/vmsettings.png)

1. Klicka på rätt sidopanelen **Skyddsmiljö**, sedan **Använd Skyddsmiljö**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. På sidan Skyddsmiljö fyller du i fälten för följande inställningar:

    * **Namn på**: Namnet på skyddsmiljö-värd som du vill skapa.
    * **Undernät**: Undernät i ditt virtuella nätverk till vilka Skyddsmiljö resurs ska distribueras. Undernätet måste skapas med namnet **AzureBastionSubnet**. Det gör att Azure vet vilka undernätet för att distribuera Skyddsmiljö resursen. Detta skiljer sig från ett Gateway-undernät. Klicka på **hantera undernätskonfiguration** att skapa Azure Skyddsmiljö undernätet. Vi rekommenderar starkt att du använder minst en/27 eller större undernät (/ 27, / 26, etc.). Skapa den **AzureBastionSubnet** utan någon Nätverkssäkerhetsgrupper dirigera tabeller eller delegering. Klicka på **skapa** för att skapa undernätet, fortsätter sedan med nästa inställningar.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Offentlig IP-adress**: Den offentliga IP-Adressen för den skyddade resursen som RDP/SSH används (via port 443). Skapa en ny offentlig IP-adress eller Använd en befintlig. Den offentliga IP-adressen måste vara i samma region som den skyddade resursen som du skapar.
    * **Namn på offentlig IP-adress**: Namnet på den offentliga IP-adressresursen.
1. Klicka på skärmen för verifiering **skapa**. Vänta tills cirka 5 minuter för den skyddade resursen skapas och distribueras.

## <a name="next-steps"></a>Nästa steg

Läs den [Skyddsmiljö vanliga frågor och svar](bastion-faq.md)
