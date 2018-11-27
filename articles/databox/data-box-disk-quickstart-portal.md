---
title: Snabbstart för Microsoft Azure Data Box Disk| Microsoft Docs
description: Använd den här snabbstarten för att snabbt distribuera Azure Data Box Disk på Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: fbe286313bce9b735665b134a5ea18ba4995548b
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712270"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal-preview"></a>Snabbstart: Distribuera Azure Data Box Disk med hjälp av Azure Portal (förhandsversion)

Den här snabbstarten beskriver hur du distribuerar Azure Data Box Disk med hjälp av Azure Portal. Stegen beskriver bland annat hur du snabbt skapar en order, tar emot diskar, packar upp och ansluter diskar samt kopierar data till diskarna så att de laddas upp till Azure. 

Detaljerade stegvisa distributions- och spårningsinstruktioner finns i [Självstudie: Beställa Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Data Box Disk är tillgängligt som förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar:

- Kontrollera att din prenumeration har aktiverats för Azure Data Box-tjänsten. Aktivera din prenumeration för den här tjänsten genom att [registrera dig för tjänsten](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="order"></a>Beställa

Det här steget tar ungefär 5 minuter.

1. Skapa en ny Azure Data Box-resurs på Azure Portal. 
2. Välj en prenumeration som har aktiverats för den här tjänsten och välj överföringstypen **Importera**. Ange **ursprungslandet** där dina data finns och **Azure-målregionen** för dataöverföringen.
3. Välj **Data Box Disk**. Den högsta lösningskapaciteten är 35 TB och du kan skapa flera diskorder för större datastorlekar.  
4. Ange order- och leveransinformationen. Om tjänsten är tillgänglig i din region anger du e-postadresser för aviseringsmeddelanden, läser sammanfattningen och skapar sedan ordern. 

När ordern har skapats förbereds diskarna för leverans. 

## <a name="unpack"></a>Uppackning

Det här steget tar ungefär 5 minuter.

Data Box-disken skickas i en UPS Express-box. Öppna boxen och kontrollera att den innehåller:

- 1 till 5 USB-diskar inlindade i bubbelplast.
- En anslutningskabel per disk. 
- En fraktsedel för returförsändelse.

## <a name="connect-and-unlock"></a>Ansluta och låsa upp

Det här steget tar ungefär 5 minuter.

1. Använd den medföljande kabeln för att ansluta disken till en Windows-/Linux-dator som kör en version som stöds. Mer information om operativsystemversioner som stöds finns i [Systemkrav för Azure Data Box Disk](data-box-disk-system-requirements.md). 
2. Så här låser du upp disken:

    1. På Azure Portal går du till **Allmänt > Enhetsinformation** och hämtar nyckeln.
    2. Ladda ned och extrahera det operativsystemspecifika Data Box Disk-upplåsningsverktyget på den dator som används för att kopiera data till diskarna. 
    3. Kör upplåsningsverktyget för Data Box Disk och ange nyckeln. Om du sätter i nya diskar kör du upplåsningsverktyget igen och anger nyckeln. **Använd inte BitLocker-dialogrutan eller BitLocker-nyckeln för att låsa upp disken.** Mer information om hur du låser upp diskar finns i [Låsa upp diskar på en Windows-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) eller [Låsa upp diskar på en Linux-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. Diskens tilldelade enhetsbeteckning visas i verktyget. Skriv ned diskens enhetsbeteckning. Du behöver den i efterföljande steg.

## <a name="copy-data-and-validate"></a>Kopiera data och verifiera

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken. 

1. Enheten innehåller mapparna *PageBlob*, *BlockBlob*, *DataBoxDiskImport*. Dra och släpp för att kopiera de data som ska importeras som blockblobar till mappen *BlockBlob*. På samma sätt drar du och släpper data som exempelvis VHD/VHDX till mappen *PageBlob*.

    En container skapas i Azure Storage-kontot för varje undermapp under mapparna *BlockBlob* och *PageBlob*. Alla filer under mapparna *BlockBlob* och *PageBlob* kopieras till standardcontainern `$root` under Azure Storage-kontot.

    > [!NOTE] 
    > - Alla containrar och blobar måste följa [Azures namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Om dessa regler inte uppfylls misslyckas datauppladdningen till Azure.
    > - Kontrollera att filerna inte överskrider ~4,75 TiB för blockblobar och ~ 8 TiB för sidblobar.

2. (Valfritt) När kopieringen är klar rekommenderar vi att du kör `DataBoxDiskValidation.cmd` i mappen *DataBoxDiskImport* för att generera kontrollsummor för verifiering. Beroende på datastorleken kan det här steget ta en stund. 
3. Koppla från enheten. 

## <a name="ship-to-azure"></a>Skicka till Azure

Det här steget tar cirka 5–7 minuter att slutföra.

1. Lägg alla diskar tillsammans i det ursprungliga paketet. Använd den medföljande fraktsedeln. Om etiketten är skadad eller saknas kan du ladda ned den från portalen. Gå till **Översikt** och klicka på **Ladda ned adressetikett** i kommandofältet.
2. Lämna in det förseglade paketet på inlämningsstället.  

Data Box Disk-tjänsten skickar ett e-postmeddelande och uppdaterar orderstatusen på Azure Portal.

## <a name="verify-your-data"></a>Verifiera dina data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När Data Box-disken har anslutits till nätverket på Azures datacenter startar datauppladdningen till Azure automatiskt. 
2. Azure Data Box-tjänsten meddelar dig via Azure Portal när datakopieringen är klar. 
    
    1. Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
    2. Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

## <a name="clean-up-resources"></a>Rensa resurser

Det här steget tar 2–3 minuter att slutföra.

Om du vill rensa resurser kan du avbryta Data Box-beställningen och ta bort ordern.

- Du kan annullera Data Box-beställningen på Azure Portal innan orderbehandlingen har påbörjats. När orderbehandlingen har påbörjats kan du inte avbryta ordern. Ordern behandlas tills den når slutskedet i processen. 

    Om du vill avbryta beställningen går du till **Översikt** och klickar på **Avbryt** i kommandofältet.  

- Du kan ta bort ordern när statusen visas som **slutförd** eller **avbruten** på Azure Portal. 

    Om du vill ta bort ordern går du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat Azure Data Box Disk för att importera dina data till Azure. Gå vidare till nästa självstudie om du vill veta mer om Azure Data Box Disk-hantering: 

> [!div class="nextstepaction"]
> [Administrera Data Box Disk via Azure Portal](data-box-portal-ui-admin.md)
