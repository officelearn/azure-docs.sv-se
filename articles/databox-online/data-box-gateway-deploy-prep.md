---
title: Självstudie om hur du förbereder Azure-portalen för att distribuera Data Box Gateway | Microsoft Docs
description: Den första självstudien om hur du distribuerar Azure Data Box Gateway handlar om hur du förbereder Azure-portalen.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: f4c3cf5329c10cda3691370e946b8397662a8d66
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953420"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>Självstudier: Förbereda distributionen av Azure Data Box Gateway (förhandsversion)


Det här är den första självstudien i serien med distributionssjälvstudier som krävs för en fullständig Azure Data Box Gateway-distribution. Den här självstudien beskriver hur du förbereder Azure-portalen för att distribuera Data Box Gateway-resursen. 

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Portalförberedelserna tar mindre än tio minuter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny resurs
> * Ladda ned avbildningen av den virtuella enheten
> * Hämta aktiveringsnyckeln

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


> [!IMPORTANT]
> - Data Box Gateway är en förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen. 

### <a name="get-started"></a>Kom igång

Gå igenom följande självstudier i angiven ordning när du ska distribuera Data Box Gateway.

| **#** | **I det här steget** | **Använd de här dokumenten** |
| --- | --- | --- | 
| 1. |**[Förbereda Azure-portalen för Data Box Gateway](data-box-gateway-deploy-prep.md)** |Skapa och konfigurera Data Box Gateway-resursen innan du etablerar en virtuell Data Box Gateway-enhet. |
| 2. |**[Etablera Data Box Gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Etablera Data Box Gateway i VMware](data-box-gateway-deploy-provision-vmware.md)**|För Hyper-V etablerar du och ansluter till en virtuell Data Box Gateway-enhet i ett värdsystem som kör Hyper-V i Windows Server 2016 eller Windows Server 2012 R2. <br><br><br> För VMware etablerar du och ansluter till en virtuell Data Box Gateway-enhet i ett värdsystem som kör VMware ESXi 6.0 eller 6.5.<br></br> |
| 3. |**[Ansluta, konfigurera och aktivera Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Anslut till det lokala webbgränssnittet, slutför installationen av enheten och aktivera enheten. Sedan kan du etablera SMB-resurser.  |
| 4. |**[Överföra data med Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Lägg till resurser och anslut till resurser via SMB eller NFS. |

Nu kan du börja konfigurera Azure-portalen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Här hittar du konfigurationskraven för Data Box Gateway-resursen, Data Box Gateway-enheten och datacenternätverket.

### <a name="for-the-data-box-gateway-resource"></a>För Data Box Gateway-resursen

Innan du börjar ska du kontrollera att:

* Din Microsoft Azure-prenumeration måste vara aktiverad för Data Box Gateway-resursen.
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.

### <a name="for-the-data-box-gateway-device"></a>För Data Box Gateway-enheten

Innan du distribuerar en virtuell enhet kontrollerar du att:

* Du har åtkomst till ett värdsystem som kör Hyper-V i Windows Server 2012 R2 eller senare eller VMware (ESXi 6.0 eller 6.5) som kan användas för att etablera en enhet.
* Värdsystemet kan dedikera följande resurser för att etablera den virtuella Data Box-enheten:
  
  * Minst 4 kärnor.
  * Minst 8 GB RAM-minne. 
  * Ett nätverksgränssnitt.
  * En operativsystemdisk på 250 GB.
  * En virtuell disk på 2 TB för systemdata.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

* Nätverket i datacentret konfigureras enligt nätverkskraven för din Data Box Gateway-enhet. Mer information finns i avsnittet med [systemkrav för Data Box Gateway](data-box-gateway-system-requirements.md).

* Din Data Box Gateway har en dedikerad Internetbandbredd på minst 20 Mbit/s som alltid är tillgänglig. Den här bandbredden ska inte delas med andra program. Om du använder nätverksbegränsning rekommenderar vi att du använder en Internetbandbredd på 32 Mbit/s eller mer för att begränsningen ska fungera.

## <a name="create-a-new-resource"></a>Skapa en ny resurs

En enda instans av Data Box Gateway-resursen kan hantera flera virtuella Data Box Gateway-enheter. Utför följande steg för att skapa en ny Data Box Gateway-resurs. 

Om du har en befintlig Data Box Gateway-resurs för hantering av virtuella enheter hoppar du över det här steget och går till [Hämta aktiveringsnyckeln](#get-the-activation-key).

Utför följande steg på Azure-portalen för att skapa en Data Box-resurs.
1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på Azure-förhandsgranskningsportalen på följande URL: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Välj den prenumeration som du vill använda för förhandsversionen av Data Box Edge. Välj den region där du vill distribuera Data Box Edge-resursen. Klicka på **Skapa** för alternativet **Data Box Gateway**.

    ![Leta upp Data Box Gateway-tjänsten](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. Ange eller välj följande information för den nya resursen.
    
    |Inställning  |Värde  |
    |---------|---------|
    |Resursnamn   | Ett eget namn som identifierar resursen.<br>Namnet innehåller mellan 2 och 50 tecken som består av bokstäver, siffror och bindestreck.<br> Namnet börjar och slutar med en bokstav eller en siffra.        |
    |Prenumeration    |Prenumerationen är kopplad till ditt faktureringskonto. |
    |Resursgrupp  |Välj en befintlig grupp eller skapa en ny grupp.<br>Läs mer om [Azure-resursgrupper](../azure-resource-manager/resource-group-overview.md).     |
    |Plats     |För den här versionen är följande regioner tillgängliga: USA, östra; USA 2, västra; Asien, sydöstra; och Europa, västra. <br> Välj den plats som är närmast den geografiska region där du vill distribuera enheten.|
    
    ![Skapa Data Box Gateway-resursen](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Klicka på **OK**.
 
Det tar några minuter att skapa resursen. När resursen har skapats får du ett meddelande.


## <a name="download-the-virtual-device-image"></a>Ladda ned avbildningen av den virtuella enheten

När Data Box Gateway-resursen har skapats laddar du ned lämplig avbildning av den virtuella enheten för att etablera en virtuell enhet i värdsystemet. Avbildningar av virtuella enheter är operativsystemspecifika och kan laddas ned från bladet **Snabbstart** för din resurs på Azure-portalen.

> [!IMPORTANT]
> Programvaran som körs på Data Box Gateway kan bara användas med Data Box Gateway-resursen.


Utför följande steg på [Azure-portalen](https://portal.azure.com/).

1. Klicka på den resurs som du skapade och klicka sedan på **Översikt**. Om du har en befintlig Azure Data Box Gateway-resurs klickar du på resursen och går till **Översikt**.

    ![Ny Data Box Gateway-resurs](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. I snabbstarten i den högra rutan klickar du på länken för den avbildning som du vill hämta. Avbildningsfilerna är cirka 4,8 GB.
   
   * [VHDX för Hyper-V i Windows Server 2012 R2 och senare](https://aka.ms/dbe-vhdx-2012).
   * [VMDK för VMWare ESXi 6.0 eller 6.5](https://aka.ms/dbe-vmdk).

5. Ladda ned och packa upp filen till en lokal enhet och skriv ned sökvägen till de uppackade filerna.


## <a name="get-the-activation-key"></a>Hämta aktiveringsnyckeln

När Data Box Gateway-resursen är igång och körs måste du hämta aktiveringsnyckeln. Den här nyckeln används för att aktivera och ansluta Data Box Gateway-enheten med resursen.

Aktiveringsnyckeln används för att registrera alla Data Box Gateway-enheter som ska aktiveras med din Data Box Gateway-resurs. Du kan hämta den här nyckeln nu när du befinner dig på Azure-portalen.

1. Klicka på den resurs som du skapade och klicka sedan på **Översikt**.

    ![Ny Data Box Gateway-resurs](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Klicka på **Generera nyckel** för att skapa en aktiveringsnyckel. Klicka på kopieringsikonen för att kopiera nyckeln och spara den för senare användning.

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


