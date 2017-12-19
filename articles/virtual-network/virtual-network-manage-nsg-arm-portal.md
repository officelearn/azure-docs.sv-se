---
title: "Hantera NSG: er med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig mer om att hantera befintliga NSG: er med hjälp av Azure portal."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="manage-nsgs-using-the-portal"></a>Hantera NSG: er med hjälp av portalen

> [!div class="op_single_selector"]
> * [Portal](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Azure CLI](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln täcker distributionsmodell hanteraren för filserverresurser, som Microsoft rekommenderar för de flesta nya distributioner i stället för den klassiska distributionsmodellen.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Hämta Information
Du kan visa dina befintliga NSG: er, hämta regler för en befintlig NSG och ta reda på vilka resurser en NSG är kopplad till.

### <a name="view-existing-nsgs"></a>Visa befintliga NSG: er

Om du vill visa alla befintliga NSG: er i en prenumeration gör du följande:

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.

2. Klicka på **Bläddra >** > **Nätverkssäkerhetsgrupper**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Kontrollera listan över NSG: er i den **Nätverkssäkerhetsgrupper** bladet.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Visa NSG: er i en resursgrupp

Visa lista med NSG: er i den **RG NSG** resursen och gör du följande:

1. Klicka på **resursgrupper >** > **RG NSG** > **...** .

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. I listan över resurser, leta efter objekt visa NSG-ikon som visas i den **resurser** bladet nedan.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>Visa en lista med alla regler för en NSG

Visa regler för en NSG som heter **NSG-klientdel**, gör du följande:

1. Från den **Nätverkssäkerhetsgrupper** bladet eller **resurser** bladet som visas ovan, klickar du på **NSG-klientdel**.

2. I den **inställningar** klickar du på **inkommande säkerhetsregler**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. Den **inkommande säkerhetsregler** bladet visas enligt nedan.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. I den **inställningar** klickar du på **utgående säkerhetsregler** att se utgående regler.

    > [!NOTE]
    > Om du vill visa standardregler klickar du på den **standard regler** längst upp på bladet som visar reglerna.
    >

### <a name="view-nsgs-associations"></a>Visa NSG: er associationer

Visa vilka resurser de **NSG-klientdel** NSG är associerat med, gör du följande:

1. Från den **Nätverkssäkerhetsgrupper** bladet eller **resurser** bladet som visas ovan, klickar du på **NSG-klientdel**.

2. I den **inställningar** klickar du på **undernät** att visa vilka undernät som är kopplade till NSG: N.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. I den **inställningar** klickar du på **nätverksgränssnitt** att visa vilka nätverkskort är kopplad till NSG: N.

## <a name="manage-rules"></a>Hantera regler
Du kan lägga till regler i en befintlig NSG, redigera befintliga regler och ta bort regler.

### <a name="add-a-rule"></a>Lägg till en regel
Att lägga till en regel som tillåter **inkommande** trafik till port **443** från en dator till den **NSG-klientdel** NSG, gör du följande:

1. Från den **Nätverkssäkerhetsgrupper** bladet eller **resurser** bladet som visas ovan, klickar du på **NSG-klientdel**.
2. I den **inställningar** klickar du på **inkommande säkerhetsregler**.
3. I den **inkommande säkerhetsregler** bladet, klickar du på **Lägg till**. I den **Lägg till ingående säkerhetsregel** bladet fyller värdena som visas nedan och klicka sedan på **OK**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Efter några sekunder, Lägg märke till den nya regeln i den **inkommande säkerhetsregler** bladet.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Ändra en regel
Så här ändrar du regeln som skapade ovan för att tillåta inkommande trafik från den **Internet** , gör du följande:

1. Från den **Nätverkssäkerhetsgrupper** bladet eller **resurser** bladet som visas ovan, klickar du på **NSG-klientdel**.
2. I den **inställningar** klickar du på regeln som skapade ovan.
3. I den **Tillåt https** bladet ändra den **källa** egenskapen enligt nedan och klickar sedan på **spara**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Ta bort en regel

Utför följande steg om du vill ta bort regeln som skapade ovan:

1. Från den **Nätverkssäkerhetsgrupper** bladet eller **resurser** bladet som visas ovan, klickar du på **NSG-klientdel**.
2. I den **inställningar** klickar du på regeln som skapade ovan.
3. I den **Tillåt https** bladet, klickar du på **ta bort**, och klicka sedan på **Ja**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Hantera kopplingar
Du kan koppla en NSG till undernät och nätverkskort. Du kan också koppla bort en NSG från alla resurser som den är kopplad till.

### <a name="associate-an-nsg-to-a-nic"></a>Koppla en NSG till ett nätverkskort
Associera den **NSG-klientdel** NSG till den **TestNICWeb1** NIC, gör du följande:

1. Från den **Nätverkssäkerhetsgrupper** bladet eller **resurser** bladet som visas ovan, klickar du på **NSG-klientdel**.
2. I den **inställningar** klickar du på **nätverksgränssnitt** > **associera** > **TestNICWeb1**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Koppla bort en NSG från ett nätverkskort

Koppla bort den **NSG-klientdel** NSG från den **TestNICWeb1** NIC, gör du följande:

1. Azure-portalen klickar du på **resursgrupper >** > **RG NSG** > **...**   >  **TestNICWeb1**.

2. I den **TestNICWeb1** bladet, klickar du på **ändra säkerhet...**   >  **Ingen**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> Du kan också använda det här bladet för att associera nätverkskortet till en befintlig NSG.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Koppla bort en NSG från ett undernät

Koppla bort den **NSG-klientdel** NSG från den **klientdel** undernät, gör du följande:

1. Azure-portalen klickar du på **resursgrupper >** > **RG NSG** > **...**   >  **TestVNet**.

2. I den **inställningar** bladet, klickar du på **undernät** > **klientdel** > **nätverkssäkerhetsgruppen** > **ingen**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. I den **klientdel** bladet, klickar du på **spara**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Koppla en NSG till ett undernät

Associera den **NSG-klientdel** NSG till den **FronEnd** undernät igen, gör du följande:

1. Azure-portalen klickar du på **resursgrupper >** > **RG NSG** > **...**   >  **TestVNet**.
2. I den **inställningar** bladet, klickar du på **undernät** > **klientdel** > **nätverkssäkerhetsgruppen** > **NSG-klientdel**.
3. I den **klientdel** bladet, klickar du på **spara**.

> [!NOTE]
> Du kan också koppla en NSG till ett undernät från thh NSG **inställningar** bladet.
>

## <a name="delete-an-nsg"></a>Ta bort en NSG
Du kan bara ta bort en NSG om den inte är kopplad till en resurs. Utför följande steg för att ta bort en NSG:.

1. Azure-portalen klickar du på **resursgrupper >** > **RG NSG** > **...**   >  **NSG-klientdel**.
2. I den **inställningar** bladet, klickar du på **nätverksgränssnitt**.
3. Om det finns några nätverkskort i listan, klicka på nätverkskortet och följ steg 2 i [koppla bort en NSG från ett nätverkskort](#Dissociate-an-NSG-from-a-NIC).
4. Upprepa steg 3 för varje nätverkskort.
5. I den **inställningar** bladet, klickar du på **undernät**.
6. Om det finns inga undernät i listan, klicka på undernätet och följ steg 2 och 3 i [koppla bort en NSG från ett undernät](#Dissociate-an-NSG-from-a-subnet).
7. Rullar vänster till den **NSG-klientdel** bladet Klicka **ta bort** > **Ja**.

    ![Azure portal – NSG: er](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Nästa steg
* [Aktivera loggning](virtual-network-nsg-manage-log.md) för NSG: er.
