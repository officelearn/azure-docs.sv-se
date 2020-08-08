---
title: Installera språk paket på virtuella Windows 10-datorer i Windows Virtual Desktop – Azure
description: Så här installerar du språk paket för Windows 10-virtuella datorer med flera sessioner i Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 542163511a1b4fc0acde9b44d73be6ffc042d5d3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008771"
---
# <a name="install-language-packs"></a>Installera språkpaket

När du konfigurerar distributioner av virtuella Windows-datorer internationellt, är det en bra idé att se till att distributionen har stöd för flera språk. Du kan installera språk paket på en Windows 10 Enterprise-avbildning av virtuella datorer med flera sessioner för att stödja så många språk som din organisation behöver. I den här artikeln får du veta hur du installerar språk paket och avbildningar som låter användarna välja sina egna visnings språk.

Lär dig mer om hur du distribuerar en virtuell dator i Azure på [skapa en virtuell Windows-dator i en tillgänglighets zon med Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Den här artikeln gäller för virtuella datorer med Windows 10 Enterprise multi-session.

## <a name="install-a-language-pack"></a>Installera ett språk paket

Om du vill skapa en VM-avbildning med språk paket måste du först installera språk paket på en dator och avbilda en avbildning av den.

Installera språk paket:

1. Logga in som administratör.
2. Kontrol lera att du har installerat alla de senaste uppdateringarna för Windows och Windows Store.
3. Gå till **Inställningar**  >  **tid & språk**  >  **region**.
4. Under **land eller region**väljer du önskat land eller region i den nedrullningsbara menyn.
    I det här exemplet ska vi välja **Frankrike**, som du ser i följande skärm bild:

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av sidan region. Den region som valts är Frankrike.](media/region-page-france.png)

5. Sedan väljer du **språk**och sedan **Lägg till ett språk**. Välj det språk som du vill installera i listan och välj sedan **Nästa**.
6. När fönstret **Installera språk funktioner** öppnas markerar du kryss rutan **Installera språk paket och anger som mitt visnings språk för Windows**.
7. Välj **Installera**.
8. Om du vill lägga till flera språk samtidigt väljer du **Lägg till ett språk**och upprepar sedan processen för att lägga till ett språk i steg 5 och 6. Upprepa processen för varje språk som du vill installera. Du kan dock bara ange ett språk som visnings språk i taget.

    Vi går igenom en snabb visuell demonstration. Följande bilder visar hur du installerar de franska och nederländska språk paketen och anger franska som visnings språk.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av språk sidan i början av processen. Det valda visnings språket i Windows är engelska.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av fönstret för val av språk. Användaren har angett "franska" i Sök fältet för att hitta de franska språk paketen.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av sidan installations språk funktioner. Franska väljs som önskat språk. De valda alternativen är "ange mitt visnings språk", "installera språk paket", "tal igenkänning" och "hand skrift".](media/install-language-features.png)

    När språk paketen har installerats bör du se namnen på dina språk paket i listan över språk.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av språk sidan med nya språk paket installerade. De franska och nederländska språk paketen visas under "prioriterade språk".](media/language-page-complete.png)

9. Om ett fönster visas där du uppmanas att logga ut från din session. Logga ut och logga sedan in igen. Ditt visnings språk bör nu vara det språk som du har valt.

10.  Gå till **kontroll panelens**  >  **klocka och region**  >  **region**.

11.  När fönstret **region** öppnas, Välj fliken **Administration** och välj sedan **Kopiera inställningar**.

12.  Markera kryss rutorna **välkomst skärm och system konton** och **nya användar konton**.

13.  Välj **OK**.

14.  Ett fönster öppnas och du uppmanas att starta om sessionen. Välj **starta om nu**.

15.  När du har loggat in igen går du tillbaka till **kontroll panelens**  >  **klocka och region**  >  **region**.

16.  Välj fliken **Administration** .

17.  Välj **ändra system språk**.

18. På den nedrullningsbara menyn under **Aktuellt system språk**väljer du det språk som du vill använda. Sedan väljer du **OK**.

19. Välj **starta om nu** för att starta om sessionen en gång.

Grattis, du har installerat dina språk paket!

Innan du fortsätter kontrollerar du att systemet har de senaste versionerna av Windows och Windows Store installerade.

## <a name="sysprep"></a>Sysprep

Sedan måste du köra Sysprep-datorn för att förbereda den för avbildnings processen.

För att Sysprep datorn:

1. Öppna PowerShell som administratör.
2. Kör följande cmdlet för att gå till rätt katalog:

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Kör sedan följande cmdlet:

    ```powershell
    .\sysprep.exe
    ```

4. När fönstret system förberedelse verktyget öppnas markerar du kryss rutan **generalisera**och går sedan till **avstängnings alternativen** och väljer **Stäng** av på den nedrullningsbara menyn.

>[!NOTE]
>Syprep-processen tar några minuter att slutföra. Fjärrsessionen kommer att kopplas från när den virtuella datorn stängs av.

### <a name="resolve-sysprep-errors"></a>Lösa Sysprep-fel

Om du ser ett fel meddelande under Sysprep-processen gör du följande:

1. Öppna **enhet C** och gå till **Windows**  >  **system32 Sysprep**  >  -**Panther**och öppna **Setuperr** -filen.

   Texten i felfilen meddelar dig att du måste avinstallera ett särskilt språk paket, som du ser i följande bild. Kopiera språk paket namnet för nästa steg.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av Setuperr-filen. Texten med paket namnet är markerad med mörkt blått.](media/setuperr-package-name.png)

2. Öppna ett nytt PowerShell-fönster och kör följande cmdlet med det paket namn som du kopierade i steg 2 för att ta bort språk paketet:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Kontrol lera att du har tagit bort paketet genom att köra `Remove-AppxPackage` cmdleten igen. Om du har tagit bort paketet bör du se ett meddelande om att paketet som du försöker ta bort inte finns där.

4. Kör `sysprep.exe` cmdleten igen.

## <a name="capture-the-image"></a>Avbilda avbildningen

Nu när systemet är klart kan du skapa en avbildning så att andra användare kan börja använda virtuella datorer baserat på systemet utan att behöva upprepa konfigurations processen.

Så här avbildar du en avbildning:

1. Gå till Azure Portal och välj namnet på den dator som du konfigurerade i [Installera ett språk paket](#install-a-language-pack) och [Sysprep](#sysprep).

2. Välj **avbildning**.

3. Ange ett namn för avbildningen i fältet **namn** och tilldela den till resurs gruppen med hjälp av den nedrullningsbara menyn **resurs grupp** , som du ser i följande bild.

   > [!div class="mx-imgBorder"]
   > ![En skärm bild av fönstret Skapa bild. Namnet som användaren har gett till den här test avbildningen är "vmwvd-image-fr" och de har tilldelat till resurs gruppen "testwvdimagerg".](media/create-image.png)

4. Välj **Skapa**.

5. Vänta några minuter tills hämtningen är klar. När avbildningen är klar bör du se ett meddelande i meddelande centret som låter dig veta att avbildningen har fångats.

Nu kan du distribuera en virtuell dator med den nya avbildningen. När du distribuerar den virtuella datorn måste du följa anvisningarna i [skapa en virtuell Windows-dator i en tillgänglighets zon med Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Ändra visnings språk för standard användare

Standard användare kan ändra visnings språket på sina virtuella datorer.

Ändra visnings språk:

1. Gå till **språk inställningar**. Om du inte vet var det finns kan du ange **språk** i Sök fältet på Start menyn.

2. I list rutan visnings språk i Windows väljer du det språk som du vill använda som visnings språk.

3. Logga ut från din session och logga sedan in igen. Visnings språket bör nu vara det som du valde i steg 2.
