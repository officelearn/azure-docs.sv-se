---
title: Installera språkpaket på virtuella Windows 10-datorer i Windows Virtual Desktop – Azure
description: Så här installerar du språkpaket för virtuella datorer med flera sessioner i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c40df9d821e069e2cd5ff0c42d5841f6b9041c96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634040"
---
# <a name="install-language-packs"></a>Installera språkpaket

När du konfigurerar Windows Virtual Desktop-distributioner internationellt är det en bra idé att se till att distributionen stöder flera språk. Du kan installera språkpaket på en virtuell datoravbildning (Virtual Machine) för flera flera sessioner för att stödja så många språk som din organisation behöver. I den här artikeln får du lära dig hur du installerar språkpaket och tar bilder där användarna kan välja sina egna visningsspråk.

Läs mer om hur du distribuerar en virtuell dator i Azure på [Skapa en virtuell Windows-dator i en tillgänglighetszon med Azure-portalen](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Den här artikeln gäller windows 10 enterprise-virtuella datorer med flera sessioner.

## <a name="install-a-language-pack"></a>Installera ett språkpaket

Om du vill skapa en VM-avbildning med språkpaket måste du först installera språkpaket på en dator och ta en avbildning av den.

Så här installerar du språkpaket:

1. Logga in som administratör.
2. Kontrollera att du har installerat alla de senaste Windows- och Windows Store-uppdateringarna.
3. Gå till **Inställningstid** > **& språkregion** > **Region**.
4. Under **Land eller region**väljer du önskat land eller region på den nedrullningsbara menyn.
    I det här exemplet väljer vi **Frankrike,** vilket visas i följande skärmbild:

    ![En skärmbild av sidan Region. Den region som för närvarande väljs är Frankrike.](media/region-page-france.png)

5. Därefter väljer du **Språk**och väljer sedan **Lägg till ett språk**. Välj det språk som du vill installera i listan och välj sedan **Nästa**.
6. När fönstret **Installera språkfunktioner** öppnas markerar du kryssrutan **Installera språkpaket och anger som mitt Visningsspråk**i Windows .
7. Välj **Installera**.
8. Om du vill lägga till flera språk samtidigt väljer du **Lägg till ett språk**och upprepar sedan processen för att lägga till ett språk i steg 5 och 6. Upprepa den här processen för varje språk som du vill installera. Du kan dock bara ange ett språk som visningsspråk åt gången.

    Låt oss gå igenom en snabb visuell demonstration. Följande bilder visar hur du installerar de franska och nederländska språkpaketen och ställer sedan in franska som visningsspråk.

    ![En skärmbild av språksidan i början av processen. Det valda Windows-visningsspråket är engelska.](media/language-page-default.png)

    ![En skärmbild av språkvalsfönstret. Användaren har angett "franska" i sökfältet för att hitta de franska språkpaketen.](media/select-language-french.png)

    ![En skärmbild av sidan Installera språkfunktioner. Franska är valt som önskat språk. De valda alternativen är "Ange visningsspråket", "Installera språkpaket", "Taligenkänning" och "Handskrift".](media/install-language-features.png)

    När språkpaketen har installerats bör namnen på språkpaketen visas i listan med språk.

    ![En skärmbild av språksidan med de nya språkpaketen installerade. De franska och nederländska språkpaketen listas under "föredragna språk".](media/language-page-complete.png)

9. Om ett fönster visas där du uppmanas att logga ut från sessionen. Logga ut och logga sedan in igen. Ditt visningsspråk ska nu vara det språk du valt.

10.  Gå till > **Kontrollpanelens klocka och region** > **.** **Control Panel**

11.  När fönstret **Region** öppnas väljer du fliken **Administration** och väljer sedan **Kopiera inställningar**.

12.  Markera kryssrutorna med etiketten **Välkomstskärm och systemkonton** och **Nya användarkonton**.

13.  Välj **OK**.

14.  Ett fönster öppnas och talar om för dig att starta om sessionen. Välj **Starta om nu**.

15.  När du har loggat in igen går du tillbaka till > **Kontrollpanelens klocka och region** > **.** **Control Panel**

16.  Välj fliken **Administration.**

17.  Välj **Ändra systemlokal**.

18. I den nedrullningsbara menyn under **Aktuellt systemspråk**väljer du det språkspråk du vill använda. Därefter väljer du **OK**.

19. Välj **Starta om nu** om du vill starta om sessionen igen.

Grattis, du har installerat dina språkpaket!

Innan du fortsätter kontrollerar du att systemet har de senaste versionerna av Windows och Windows Store installerade.

## <a name="sysprep"></a>Sysprep

Därefter måste du sysprep din maskin för att förbereda den för bilden fånga processen.

Så här sysprep din maskin:

1. Öppna PowerShell som administratör.
2. Kör följande cmdlet för att gå till rätt katalog:
   
    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Kör sedan följande cmdlet:
    
    ```powershell
    .\sysprep.exe
    ```

4. När fönstret Systemförberedelseverktyg öppnas markerar du kryssrutan **Generalize**, går sedan till **Avstängningsalternativ** och väljer **Stäng av** på den nedrullningsbara menyn.

>[!NOTE]
>Syprep-processen tar några minuter att slutföra. När den virtuella datorn stängs av kopplas fjärrsessionen från.

### <a name="resolve-sysprep-errors"></a>Lösa sysprep-fel

Om du ser ett felmeddelande under sysprep-processen bör du göra följande:

1. Öppna **Enhet C** och gå till **Windows** > **System32 Sysprep** > **Panther**och öppna sedan **setuperr-filen.**

   Texten i felfilen talar om för dig att du måste avinstallera ett visst språkpaket, som visas i följande bild. Kopiera språkpaketets namn för nästa steg.

   ![En skärmbild av setuperr-filen. Texten med paketnamnet är markerad i mörkblått.](media/setuperr-package-name.png)

2. Öppna ett nytt PowerShell-fönster och kör följande cmdlet med paketnamnet som du kopierade i steg 2 för att ta bort språkpaketet:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Kontrollera att du har tagit bort paketet `Remove-AppxPackage` genom att köra cmdleten igen. Om du har tagit bort paketet bör du se ett meddelande om att paketet du försöker ta bort inte finns där.

4. Kör `sysprep.exe` cmdleten igen.

## <a name="capture-the-image"></a>Ta bilden

Nu när systemet är klart kan du ta en avbildning så att andra användare kan börja använda virtuella datorer baserat på ditt system utan att behöva upprepa konfigurationsprocessen.

Så här tar du en bild:

1. Gå till Azure-portalen och välj namnet på den dator som du konfigurerade i [Installera ett språkpaket](#install-a-language-pack) och [sysprep](#sysprep).

2. Välj **Fånga**.

3. Ange ett namn på bilden i fältet **Namn** och tilldela den till resursgruppen med hjälp av listrutan **Resursgrupp,** som visas i följande bild.

   ![En skärmbild av fönstret Skapa bild. Namnet som användaren har gett till den här testavbildningen är "vmwvd-image-fr", och de har tilldelat den till resursgruppen "testwvdimagerg".](media/create-image.png)

4. Välj **Skapa**.

5. Vänta några minuter innan insamlingsprocessen är klar. När bilden är klar bör du se ett meddelande i Meddelandecenter där du meddelar att bilden togs.

Du kan nu distribuera en virtuell dator med den nya avbildningen. När du distribuerar den virtuella datorn måste du följa instruktionerna i [Skapa en virtuell Windows-dator i en tillgänglighetszon med Azure-portalen](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Så här ändrar du visningsspråk för standardanvändare

Standardanvändare kan ändra visningsspråket på sina virtuella datorer.

Så här ändrar du visningsspråket:

1. Gå till **Språkinställningar**. Om du inte vet var det är kan du ange **Språk** i sökfältet på Start-menyn.

2. På den nedrullningsbara menyn För Visningsspråk i Windows väljer du det språk som du vill använda som visningsspråk.

3. Logga ut från sessionen och logga sedan in igen. Visningsspråket ska nu vara det du valde i steg 2.
