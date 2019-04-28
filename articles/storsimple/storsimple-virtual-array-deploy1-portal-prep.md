---
title: Portalen prep för StorSimple Virtual Array | Microsoft Docs
description: Första självstudie om du vill distribuera StorSimple virtual array innebär att förbereda Azure-portalen
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c6f0a6371b38f0271237db0f7d80b831ecc145c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127148"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Distribuera StorSimple Virtual Array – förbereda Azure-portalen

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Översikt

Det här är den första artikeln i serien av självstudiekurserna om distribution krävs för att distribuera din virtuella matris helt som en filserver eller en iSCSI-server med hjälp av Resource Manager-modellen. Den här artikeln beskriver förberedelse krävs för att skapa och konfigurera din StorSimple Device Manager-tjänsten innan du etablerar en virtuell matris. Den här artikeln innehåller också länkar ut till en checklista för distributionskonfiguration och konfiguration krav.

Du måste ha administratörsbehörighet för att utföra installationen och konfigurationen. Vi rekommenderar att du läser checklistan för distributionskonfiguration innan du börjar. Portalförberedelserna tar mindre än tio minuter.

Den information som publiceras i den här artikeln gäller för distribution av StorSimple Virtual Array i Azure-portalen och Microsoft Azure Government-molnet.

### <a name="get-started"></a>Kom igång
Arbetsflödet består av förbereda portalen, etablera en virtuell matris i din virtualiserade miljö och slutföra installationen. Om du vill komma igång med StorSimple Virtual Array-distribution som en filserver eller en iSCSI-server, måste du referera till följande resurser i tabellform.

#### <a name="deployment-articles"></a>Distributionsartiklarna

För att distribuera StorSimple Virtual Array, finns i följande artiklar i föreskrivna sekvensen.

| **#** | **I det här steget** | **Du gör detta...** | **Och Använd de här dokumenten.** |
| --- | --- | --- | --- |
| 1. |**Konfigurera Azure-portalen** |Skapa och konfigurera din StorSimple Device Manager-tjänsten innan du etablerar en StorSimple Virtual Array. |[Förbereda portalen](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Etablera den virtuella matrisen** |För Hyper-V, etablerar och ansluter till en StorSimple Virtual Array i ett värdsystem som kör Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. <br></br> <br></br> För VMware, etablerar och ansluter till en StorSimple Virtual Array i ett värdsystem som kör VMware ESXi 5.0, 5.5, 6.0 eller 6.5.<br></br> |[Etablera en virtuell matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Etablera en virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurera den virtuella matrisen** |Utföra installationen för din filserver, registrera din StorSimple-filserver och slutföra installationen av enheten. Sedan kan du etablera SMB-resurser. <br></br> <br></br> För iSCSI-servern, utföra installationen, registrera StorSimple iSCSI-servern och slutföra installationen av enheten. Du kan sedan etablera iSCSI-volymer. |[Konfigurera virtuell matris som filserver](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurera virtuell matris som iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Nu kan du börja konfigurera Azure-portalen.

## <a name="configuration-checklist"></a>Checklista för distributionskonfiguration

Checklista för distributionskonfiguration anger den information som du behöver samla in innan du konfigurerar programvaran på StorSimple Virtual Array. Förbereda den här informationen förbereds i förväg hjälper till att effektivisera processen för att distribuera StorSimple-enheten i din miljö. Beroende på om StorSimple Virtual Array distribueras som en filserver eller en iSCSI-server, behöver du något av följande checklistor.

* Ladda ned den [konfigurationschecklistan för StorSimple Virtual Array File Server](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Ladda ned den [StorSimple Virtual Array iSCSI Server konfigurationschecklistan](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Nödvändiga komponenter

Här hittar du konfigurationskraven för StorSimple Device Manager-tjänsten och StorSimple Virtual Array datacenternätverket.

### <a name="for-the-storsimple-device-manager-service"></a>För StorSimple Device Manager-tjänsten

Innan du börjar ska du kontrollera att:

* Du har ditt Microsoft-konto med autentiseringsuppgifter.
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
* Microsoft Azure-prenumerationen ska aktiveras för StorSimple Device Manager-tjänsten.

### <a name="for-the-storsimple-virtual-array"></a>För StorSimple Virtual Array

Kontrollera följande innan du distribuerar en virtuell matris:

* Du har åtkomst till en värdsystem som kör Hyper-V på Windows Server 2008 R2 eller senare eller VMware (ESXi 5.0, 5.5, 6.0 eller 6.5) som kan användas för att en etablera en enhet.
* Värddatorn är att dedikera följande resurser för att etablera den virtuella matrisen:
  
  * Minst 4 kärnor.
  * Minst 8 GB RAM. Om du vill konfigurera den virtuella matrisen som filserver stöder 8 GB 2 miljoner filer. Behöver du 16 GB RAM-minne till 2-4 miljoner stödfiler.
  * Ett nätverksgränssnitt.
  * En virtuell disk 500 GB för systemdata.

### <a name="for-the-datacenter-network"></a>För datacenternätverket

Innan du börjar ska du kontrollera att:

* Nätverket i datacentret har konfigurerats enligt nätverkskraven för StorSimple-enheten. Mer information finns i den [systemkrav för StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* StorSimple Virtual Array har en dedikerad 5 Mbit/s internetbandbredd (eller mer) vid alla tidpunkter. Den här bandbredden ska inte delas med andra program.

## <a name="step-by-step-preparation"></a>Stegvisa förberedelse

Använd följande steg för steg-instruktioner för att förbereda din portal för StorSimple Device Manager-tjänsten.

## <a name="step-1-create-a-new-service"></a>Steg 1: Skapa en ny tjänst

En instans av StorSimple Device Manager-tjänsten kan hantera flera StorSimple Virtual Array. Skapa en instans av StorSimple Device Manager-tjänsten genom att utföra stegen nedan. Om du har en befintlig StorSimple Device Manager-tjänsten för att hantera dina virtuella matriser, hoppa över det här steget och gå till [steg 2: Hämta tjänstregistreringsnyckeln](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Om du inte har aktiverat automatiskt skapande av lagringskonton med din tjänst måste du skapa minst ett lagringskonto efter att du har skapat en tjänst.
> 
> * Om du inte har skapat ett lagringskonto automatiskt går du till [Konfigurera ett nytt lagringskonto för tjänsten](#optional-step-configure-a-new-storage-account-for-the-service) för detaljerade anvisningar.
> * Om du har aktiverat automatiskt skapande av ett storage-konto går du till [steg 2: Hämta tjänstregistreringsnyckeln](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Steg 2: Hämta nyckel för tjänstregistrering

När StorSimple Device Manager-tjänsten är igång måste du hämta tjänstregistreringsnyckeln. Den här nyckeln används för att registrera och ansluta din StorSimple-enhet till tjänsten.

Utför följande steg på [Azure-portalen](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Nyckel för tjänstregistrering används för att registrera alla StorSimple Device Manager-enheter som behöver registreras med StorSimple Device Manager-tjänsten.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Steg 3: Ladda ned den virtuella matrisavbildningen

När du har Registreringsnyckeln för tjänsten kan behöver du ladda ned den lämpliga virtuella matrisavbildningen för att etablera en virtuell matris på värdsystemet. Virtuell matris-avbildningar är specifika för operativsystemet och kan hämtas från sidan Snabbstart i Azure-portalen.

> [!IMPORTANT]
> Programmet som körs på StorSimple Virtual Array kan bara användas med StorSimple Device Manager-tjänsten.
> 
> 

Utför följande steg på [Azure-portalen](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Att hämta den virtuella matrisavbildningen

1. Logga in på [Azure-portalen](https://portal.azure.com/). 
2. I Azure-portalen klickar du på **Bläddra > StorSimple-enhetshanterare**.
3. Välj en befintlig StorSimple Device Manager-tjänst. I den **StorSimple Device Manager** bladet klickar du på **Snabbstart**. 
4. Klicka på länken som motsvarar den avbildning som du vill ladda ned från Microsoft Download Center. Avbildningsfilerna är cirka 4,8 GB.
   
   * VHDX för Hyper-V på Windows Server 2012 och senare
   * VHD för Hyper-V på Windows Server 2008 R2 och senare
   * VMDK för VMWare ESXi 5.0, 5.5, 6.0 eller 6.5
5. Ladda ned och packa upp filen till en lokal enhet och skriv ned sökvägen till de uppackade filerna.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Valfritt steg: Konfigurera ett nytt lagringskonto för tjänsten

Det här steget är valfritt och bör utföras endast om du inte har aktiverat automatiskt skapande av ett lagringskonto med din tjänst.

Om du vill skapa ett Azure storage-konto i en annan region finns i [hur du skapar ett lagringskonto](../storage/common/storage-quickstart-create-account.md) stegvisa instruktioner.

Utför följande steg i den [Azure-portalen](https://ms.portal.azure.com/) på tjänstsidan StorSimple Device Manager för att lägga till ett befintligt Microsoft Azure-lagringskonto.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Lägg till autentiseringsuppgift för lagringskonto har som samma Azure-prenumeration som Device Manager-tjänsten

1. Navigera till Device Manager-tjänsten, väljer och dubbelklicka på den. Då öppnas det **översikt** bladet.
2. Välj **lagringskontouppgifter** inom den **Configuration** avsnittet.
3. Klicka på **Lägg till**.
4. I den **Lägg till ett lagringskonto** bladet gör du följande:
   
   1. För **prenumeration**väljer **aktuella**.
   
   2. Ange namnet på ditt Azure storage-konto.
   
   3. Välj **aktivera** att skapa en säker kanal för nätverkskommunikation mellan din StorSimple-enhet och molnet. Välj **inaktivera** endast om du arbetar i ett privat moln.
   
   4. Klicka på **Lägg till**. Du meddelas när lagringskontot har skapats.<br></br>
   
      ![Lägg till en befintlig autentiseringsuppgift för lagringskonto](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Nästa steg

Nästa steg är att etablera en virtuell dator för StorSimple Virtual Array. Läs de detaljerade instruktionerna i följande avsnitt, beroende på ditt värdoperativsystem:

* [Etablera en StorSimple virtuell matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Etablera en StorSimple virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

