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
ms.openlocfilehash: 6c6553ace250aa9cbc06dfdfea77fc5e1637cd41
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384827"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Självstudie: Överföra data med Data Box Edge (förhandsversion)

Den här självstudien beskriver hur du lägger till och ansluter till resurser på din Data Box Edge-enhet. När du har lagt till resurserna kan Data Box Edge överföra data till Azure.

Den här proceduren kan ta upp till 10 minuter att slutföra. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resursen

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen. 
 
## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera följande innan du lägger till resurser till din Data Box Edge:
* Du har installerat den fysiska enheten enligt beskrivningen i [Installera Azure Data Box Edge](data-box-edge-deploy-install.md). 

* Du har aktiverat den fysiska enheten enligt beskrivningen i [Ansluta, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md). 

* Enheten är redo för att du ska skapa resurser och överföra data.


## <a name="add-a-share"></a>Lägga till en resurs

Om du vill skapa en resurs, gör du så här:

1. Gå till [Azure Portal](https://portal.azure.com/) och sedan till **Alla resurser** och sök sedan efter din Data Box Edge-resurs.
    
1. Välj din Data Box Edge-resurs i den filtrerade listan över resurser.

1. I den vänstra rutan väljer du **Översikt** och sedan väljer du **Lägg till resurs**.
   
   ![Lägga till en resurs](./media/data-box-edge-deploy-add-shares/click-add-share.png)

1. I fönstret **Lägg till resurs** gör du följande:

    a. Ange ett unikt namn på resursen i rutan **Namn**.  
    Resursnamnet får bara ha gemena bokstäver, siffror och bindestreck. Det måste ha mellan 3 och 63 tecken och börja med en bokstav eller ett numeriskt värde. Bindestreck måste föregås och följas av en bokstav eller ett numeriskt värde.
    
    b. Välj en **typ** för resursen.  
    Typen kan vara **SMB** eller **NFS**, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter.  
    Beroende på om du väljer SMB- eller NFS-resurser varierar resten av alternativen något. 

    c. Ange ett lagringskonto där resursen ska lagras.  
    En container skapas på lagringskontot med det nyligen skapade resursnamnet om det inte redan finns en. Om containern redan finns används den befintliga containern. 
    
    d. Välj **Blockblob**, **Sidblob** eller **Filer** i listrutan **Lagringstjänst**.  
    Vilken typ av tjänst som du väljer beror på vilket format som du vill att dina data ska använda i Azure. I det här exemplet väljer vi **Blockblob** eftersom vi vill att våra data ska lagras som blobblock. Om du väljer Sidblob kontrollerar du att dina data är justerade för 512 byte. Till exempel är en VHDX alltid justerad för 512 byte.
   
    e. Beroende på om du har skapat en SMB-resurs eller en NFS-resurs, gör du något av följande: 
     
    - **SMB-resurs**: Under **Lokal användare med full behörighet** väljer du **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare, anger du ett användarnamn och ett lösenord och bekräftar därefter lösenordet. Denna åtgärd tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren för att modifiera dem.

        Om du markerar kryssrutan **Tillåt endast läsåtgärder** för dessa resursdata kan du ange skrivskyddade användare.

        ![Lägga till SMB-resurs](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-resurs**: Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen.

        ![Lägga till NFS-resurs](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
1. Klicka på **Skapa** för att skapa resursen. 
    
    Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna uppdateras avsnittet **Resurser** med den nya resursinformationen. 
    
    ![Uppdaterad lista över resurser](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ansluta till resursen

Du kan nu ansluta till en eller flera av de resurser som du skapade i det sista steget. Beroende på om du har en SMB- eller en NFS-resurs kan stegen variera. 

### <a name="connect-to-an-smb-share"></a>Ansluta till en SMB-resurs

På en Windows Server-klient ansluten till en Data Box Edge-enhet ansluter du till en SMB-resurs genom att ange följande kommandon:


1. I ett kommandofönster skriver du:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

1. Ange lösenordet för resursen när du uppmanas att göra det.  
   Exempel på utdata från det här kommandot visas här.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 Microsoft Corporation. All rights reserved. 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


1. Välj Windows + R på tangentbordet. 

1. I fönstret **Kör** anger du `\\<device IP address>` och väljer sedan **OK**.  
   Utforskaren öppnas. Du bör nu kunna se de resurser du har skapat som mappar. Dubbelklicka på en resurs (mapp) i Utforskaren för att visa innehållet.
 
    ![Ansluta till SMB-resurs](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Data skrivs till dessa resurser då de genereras och enheten skickar data till molnet.

### <a name="connect-to-an-nfs-share"></a>Ansluta till en NFS-resurs

Utför följande procedur på en Linux-klient som är ansluten till en Data Box Edge-enhet:

1. Kontrollera att klienten har NFSv4-klienten installerad. Om du vill installera NFS-klienten använder du följande kommando:

   `sudo apt-get install nfs-common`

    Mer information finns i [det här avsnittet om att installera NFSv4-klienten](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

1. När NFS-klienten har installerats, monterar du den NFS-resurs som du skapade på din Data Box Edge-enhet med följa kommando:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Innan du monterar filresurserna kontrollerar du om de kataloger som kommer att fungera som monteringspunkter på den lokala datorn redan har skapats. Dessa kataloger får inte innehålla några filer eller undermappar.

    I följande exempel visas hur du ansluter via NFS till en resurs på din Data Box Edge-enhet. Enhetens IP-adress är `10.10.10.60`. `mylinuxshare2`-resursen är monterad på den virtuella Ubuntu-datorn. Resursens monteringspunkt är `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Följande villkor gäller för förhandsversionen:
> - När en fil har skapats i resurser går det inte att byta namn på filen. 
> - När en fil tas bort från en resurs försvinner inte posten i lagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om följande Data Box Edge-ämnen:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resurs

Gå vidare till nästa självstudie för att lära dig hur du transformerar dina data med Data Box Edge:

> [!div class="nextstepaction"]
> [Transformera data med Data Box Edge](./data-box-edge-deploy-configure-compute.md)


