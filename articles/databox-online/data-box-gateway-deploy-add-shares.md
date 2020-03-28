---
title: Överföra data med Azure Data Box Gateway | Microsoft Docs
description: Lär dig hur du lägger till och ansluter till resurser på en Data Box Gateway-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 32466cc0a1ab9b86fc2fb8eb791c232ae13f1c01
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79213562"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Självstudiekurs: Överföra data med Azure Data Box Gateway


## <a name="introduction"></a>Introduktion

I den här artikeln beskrivs hur du lägger till och ansluter till resurser på din Data Box Gateway. När du har lagt till resurserna kan Data Box Gateway-enheten överföra data till Azure.

Den här proceduren kan ta upp till 10 minuter att slutföra.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> * Lägga till en resurs
> * Ansluta till resurs

## <a name="prerequisites"></a>Krav

Kontrollera följande innan du lägger till resurser till din Data Box Gateway:

- Du har etablerat en virtuell enhet och anslutit till den enligt beskrivningen i [etableringen av en databoxgateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md) eller [etablera en databoxgateway i VMware](data-box-gateway-deploy-provision-vmware.md).

- Du har aktiverat den virtuella enheten som beskrivs i [Anslut och aktivera din Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Enheten är redo för att du ska skapa resurser och överföra data.

## <a name="add-a-share"></a>Lägga till en resurs

Så här skapar du en resurs:

1. I [Azure-portalen](https://portal.azure.com/)väljer du din Data Box Gateway-resurs och går sedan till **Översikt**. Enheten ska vara online. Välj **+ Lägg till resurs** i enhetskommandofältet.
   
   ![Lägga till en resurs](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Gör följande i **Lägg till resurs:**

    1. Ange ett unikt namn på resursen. Resursnamnen kan bara ha gemener, siffror och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller ett tal. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.
    
    2. Välj en **typ** för resursen. Typen kan vara SMB eller NFS, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

    3. Ange ett lagringskonto där resursen ska finnas. En container skapas på lagringskontot med det nyligen skapade resursnamnet om det inte redan finns en. Om containern redan finns används den befintliga containern.
       > [!IMPORTANT]
       > Kontrollera att Azure Storage-kontot som du använder inte har principer för oföränderlighet som angetts för det om du använder det med en Azure Stack Edge- eller Data Box Gateway-enhet. Mer information finns i [Ange och hantera principer för oföränderlighet för bloblagring](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).
    
    4. Välj **lagringstjänst** – blockblob, sidblob eller filer. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. I det här fallet vill vi till exempel att data ska lagras som blob-block i Azure, därför väljer vi Blockblob. Om du väljer Sidblob måste du kontrollera att dina data är justerade för 512 byte. Till exempel är en VHDX alltid justerad för 512 byte.
   
    5. Det här steget beror på om du skapar en SMB- eller en NFS-resurs.
     
    - **SMB-resurs** – Under **Alla privilegier lokal användare**väljer du Skapa **ny** eller **Använd befintlig**. Om du skapar en ny lokal användare anger du ett **användarnamn** och **lösenord**och bekräftar sedan **lösenordet**. Den här åtgärden tilldelar behörigheterna till den lokala användaren. Ändring av behörigheter på resursnivå stöds för närvarande inte.
    
        ![Lägga till SMB-resurs](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Om du väljer Kryssrutan **Tillåt endast läsåtgärder** för dessa delningsdata kan du ange skrivskyddade användare.
        
    - **NFS-resurs** - Ange IP-adresserna för tillåtna klienter som kan komma åt resursen.

        ![Lägga till NFS-resurs](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Klicka på **Skapa** för att skapa resursen.
    
    Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna uppdateras panelen **Resurser** för att återspegla den nya resursen.
    
    ![Panel för uppdaterade resurser](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ansluta till resursen

Du kan nu ansluta till en eller flera av de resurser som du skapade i det sista steget. Beroende på om du har en SMB- eller en NFS-resurs kan stegen variera.

### <a name="connect-to-an-smb-share"></a>Ansluta till en SMB-resurs

På din Windows Server-klient som är ansluten till Data Box Gateway ansluter du till en SMB-resurs genom att ange kommandona:


1. I ett kommandofönster skriver du:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ange lösenordet för resursen när du tillfrågas. Exempel på utdata från det här kommandot visas här.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Välj Windows + R på tangentbordet. 
3. I fönstret **Kör** anger `\\<device IP address>` du och väljer sedan **OK**. Utforskaren öppnas. Du bör nu kunna se de resurser du har skapat som mappar. Dubbelklicka på en resurs (mapp) i Utforskaren för att visa innehållet.
 
    ![Ansluta till SMB-resurs](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Data skrivs till dessa resurser då de genereras och enheten skickar data till molnet.

### <a name="connect-to-an-nfs-share"></a>Ansluta till en NFS-resurs

Utför följande procedur på en Linux-klient som är ansluten till en Data Box Edge-enhet:

1. Kontrollera att klienten har NFSv4-klienten installerad. Om du vill installera NFS-klienten använder du följande kommando:

   `sudo apt-get install nfs-common`

    Mer information finns i [det här avsnittet om att installera NFSv4-klienten](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen som du har skapat på Data Box Gateway-enheten:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Innan du monterar ska du kontrollera att de kataloger som fungerar som monteringspunkter på den lokala datorn redan har skapats och att de inte innehåller några filer eller undermappar.

    I följande exempel visas hur du ansluter via NFS till en resurs på Gateway-enhet. Den virtuella enhetens IP-adress är `10.10.10.60`, resursen `mylinuxshare2` är monterad på ubuntuVM, och monteringspunkten är `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Följande förbehåll gäller för den här versionen:
> - När en fil har skapats i resurserna stöds inte namnbytet av filen.
> - När en fil tas bort från en resurs försvinner inte posten i lagringskontot.
> - Om du använder `rsync` för att kopiera data stöds inte alternativet `rsync -a`.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Gateway-ämnen som att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resurs


Gå vidare till nästa självstudie och lär dig hur du administrerar Data Box Gateway.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Gateway](https://aka.ms/dbg-docs)


