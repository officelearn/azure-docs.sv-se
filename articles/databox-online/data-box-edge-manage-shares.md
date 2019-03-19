---
title: Hanteringen av filresurser i Azure Data Box Edge | Microsoft Docs
description: Beskriver hur du använder Azure-portalen för att hantera resurser på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 79648e30e832a056016b8842fdc39e27e206c9ee
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57897819"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Använda Azure-portalen för att hantera resurser på din Azure Data Box Edge

Den här artikeln beskriver hur du hanterar resurser på din Azure Data Box Edge. Du kan hantera Azure Data Box Edge via Azure portal eller via lokalt webbgränssnitt. Använd Azure-portalen för att lägga till, ta bort, uppdatera resurser eller synkronisera lagringskontots åtkomstnyckel för lagringskontot som är associerat med filresurser.

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.


## <a name="about-shares"></a>Om resurser

Om du vill överföra data till Azure måste du skapa filresurser på din Azure Data Box Edge. De filresurser som du lägger till på Data Box Edge-enhet kan vara lokala resurser eller resurser som skickar data till molnet.

 - **Lokala resurser**: Använd dessa resurser när du vill att data ska bearbetas lokalt på enheten.
 - **Filresurser**: Använd dessa resurser när du vill att enhetsdata ska skickas automatiskt till ditt lagringskonto i molnet. Alla moln fungerar som **uppdatera** och **synkronisera lagringsnycklar** avser filresurser.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till en resurs
> * Ta bort en resurs
> * Uppdatera resurser
> * Synkronisera lagringsnyckel


## <a name="add-a-share"></a>Lägga till en resurs

Skapa en resurs genom att utföra stegen nedan på Azure-portalen.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Gateway > resurser**. Välj **+ Lägg till resurs** i kommandofältet.

    ![Välj Lägg till resurs](media/data-box-edge-manage-shares/add-share-1.png)

2. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen.
    
    Resursnamn får bara innehålla siffror, gemener och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.

3. Välj en **typ** för resursen. Typen kan vara **SMB** eller **NFS**, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

4. Ange ett **Lagringskonto** där resursen ska placeras. En container skapas på lagringskontot med resursnamnet om containern inte redan finns. Om containern redan finns används den befintliga containern.

5. I listrutan väljer du den **lagringstjänst** från blockblob, sidblob eller filer. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. Till exempel i den här instansen, som vi vill att data ska lagras som blockblobar i Azure, och därför väljer vi **Blockblob**. Om du väljer **Sidblob**, måste du se till att dina data är 512 byte justerad. Använd **sidblob** för VHD eller VHDX som alltid är 512 byte justerad.

6. Det här steget beror på om du skapar en SMB- eller en NFS-resurs.
    - **Om du skapar en SMB-resurs** – I fältet för **lokal användare med fullständig behörighet** väljer du mellan **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare anger du **användarnamn**, **lösenord** och sedan bekräfta lösenord. Detta tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.

        ![Lägga till SMB-resurs](media/data-box-edge-manage-shares/add-smb-share.png)

        Om du markerar alternativet för att endast tillåta läsåtgärder för dessa resursdata kan du ange skrivskyddade användare.
    - **Om du skapar en NFS-resurs** – Du måste ange **IP-adresserna för de tillåtna klienter** som har åtkomst till resursen.

        ![Lägga till NFS-resurs](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Klicka på **Skapa** för att skapa resursen. Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna uppdateras bladet **Resurser** med den nya resursen.
 
## <a name="add-a-local-share"></a>Lägg till en lokal resurs

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Gateway > resurser**. Välj **+ Lägg till resurs** i kommandofältet.

    ![Välj Lägg till resurs](media/data-box-edge-manage-shares/add-local-share-1.png)

2. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen.
    
    Resursnamn får bara innehålla siffror, gemener och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.

3. Välj en **typ** för resursen. Typen kan vara **SMB** eller **NFS**, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

4. Använda lokala monteringspunkten så att du enkelt vill komma åt filresurser från Edge-moduler för beräkning. Välj **använder resursen med Edge-beräkning** så att Edge-modul kan använda beräkningarna med lokala monteringspunkten.

5. Välj **konfigurera som Edge lokala resurser**. Data i lokala resurser kvar lokalt på enheten. Du kan bearbeta dessa data lokalt.

6. I den **alla lokala användare med privilegier** , Välj från **Skapa nytt** eller **Använd befintlig**.

7. Välj **Skapa**. 

    ![Skapa lokal resurs](media/data-box-edge-manage-shares/add-local-share-2.png)

    Du ser ett meddelande om att skapa resursen håller på att skapas. När resursen har skapats med de angivna inställningarna uppdateras bladet **Resurser** med den nya resursen.

    ![Visa uppdateringar resurser bladet](media/data-box-edge-manage-shares/add-local-share-4.png)
    
    Markera resursen som Visa lokala monteringspunkt för Edge-moduler för beräkning för den här resursen.

    ![Visa lokala resursinformation](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="delete-a-share"></a>Ta bort en resurs

Utför följande steg på Azure-portalen om du vill ta bort en resurs.

1. Välj från listan över resurser, och klicka på den resurs som du vill ta bort.

    ![Välja resurs](media/data-box-edge-manage-shares/delete-share-1.png)

2. Klicka på **Ta bort**.

    ![Klicka på Ta bort](media/data-box-edge-manage-shares/delete-share-2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Bekräfta borttagning](media/data-box-edge-manage-shares/delete-share-3.png)

Lista över resurser uppdateringar borttagningen.


## <a name="refresh-shares"></a>Uppdatera resurser

Uppdatera för kan du uppdatera innehållet i en resurs. När du uppdaterar en resurs initieras en sökning för att hitta alla Azure-objekt, inklusive blobar och filer som har lagts till i molnet sedan den senaste uppdateringen. Dessa ytterligare filer sedan laddas ned för att uppdatera innehållet i filresursen på enheten.

> [!IMPORTANT]
> Du kan inte uppdatera lokala resurser.

Utför följande steg på Azure-portalen om du vill uppdatera en resurs.

1.  Gå till **Resurser** i Azure-portalen. Välj och klicka på den resurs som du vill uppdatera.

    ![Välja resurs](media/data-box-edge-manage-shares/refresh-share-1.png)

2.  Klicka på **Uppdatera**. 

    ![Klicka på Uppdatera](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.  Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Ett jobb startas för att uppdatera innehållet i den lokala resursen.

    ![Bekräfta uppdatering](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.  Medan uppdateringen pågår nedtonas uppdateringsalternativet i snabbmenyn. Klicka på meddelandet för jobbet för att visa jobbstatus för uppdateringen.

5.  Tiden för att uppdatera bestäms av antalet filer i Azure-containern samt filerna på enheten. När uppdateringen är klar uppdateras tidsstämpeln för resursen. Även om uppdateringen delvis har misslyckats anses åtgärden ha lyckats och tidsstämpeln uppdateras. Felloggarna uppdatering uppdateras också.

    ![Uppdaterad tidsstämpel](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Om ett fel uppstår genereras en avisering. Aviseringen innehåller detaljerad information om orsaken och rekommendationen för att åtgärda problemet. Aviseringen innehåller också en länk till en fil med en fullständig sammanfattning av felen, inklusive de filer som inte kunde uppdateras eller tas bort.


## <a name="sync-storage-keys"></a>Synkronisera lagringsnycklar

Om dina lagringskontonycklar har roterats måste du synkronisera åtkomstnycklarna för lagring. Synkroniseringen hjälper enheten att hämta de senaste nycklarna för ditt lagringskonto.

Utför följande steg i Azure-portalen för att synkronisera din lagringsåtkomstnyckel.

1. Gå till **Översikt** i din resurs. Välj från listan över resurser och klicka på en resurs som är associerad med det lagringskonto som du vill synkronisera.

    ![Välj resurs med relevanta storage-konto](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Klicka på **Synkronisera lagringsnyckel**. Klicka på **Ja** när du uppmanas att bekräfta.

     ![Välj synkronisering lagringsnyckel](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Avsluta dialogrutan när synkroniseringen är klar.

>[!NOTE]
> Du behöver bara utföra åtgärden en gång för ett givet lagringskonto. Du behöver inte upprepa åtgärden för alla resurser som är associerade med samma lagringskonto.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](data-box-edge-manage-users.md).
