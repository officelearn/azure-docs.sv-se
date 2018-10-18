---
title: Överföra data med Azure Data Box Edge | Microsoft Docs
description: Lär dig hur du lägger till och ansluter till resurser på en Data Box Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 61d427c22f2ac57627ac04a91748e4e6cf8c4e55
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165371"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Självstudie: Överföra data med Azure Data Box Edge (förhandsversion)

Den här självstudien beskriver hur du lägger till och ansluter till resurser på Data Box Edge. När resurserna har lagts till kan Data Box Edge-enheten överföra data till Azure.

Den här proceduren kan ta upp till 10 minuter att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resurs

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen. 
 
## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera följande innan du lägger till resurser till din Data Box Edge:

* Du har installerat den fysiska enheten enligt beskrivningen i [Installera en Data Box Edge](data-box-edge-deploy-install.md). 

    Den fysiska enheten har aktiverats enligt beskrivningen i [Ansluta och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md). Enheten är redo för att du ska skapa resurser och överföra data.


## <a name="add-a-share"></a>Lägga till en resurs

Skapa en resurs genom att utföra stegen nedan på [Azure-portalen](https://portal.azure.com/).

1. Gå tillbaka till Azure-portalen. Gå till **Alla resurser** och sök efter din Data Box Edge-resurs.
    
2. Välj din Data Box Edge-resurs i den filtrerade listan över resurser och gå till **Översikt**. Klicka på **+ Lägg till resurs** i enhetens kommandofält.
   
   ![Lägga till en resurs](./media/data-box-edge-deploy-add-shares/click-add-share.png)

3. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen. 

   Resursnamn får bara innehålla siffror, gemener och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.
    
    1. Välj en **typ** för resursen. Typen kan vara SMB eller NFS, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. 

    2. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika. 

    3. Du måste ange ett lagringskonto där resursen ska placeras. En container skapas på lagringskontot med resursnamnet om containern inte redan finns. Om containern redan finns används den befintliga containern. 
    
    4. Välj **lagringstjänst** – blockblob, sidblob eller filer. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. I det här exemplet vill vi till exempel att data ska lagras som blob-block i Azure, och därför väljer vi Blockblob. Om du väljer Sidblob kontrollerar du att dina data är justerade för 512 byte. Till exempel är en VHDX alltid justerad för 512 byte.
   
    5. Det här steget beror på om du skapar en SMB- eller en NFS-resurs. 
     
        - **Om du skapar en SMB-resurs** – I fältet för lokal användare med fullständig behörighet väljer du mellan **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare anger du **användarnamn**, **lösenord** och sedan **bekräfta lösenord**. Detta tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.

            Om du markerar att **endast tillåta läsåtgärder** för dessa resursdata har du sedan alternativet att ange skrivskyddade användare.

            ![Lägga till SMB-resurs](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
        - **Om du skapar en NFS-resurs** – Du måste ange IP-adresserna för de tillåtna klienter som har åtkomst till resursen.

            ![Lägga till NFS-resurs](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Klicka på **Skapa** för att skapa resursen. 
    
    Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna uppdateras bladet **Resurser** med den nya resursen. 
    
    ![Uppdaterad lista över resurser](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ansluta till resursen

Du kan nu ansluta till en eller flera resurser som du skapade i föregående steg. Beroende på om du har en SMB- eller en NFS-resurs kan stegen vara olika. 

### <a name="connect-to-an-smb-share"></a>Ansluta till en SMB-resurs

Utför dessa steg på din Windows Server-klient som är ansluten till din Data Box Edge för att ansluta till resurser.


1. Öppna ett kommandofönster. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ange lösenordet för resursen när du tillfrågas. Exempel på utdata från det här kommandot visas här.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


2. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>`. Klicka på **OK**. Utforskaren öppnas. Du bör nu kunna se de resurser du har skapat som mappar. Välj och dubbelklicka på en resurs (mapp) för att visa innehållet.
 
    ![Ansluta till SMB-resurs](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Data skrivs till dessa resurser då de genereras och enheten skickar data till molnet.

### <a name="connect-to-an-nfs-share"></a>Ansluta till en NFS-resurs

Utför dessa steg på din Linux-klient som är ansluten till Data Box Edge.

1. Kontrollera att klienten har NFSv4-klienten installerad. Om du vill installera NFS-klienten använder du följande kommando:

   `sudo apt-get install nfs-common`

    Mer information finns i [det här avsnittet om att installera NFSv4-klienten](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. När NFS-klienten har installerats använder du följande kommando för att montera den NFS-resurs som du har skapat på Data Box Edge-enheten:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Innan du monterar filresurserna kontrollerar du om de kataloger som kommer att fungera som monteringspunkter på den lokala datorn redan har skapats. Dessa kataloger får inte innehålla några filer eller undermappar.

    I följande exempel visas hur du ansluter via NFS till en resurs på din Data Box Edge-enhet. Enhetens IP-adress är `10.10.10.60`. `mylinuxshare2`-resursen är monterad på den virtuella Ubuntu-datorn. Resursens monteringspunkt är `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Följande villkor gäller för förhandsversionen:
> - När en fil skapas i resurser går det inte att byta namn på filen. 
> - När en fil tas bort från en resurs försvinner inte posten i lagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Box Edge-ämnen som att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resurs


Gå vidare till nästa självstudie och lär dig hur du transformerar dina data med Data Box Edge.

> [!div class="nextstepaction"]
> [Transformera data med Data Box Edge](./data-box-edge-deploy-configure-compute.md)


