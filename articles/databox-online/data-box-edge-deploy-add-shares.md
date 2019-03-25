---
title: Överföra data med Azure Data Box Edge | Microsoft Docs
description: Lär dig hur du lägger till och ansluter till resurser på en Data Box Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e902f0c9465f65f31f6e1a5cadc7b6b30cda1a27
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403688"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Självstudier: Överföra data med Azure Data Box Edge

Den här självstudien beskriver hur du lägger till och ansluter till resurser på din Data Box Edge-enhet. När du har lagt till resurserna kan Data Box Edge överföra data till Azure.

Den här proceduren kan ta upp till 10 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resursen

 
## <a name="prerequisites"></a>Förutsättningar

Kontrollera följande innan du lägger till resurser till din Data Box Edge:

- Du har installerat den fysiska enheten enligt beskrivningen i [Installera Azure Data Box Edge](data-box-edge-deploy-install.md).

- Du har aktiverat den fysiska enheten enligt beskrivningen i [Ansluta, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Lägga till en resurs

Om du vill skapa en resurs, gör du så här:

1. I den [Azure-portalen](https://portal.azure.com/), Välj din Data Box Edge-resurs och gå sedan till den **översikt**. Din enhet ska vara online.

   ![Enhet online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Välj **+ Lägg till resurs** i kommandofältet för enheten.

   ![Lägga till en resurs](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. I fönstret **Lägg till resurs** gör du följande:

    a. Ange ett unikt namn på resursen i rutan **Namn**.  
    Resursnamnet får bara ha gemena bokstäver, siffror och bindestreck. Den måste bestå av 3 till 63 tecken och börja med en bokstav eller ett numeriskt värde. Bindestreck måste föregås och följas av en bokstav eller ett numeriskt värde.
    
    b. Välj en **typ** för resursen.  
    Typen kan vara **SMB** eller **NFS**, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter.  
    Beroende på om du väljer SMB- eller NFS-resurser varierar resten av alternativen något. 

    c. Ange ett lagringskonto där resursen ska placeras. 

    
    d. Välj **Blockblob**, **Sidblob** eller **Filer** i listrutan **Lagringstjänst**.  
    Vilken typ av tjänst som du väljer beror på vilket format som du vill att dina data ska använda i Azure. I det här exemplet, eftersom vi vill lagra data som blockblobar i Azure, väljer vi **Blockblob**. Om du väljer **Sidblob**, se till att dina data är 512 byte justerad. Till exempel är en VHDX alltid justerad för 512 byte.

    e. Skapa en ny blobbehållare eller använda ett befintligt namn i listrutan. Om du skapar en blob-behållare ger ett behållarnamn. En container skapas på lagringskontot med det nyligen skapade resursnamnet om det inte redan finns en.
   
    f. Beroende på om du har skapat en SMB-resurs eller en NFS-resurs, gör du något av följande: 
     
    - **SMB-resurs**: Under **Lokal användare med full behörighet** väljer du **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare, anger du ett användarnamn och ett lösenord och bekräftar därefter lösenordet. Denna åtgärd tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren för att modifiera dem.

        Om du väljer den **Tillåt endast läsåtgärder** markerar du kryssrutan för den här resursdata, kan du ange skrivskyddade användare.

        ![Lägga till SMB-resurs](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-resurs**: Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen.

        ![Lägga till NFS-resurs](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Klicka på **Skapa** för att skapa resursen.
    
    Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna i **resurser** panelen uppdateringar för att återspegla den nya resursen.
    

## <a name="connect-to-the-share"></a>Ansluta till resursen

Du kan nu ansluta till en eller flera av de resurser som du skapade i det sista steget. Beroende på om du har en SMB- eller en NFS-resurs kan stegen variera.

### <a name="connect-to-an-smb-share"></a>Ansluta till en SMB-resurs

På en Windows Server-klient ansluten till en Data Box Edge-enhet ansluter du till en SMB-resurs genom att ange följande kommandon:


1. I ett kommandofönster skriver du:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Ange lösenordet för resursen när du uppmanas att göra det.  
   Exempel på utdata från det här kommandot visas här.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Välj Windows + R på tangentbordet.

4. I fönstret **Kör** anger du `\\<device IP address>` och väljer sedan **OK**.  
   Utforskaren öppnas. Du bör nu kunna se de resurser du har skapat som mappar. Dubbelklicka på en resurs (mapp) i Utforskaren för att visa innehållet.
 
    ![Ansluta till SMB-resurs](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Data skrivs till dessa resurser då de genereras och enheten skickar data till molnet.

### <a name="connect-to-an-nfs-share"></a>Ansluta till en NFS-resurs

Utför följande procedur på en Linux-klient som är ansluten till en Data Box Edge-enhet:

1. Kontrollera att klienten har NFSv4-klienten installerad. Om du vill installera NFS-klienten använder du följande kommando:

   `sudo apt-get install nfs-common`

    Mer information finns i [det här avsnittet om att installera NFSv4-klienten](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. När NFS-klienten har installerats, monterar du den NFS-resurs som du skapade på din Data Box Edge-enhet med följa kommando:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Användning av `sync` alternativet när du monterar resurser förbättrar överföringshastighet av stora filer.
    > Innan du monterar filresurserna kontrollerar du om de kataloger som kommer att fungera som monteringspunkter på den lokala datorn redan har skapats. Dessa kataloger får inte innehålla några filer eller undermappar.

    I följande exempel visas hur du ansluter via NFS till en resurs på din Data Box Edge-enhet. Enhetens IP-adress är `10.10.10.60`. `mylinuxshare2`-resursen är monterad på den virtuella Ubuntu-datorn. Resursens monteringspunkt är `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Följande villkor kan tillämpas på den här versionen:
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


