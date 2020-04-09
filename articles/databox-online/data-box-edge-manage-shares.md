---
title: Azure Data Box Edge-resurshantering | Microsoft-dokument
description: Beskriver hur du använder Azure-portalen för att hantera resurser på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 4877b136f197f226f142f5ad5eb4035c63c07d7a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887018"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Använda Azure-portalen för att hantera resurser på din Azure Data Box Edge

I den här artikeln beskrivs hur du hanterar resurser på din Azure Data Box Edge. Du kan hantera Azure Data Box Edge via Azure-portalen eller via det lokala webbgränssnittet. Använd Azure-portalen för att lägga till, ta bort, uppdatera resurser eller synkronisera lagringsnyckel för lagringskonto som är associerat med resurserna.

## <a name="about-shares"></a>Om resurser

Om du vill överföra data till Azure måste du skapa resurser på din Azure Data Box Edge. De resurser som du lägger till på Data Box Edge-enheten kan vara lokala resurser eller resurser som skickar data till molnet.

 - **Lokala resurser**: Använd dessa resurser när du vill att data ska bearbetas lokalt på enheten.
 - **Resurser**: Använd dessa resurser när du vill att enhetsdata automatiskt ska skickas till ditt lagringskonto i molnet. Alla molnfunktioner som **Uppdatera** och **synkronisera lagringsnycklar** gäller för resurserna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Lägga till en resurs
> * Ta bort en resurs
> * Uppdatera resurser
> * Synkronisera lagringsnyckel

## <a name="add-a-share"></a>Lägga till en resurs

Skapa en resurs genom att utföra stegen nedan på Azure-portalen.

1. Gå till databoxkantsresursen i Azure-portalen och gå sedan till **Gateway > Shares**. Välj **+ Lägg till resurs** i kommandofältet.

    ![Välj lägg till resurs](media/data-box-edge-manage-shares/add-share-1.png)

2. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen.
    
    Resursnamn får bara innehålla siffror, gemener och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.

3. Välj en **typ** för resursen. Typen kan vara **SMB** eller **NFS**, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

4. Ange ett **Lagringskonto** där resursen ska placeras. En container skapas på lagringskontot med resursnamnet om containern inte redan finns. Om containern redan finns används den befintliga containern.

5. Välj **lagringstjänsten** från blockblob, sidblob eller filer i listrutan. Vilken typ av tjänst som väljs beror på vilket format du vill använda för data som lagras i Azure. I det här fallet vill vi till exempel att data ska finnas som blockblobar i Azure, därför väljer vi **Block Blob**. Om du väljer **Sidblobar**måste du se till att dina data är 512 byte justerade. Använd **sidblobb** för virtuella hårddiskar eller VHDX som alltid är 512 byte justerade.

   > [!IMPORTANT]
   > Kontrollera att Azure Storage-kontot som du använder inte har principer för oföränderlighet som angetts för det om du använder det med en Azure Stack Edge- eller Data Box Gateway-enhet. Mer information finns i [Ange och hantera principer för oföränderlighet för bloblagring](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

6. Det här steget beror på om du skapar en SMB- eller en NFS-resurs.
   - **Om du skapar en SMB-resurs** – I fältet för **lokal användare med fullständig behörighet** väljer du mellan **Skapa ny** eller **Använd befintlig**. Om du skapar en ny lokal användare anger du **användarnamn**, **lösenord** och sedan bekräfta lösenord. Detta tilldelar behörigheter till den lokala användaren. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.

      ![Lägga till SMB-resurs](media/data-box-edge-manage-shares/add-smb-share.png)

        Om du markerar alternativet för att endast tillåta läsåtgärder för dessa resursdata kan du ange skrivskyddade användare.
   - **Om du skapar en NFS-resurs** – Du måste ange **IP-adresserna för de tillåtna klienter** som har åtkomst till resursen.

      ![Lägga till NFS-resurs](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Använd den lokala monteringspunkten om du enkelt vill komma åt resurserna från Edge-beräkningsmoduler. Välj **Använd resursen med Edge-beräkning** så att resursen monteras automatiskt när den har skapats. När det här alternativet är markerat kan edge-modulen också använda beräkningen med den lokala monteringspunkten.

8. Klicka på **Skapa** för att skapa resursen. Du får ett meddelande om att resursen skapas. När resursen har skapats med de angivna inställningarna uppdateras bladet **Resurser** med den nya resursen.

## <a name="add-a-local-share"></a>Lägga till en lokal resurs

1. Gå till databoxkantsresursen i Azure-portalen och gå sedan till **Gateway > Shares**. Välj **+ Lägg till resurs** i kommandofältet.

   ![Välj lägg till resurs](media/data-box-edge-manage-shares/add-local-share-1.png)

2. I **Lägg till resurs** anger du inställningar för resursen. Ange ett unikt namn på resursen.
    
    Resursnamn får bara innehålla siffror, gemener och bindestreck. Resursnamnet måste vara mellan 3 och 63 tecken långt och börja med en bokstav eller en siffra. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck.

3. Välj en **typ** för resursen. Typen kan vara **SMB** eller **NFS**, där SMB är standardvärdet. SMB är standard för Windows-klienter och NFS används för Linux-klienter. Beroende på om du väljer SMB- eller NFS-resurser visas alternativen lite olika.

4. Använd den lokala monteringspunkten om du enkelt vill komma åt resurserna från Edge-beräkningsmoduler. Välj **Använd resursen med Edge-beräkning** så att Edge-modulen kan använda beräkningen med den lokala monteringspunkten.

5. Välj **Konfigurera som lokala edge-resurser**. Data i lokala resurser kommer att finnas kvar lokalt på enheten. Du kan bearbeta dessa data lokalt.

6. I fältet **Alla privilegier lokalt användarfält** väljer du **från Skapa nytt** eller Använd **befintligt**.

7. Välj **Skapa**. 

   ![Skapa lokal resurs](media/data-box-edge-manage-shares/add-local-share-2.png)

    Du ser ett meddelande om att skapande av resursen pågår. När resursen har skapats med de angivna inställningarna uppdateras bladet **Resurser** med den nya resursen.

   ![Visa uppdateringar Resurser blad](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Välj resursen om du vill visa den lokala monteringspunkten för Edge-beräkningsmodulerna för den här resursen.

   ![Visa information om lokal resurs](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Montera en resurs

Om du skapade en resurs innan du konfigurerade beräkning på databoxkantsenheten måste du montera resursen. Gör så här för att montera en resurs.

1. Gå till databoxkantsresursen i Azure-portalen och gå sedan till **Gateway > Shares**. Välj den resurs som du vill montera i listan över resurser. Kolumnen **Används för beräkning** visar statusen som **Inaktiverad** för den valda resursen.

   ![Välja resurs](media/data-box-edge-manage-shares/select-share-mount.png)

2. Välj **Montera**.

   ![Välj montering](media/data-box-edge-manage-shares/select-mount.png)

3. När du uppmanas att bekräfta väljer du **Ja**. Detta kommer att montera aktien.

   ![Bekräfta montering](media/data-box-edge-manage-shares/confirm-mount.png)

4. När aktien är monterad går du till listan över aktier. Du ser att kolumnen **Används för beräkning** visar resursstatusen som **Aktiverad**.

   ![Dela monterad](media/data-box-edge-manage-shares/share-mounted.png)

5. Välj resursen igen om du vill visa den lokala monteringspunkten för resursen. Edge-beräkningsmodul använder den här lokala monteringspunkten för resursen.

   ![Lokal monteringspunkt för resursen](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Avmontera en resurs

Gör följande steg i Azure-portalen för att avmontera en resurs.

1. Gå till databoxkantsresursen i Azure-portalen och gå sedan till **Gateway > Shares**.

   ![Välja resurs](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Välj den resurs som du vill avmontera i listan över resurser. Du vill vara säker på att resursen som du avmonterar inte används av några moduler. Om resursen används av en modul visas problem med motsvarande modul. Välj **Avmontera**.

   ![Välj avmontera](media/data-box-edge-manage-shares/select-unmount.png)

3. När du uppmanas att bekräfta väljer du **Ja**. Detta kommer att avmontera resursen.

   ![Bekräfta avmontera](media/data-box-edge-manage-shares/confirm-unmount.png)

4. När resursen har avmonterats går du till listan över aktier. Du ser att kolumnen **Används för beräkning** visar delningsstatusen som **Inaktiverad**.

   ![Resursen har inte monterats](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Ta bort en resurs

Utför följande steg på Azure-portalen om du vill ta bort en resurs.

1. Välj från listan över resurser, och klicka på den resurs som du vill ta bort.

   ![Välja resurs](media/data-box-edge-manage-shares/delete-share-1.png)

2. Klicka på **Ta bort**.

   ![Klicka på Ta bort](media/data-box-edge-manage-shares/delete-share-2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

   ![Bekräfta borttagning](media/data-box-edge-manage-shares/delete-share-3.png)

Listan över resurser uppdateras för att återspegla borttagningen.

## <a name="refresh-shares"></a>Uppdatera resurser

Med uppdateringsfunktionen kan du uppdatera innehållet i en resurs. När du uppdaterar en resurs initieras en sökning för att hitta alla Azure-objekt, inklusive blobar och filer som har lagts till i molnet sedan den senaste uppdateringen. Dessa ytterligare filer hämtas sedan för att uppdatera innehållet i resursen på enheten.

> [!IMPORTANT]
>
> - Du kan inte uppdatera lokala resurser.
> - Behörigheter och åtkomstkontrollistor (ACL: er) bevaras inte över en uppdateringsåtgärd. 

Utför följande steg på Azure-portalen om du vill uppdatera en resurs.

1. Gå till **Resurser** i Azure-portalen. Välj och klicka på den resurs som du vill uppdatera.

   ![Välja resurs](media/data-box-edge-manage-shares/refresh-share-1.png)

2. Klicka på **Uppdatera**.

   ![Klicka på Uppdatera](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Ett jobb startas för att uppdatera innehållet i den lokala resursen.

   ![Bekräfta uppdatering](media/data-box-edge-manage-shares/refresh-share-3.png)

4. Medan uppdateringen pågår nedtonas uppdateringsalternativet i snabbmenyn. Klicka på meddelandet för jobbet för att visa jobbstatus för uppdateringen.

5. Tiden för att uppdatera bestäms av antalet filer i Azure-containern samt filerna på enheten. När uppdateringen är klar uppdateras tidsstämpeln för resursen. Även om uppdateringen delvis har misslyckats anses åtgärden ha lyckats och tidsstämpeln uppdateras. Uppdateringsfelloggarna uppdateras också.

   ![Uppdaterad tidsstämpel](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Om ett fel uppstår genereras en avisering. Aviseringen innehåller detaljerad information om orsaken och rekommendationen för att åtgärda problemet. Aviseringen innehåller också en länk till en fil med en fullständig sammanfattning av felen, inklusive de filer som inte kunde uppdateras eller tas bort.

## <a name="sync-storage-keys"></a>Synkronisera lagringsnycklar

Om dina lagringskontonycklar har roterats måste du synkronisera åtkomstnycklarna för lagring. Synkroniseringen hjälper enheten att hämta de senaste nycklarna för ditt lagringskonto.

Utför följande steg i Azure-portalen för att synkronisera din lagringsåtkomstnyckel.

1. Gå till **Översikt** i din resurs. Välj från listan över resurser och klicka på en resurs som är associerad med det lagringskonto som du vill synkronisera.

    ![Välj resurs med relevant lagringskonto](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Klicka på **Synkronisera lagringsnyckel**. Klicka på **Ja** när du uppmanas att bekräfta.

     ![Välj synkroniseringslagringsnyckel](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Avsluta dialogrutan när synkroniseringen är klar.

>[!NOTE]
> Du behöver bara utföra åtgärden en gång för ett givet lagringskonto. Du behöver inte upprepa åtgärden för alla resurser som är associerade med samma lagringskonto.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](data-box-edge-manage-users.md).
