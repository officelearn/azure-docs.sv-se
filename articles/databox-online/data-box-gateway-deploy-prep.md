---
title: Självstudie om hur du förbereder Azure-portalen för att distribuera Data Box Gateway | Microsoft Docs
description: Den första självstudien om hur du distribuerar Azure Data Box Gateway handlar om hur du förbereder Azure-portalen.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
ms.openlocfilehash: 34bc4d7cbdbb89cd9ff3f334ca32087c474735b7
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620095"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Självstudier: Förbereda för distribution av Azure Data Box-Gateway


Det här är den första självstudien i serien med distributionssjälvstudier som krävs för en fullständig Azure Data Box Gateway-distribution. Den här självstudien beskriver hur du förbereder Azure-portalen för att distribuera Data Box Gateway-resursen. 

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Ladda ned avbildningen av den virtuella enheten
> * Hämta aktiveringsnyckeln

## <a name="get-started"></a>Kom igång

Gå igenom följande självstudier i angiven ordning när du ska distribuera Data Box Gateway.

| **#** | **I det här steget** | **Använd de här dokumenten** |
| --- | --- | --- | 
| 1. |**[Förbereda Azure-portalen för Data Box Gateway](data-box-gateway-deploy-prep.md)** |Skapa och konfigurera Data Box Gateway-resursen innan du etablerar en virtuell Data Box Gateway-enhet. |
| 2. |**[Etablera Data Box Gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Etablera Data Box Gateway i VMware](data-box-gateway-deploy-provision-vmware.md)**|För Hyper-V etablerar du och ansluter till en virtuell Data Box Gateway-enhet i ett värdsystem som kör Hyper-V i Windows Server 2016 eller Windows Server 2012 R2. <br><br><br> För VMware etablerar du och ansluter till en virtuell Data Box Gateway-enhet i ett värdsystem som kör VMware ESXi 6.0, 6.5 eller 6.7.<br></br> |
| 3. |**[Ansluta, konfigurera och aktivera Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Anslut till det lokala webbgränssnittet, slutför installationen av enheten och aktivera enheten. Sedan kan du etablera SMB-resurser.  |
| 4. |**[Överföra data med Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Lägg till resurser och anslut till resurser via SMB eller NFS. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Här hittar du konfigurationskraven för Data Box Gateway-resursen, Data Box Gateway-enheten och datacenternätverket.

### <a name="for-the-data-box-gateway-resource"></a>För Data Box Gateway-resursen

Innan du börjar ska du kontrollera att:

- Microsoft Azure-prenumerationen ska ha stöd för Data Box gatewayresursen. Prenumerationer med användningsbaserad betalning stöds inte.
- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.

### <a name="for-the-data-box-gateway-device"></a>För Data Box Gateway-enheten

Innan du distribuerar en virtuell enhet kontrollerar du att:

- Du har åtkomst till ett värdsystem som kör Hyper-V i Windows Server 2012 R2 eller senare eller VMware (ESXi 6.0, 6.5 eller 6.7) som kan användas för att etablera en enhet.
- Värdsystemet kan dedikera följande resurser för att etablera den virtuella Data Box-enheten:
  
  - Minst 4 virtuella processorer.
  - Minst 8 GB RAM.
  - Ett nätverksgränssnitt.
  - En operativsystemdisk på 250 GB.
  - En virtuell disk på 2 TB för systemdata.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

- Nätverket i datacentret konfigureras enligt nätverkskraven för din Data Box Gateway-enhet. Mer information finns i den [systemkrav för Data Box Gateway](data-box-gateway-system-requirements.md).

- Under normala driftsförhållanden av din Data Box-Gateway, bör du har en:

    - Minst 10 Mbit/s hämta bandbredd för att se till att enheten är uppdaterad.
    - Minst 20 Mbit/s dedikerade ladda upp och hämta bandbredd för att överföra filer.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

Om du har en befintlig Data Box Gateway-resurs för hantering av virtuella enheter hoppar du över det här steget och går till [Hämta aktiveringsnyckeln](#get-the-activation-key).

Om du vill skapa en resurs för Data Box-gatewayen, gör du följande i Azure-portalen.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på:

    - Azure-portalen på den här URL: [ https://portal.azure.com ](http://portal.azure.com).
    - Eller, Azure Government-portalen på den här URL: [ https://portal.azure.us ](https://portal.azure.us). Mer information går du till [Anslut till Azure Government, med hjälp av portalen](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. I den vänstra rutan väljer **+ skapa en resurs**. Sök efter **Data Box Edge / Data Box Gateway**. Välj Data Box Edge / Data Box Gateway. Välj **Skapa**.
3. Välj den prenumeration som du vill använda för Data Box-Gateway-enhet. Välj den region där du vill distribuera Data Box Gateway-resursen. Den här versionen finns östra USA, Sydostasien och Västeuropa. Välj den plats som är närmast den geografiska region där du vill distribuera enheten. I den **Data Box Gateway** väljer **skapa**.

    ![Leta upp Data Box Gateway-tjänsten](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. På den **grunderna** anger eller väljer du följande **projektet information**.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration    |Det fylls i automatiskt baserat på den tidigare val. Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Läs mer om [Azure-resursgrupper](../azure-resource-manager/resource-group-overview.md).     |

5. Ange eller Välj följande **instans information**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken som består av bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |   
    |Region     |Den här versionen är är östra USA, Sydostasien och Västeuropa tillgängliga för att distribuera din resurs. För myndigheter i Azure government-regioner som anges i den [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/) är tillgängliga. <br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|
    
    ![Skapa Data Box Gateway-resursen](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Välj **Granska + skapa**.
 
7. På den **granska + skapa** fliken kan du granska den **prisinformation**, **användningsvillkoren**, och information för din resurs. Välj **Skapa**.

    ![Granska information om Data Box Gateway-resursen](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Det tar några minuter att skapa resursen. När resursen har skapas och distribueras, meddelas du. Välj **Gå till resurs**.

![Granska information om Data Box Gateway-resursen](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Ladda ned avbildningen av den virtuella enheten

När Data Box Gateway-resursen har skapats laddar du ned lämplig avbildning av den virtuella enheten för att etablera en virtuell enhet i värdsystemet. Virtuell enhet-avbildningar är specifika för ett operativsystem.

> [!IMPORTANT]
> Programvaran som körs på Data Box Gateway kan bara användas med Data Box Gateway-resursen.

Följ de här stegen i den [Azure-portalen](https://portal.azure.com/) att hämta en avbildning av virtuell enhet.

1. I den resurs som du skapat och välj sedan **översikt**. Om du har en befintlig resurs i Azure Data Box-Gateway, markera resursen och gå till **översikt**. Välj **Enhetsinställningar**.

    ![Ny Data Box Gateway-resurs](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. På den **Download bild** panelen, väljer du den virtuella enhet-avbildning som motsvarar operativsystemet på värdservern som används för att etablera den virtuella datorn. Bildfilerna är ungefär 5,6 GB.
   
   * [VHDX för Hyper-V i Windows Server 2012 R2 och senare](https://aka.ms/dbe-vhdx-2012).
   * [VMDK för VMWare ESXi 6.0, 6.5 eller 6.7](https://aka.ms/dbe-vmdk).

    ![Ladda ned avbildningen för Data Box-Gateway-enhet](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Ladda ned och packa upp filen till en lokal enhet och skriv ned sökvägen till de uppackade filerna.


## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Data Box Gateway-resursen är igång kan behöver du hämta aktiveringsnyckel för. Den här nyckeln används för att aktivera och ansluta Data Box Gateway-enheten med resursen. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Välj den resurs som du skapade och välj sedan **Översikt**. I den **Enhetsinställningar**går du till den **konfigurera och aktivera** panelen.

    ![Konfigurera och aktivera panel](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Välj **Generera nyckel** för att skapa en aktiveringsnyckel. Välj kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

    ![Hämta aktiveringsnyckeln](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Aktiveringsnyckeln upphör att gälla tre dagar efter att den skapats.
> - Om nyckeln har upphört att gälla genererar du en ny nyckel. Den äldre nyckeln är inte giltig.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Gateway-ämnen som att:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Ladda ned avbildningen av den virtuella enheten
> * Hämta aktiveringsnyckeln

Gå vidare till nästa självstudie och lär dig hur du etablerar en virtuell dator för Data Box Gateway. Läs de detaljerade instruktionerna i följande avsnitt, beroende på ditt värdoperativsystem:

> [!div class="nextstepaction"]
> [Etablera en Data Box Gateway i Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

ELLER

> [!div class="nextstepaction"]
> [Etablera en Data Box Gateway i VMware](./data-box-gateway-deploy-provision-vmware.md)


