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
ms.openlocfilehash: d930b1db48e3a5c4bda96f0b7d80a9c9f24d53d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755653"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Självstudier: Överföra data med Azure Data Box-Gateway


## <a name="introduction"></a>Introduktion

Den här artikeln beskriver hur du lägger till och ansluta till resurser på din Data Box-Gateway. När du har lagt till filresurser kan gatewayenhet för Data Box överföra data till Azure.

Den här proceduren kan ta upp till 10 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ansluta till resurs


## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera följande innan du lägger till resurser till din Data Box Gateway:

- Du har etablerat en virtuell enhet och är anslutna till den som beskrivs i den [etablera en Data Box-Gateway i Hyper-V](data-box-gateway-deploy-provision-hyperv.md) eller [etablera en Data Box-Gateway i VMware](data-box-gateway-deploy-provision-vmware.md).

- Du har aktiverat den virtuella enheten som beskrivs i [Connect och aktivera din Azure Data Box-Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Enheten är redo för att du ska skapa resurser och överföra data.

## <a name="add-a-share"></a>Lägga till en resurs

Skapa en resurs gör följande:

1. I den [Azure-portalen](https://portal.azure.com/), Välj din Data Box Gateway-resurs och gå sedan till **översikt**. Din enhet ska vara online. Välj **+ Lägg till resurs** i kommandofältet för enheten.
   
   ![Lägga till en resurs](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. I **Lägg till resurs**, utför följande procedur:

    1. Ange ett unikt namn på resursen. Resursnamnen kan bara ha gemena bokstäver, siffror och bindestreck. Resursnamnet måste ha mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.
    
    2. Välj en **typ** för resursen. Typen kan vara SMB eller NFS, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

    3. Ange ett lagringskonto där resursen ska placeras. En container skapas på lagringskontot med det nyligen skapade resursnamnet om det inte redan finns en. Om containern redan finns används den befintliga containern.
    
    4. Välj **lagringstjänst** – blockblob, sidblob eller filer. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. I det här fallet vill vi till exempel att data ska lagras som blob-block i Azure, därför väljer vi Blockblob. Om du väljer Sidblob måste du kontrollera att dina data är justerade för 512 byte. Till exempel är en VHDX alltid justerad för 512 byte.
   
    5. Det här steget beror på om du skapar en SMB- eller en NFS-resurs.
     
    - **SMB-resurs** – Under **alla lokala användare med privilegier**väljer **Skapa nytt** eller **Använd befintlig**. Om du skapar en ny lokal användare måste ange en **användarnamn** och **lösenord**, och sedan **Bekräfta lösenord**. Den här åtgärden tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheter här använder du Utforskaren för att ändra dessa behörigheter.
    
        ![Lägga till SMB-resurs](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Om du väljer **Tillåt endast läsåtgärder** markerar du kryssrutan för den här resursdata, kan du ange skrivskyddade användare.
        
    - **NFS-resursens** – ange IP-adresserna för de tillåtna klienter som har åtkomst till resursen.

        ![Lägga till NFS-resurs](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Klicka på **Skapa** för att skapa resursen.
    
    Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna i **resurser** panelen uppdateringar för att återspegla den nya resursen.
    
    ![Uppdaterade resurser sida vid sida](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Ansluta till resursen

Du kan nu ansluta till en eller flera av de resurser som du skapade i det sista steget. Beroende på om du har en SMB- eller en NFS-resurs kan stegen variera.

### <a name="connect-to-an-smb-share"></a>Ansluta till en SMB-resurs

Anslut till en SMB-resurs genom att ange kommandon från Windows Server klienten är ansluten till din Data Box-Gateway:


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


2. Välj Windows + r på tangentbordet 
3. I den **kör** fönstret, ange den `\\<device IP address>` och välj sedan **OK**. Utforskaren öppnas. Du bör nu kunna se de resurser du har skapat som mappar. Dubbelklicka på en resurs (mapp) i Utforskaren för att visa innehållet.
 
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
> Följande villkor kan tillämpas på den här versionen:
> - När en fil skapas i resurser går det inte att byta namn på filen.
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


