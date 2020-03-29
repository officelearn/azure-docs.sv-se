---
title: PowerShell för hantering av StorSimple-enheter
description: Lär dig hur du använder Windows PowerShell för StorSimple för att hantera din StorSimple-enhet.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7d59f00d655bc7b2395c46713a56f52c61ffa42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277112"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Använda Windows PowerShell för StorSimple för att administrera din enhet

## <a name="overview"></a>Översikt

Windows PowerShell för StorSimple tillhandahåller ett kommandoradsgränssnitt som du kan använda för att hantera din Microsoft Azure StorSimple-enhet. Som namnet antyder är det ett Windows PowerShell-baserat kommandoradsgränssnitt som är byggt i ett begränsat runspace. Ur användarens perspektiv på kommandoraden visas ett begränsat runspace som en begränsad version av Windows PowerShell. Samtidigt som du behåller några av de grundläggande funktionerna i Windows PowerShell har det här gränssnittet ytterligare dedikerade cmdlets som är inriktade på att hantera din Microsoft Azure StorSimple-enhet.

I den här artikeln beskrivs funktionerna i Windows PowerShell för StorSimple, inklusive hur du kan ansluta till det här gränssnittet, och länkar till steg-för-steg-procedurer eller arbetsflöden som du kan utföra med det här gränssnittet. Arbetsflödena omfattar hur du registrerar enheten, konfigurerar nätverksgränssnittet på enheten, installerar uppdateringar som kräver att enheten är i underhållsläge, ändrar enhetens tillstånd och felsöker eventuella problem som kan uppstå.

Efter att ha läst den här artikeln kommer du att kunna:

* Anslut till Din StorSimple-enhet med Windows PowerShell för StorSimple.
* Administrera Din StorSimple-enhet med Windows PowerShell för StorSimple.
* Få hjälp i Windows PowerShell för StorSimple.

> [!NOTE]
> * Med Windows PowerShell för StorSimple-cmdlets kan du hantera din StorSimple-enhet från en seriell konsol eller på distans via Windows PowerShell-ommotning. Mer information om var och en av de enskilda cmdlets som kan användas i det här gränssnittet finns i [cmdlet-referensen för Windows PowerShell för StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Azure PowerShell StorSimple-cmdlets är en annan samling cmdlets som gör att du kan automatisera StorSimple-tjänstnivå och migreringsuppgifter från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple finns i [Azure StorSimple cmdlet-referensen](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Du kan komma åt Windows PowerShell för StorSimple med någon av följande metoder:

* [Ansluta till Seriekonsolen StorSimple-enheten](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Ansluta på distans till StorSimple med Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ansluta till Windows PowerShell för StorSimple via enhetens seriekonsol

Du kan [ladda ner PuTTY](https://www.putty.org/) eller liknande terminal emulering programvara för att ansluta till Windows PowerShell för StorSimple. Du måste konfigurera PuTTY specifikt för att komma åt Microsoft Azure StorSimple-enheten. Följande avsnitt innehåller detaljerade steg om hur du konfigurerar PuTTy och ansluter till enheten. Olika menyalternativ i seriekonsolen förklaras också.

### <a name="putty-settings"></a>PuTTY-inställningar

Kontrollera att du använder följande PuTTY-inställningar för att ansluta till Windows PowerShell-gränssnittet från seriekonsolen.

#### <a name="to-configure-putty"></a>Så här konfigurerar du PuTTY

1. Välj **Tangentbord**i fönstret Kategori i dialogrutan **Omkonfigurering** i dialogrutan **PuTTY** Omkonfiguration .
2. Kontrollera att följande alternativ är markerade (dessa är standardinställningarna när du startar en ny session).
   
   | Tangentbordsobjekt | Välj |
   | --- | --- |
   | Backstegstangenten |Kontroll-? (127) |
   | Hem- och sluttangenter |Standard |
   | Funktionstangenter och knappsats |ESC[n~ |
   | Starttillstånd för markörtangenter |Normal |
   | Ursprungligt tillstånd för numeriskt tangentbord |Normal |
   | Aktivera extra tangentbordsfunktioner |Control-Alt skiljer sig från AltGr |
   
    ![Putty-inställningar som stöds](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klicka på **Använd**.
4. Välj **Översättning**i **kategorifönstret** .
5. Välj **UTF-8**i listrutan **Fjärrteckenuppsättning** .
6. **Under Hantering av radrittecken**väljer du Använd **Unicode-radritningskodpunkter**. Följande skärmdump visar rätt PuTTY val.
   
    ![UTF Putty-inställningar](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klicka på **Använd**.

Du kan nu använda PuTTY för att ansluta till enhetens seriekonsol genom att göra följande steg.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Om seriekonsolen

När du öppnar Windows PowerShell-gränssnittet för Din StorSimple-enhet via seriekonsolen visas ett bannermeddelande följt av menyalternativ.

Bannermeddelandet innehåller grundläggande StorSimple-enhetsinformation som modell, namn, installerad programvaruversion och status för den styrenhet som du har åtkomst till. Följande bild visar ett exempel på ett banderollmeddelande.

![Meddelande om seriell banderoll](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Du kan använda banderollmeddelandet för att identifiera om handkontrollen du är ansluten till är _Aktiv_ eller _Passiv_.

Följande bild visar de olika runspace-alternativ som är tillgängliga på menyn för seriella konsoler.

![Registrera enheten 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Du kan välja mellan följande inställningar:

1. **Logga in med full åtkomst** Med det här alternativet kan du ansluta (med rätt autentiseringsuppgifter) till **SSAdminConsole-runspace** på den lokala styrenheten. (Den lokala handkontrollen är den handkontroll som du för närvarande använder via den seriella konsolen på din StorSimple-enhet.) Det här alternativet kan också användas för att tillåta Microsoft Support att komma åt obegränsad körning (en supportsession) för att felsöka eventuella enhetsproblem. När du har använt alternativ 1 för att logga in kan du tillåta Microsoft Support-teknikern att komma åt obegränsat runspace genom att köra en viss cmdlet. Mer information finns i [Starta en supportsession](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Logga in på peer controller med full åtkomst** Det här alternativet är detsamma som alternativ 1, förutom att du kan ansluta (med rätt autentiseringsuppgifter) till **SSAdminConsole-runspacen** på peer-styrenheten. Eftersom StorSimple-enheten är en enhet med hög tillgänglighet med två styrenheter i en aktiv-passiv konfiguration refererar peer till den andra styrenheten i enheten som du använder via seriekonsolen).
   I likhet med alternativ 1 kan det här alternativet också användas för att tillåta Microsoft Support att komma åt obegränsat runspace på en peer-styrenhet.

3. **Anslut med begränsad åtkomst** Det här alternativet används för att komma åt Windows PowerShell-gränssnittet i begränsat läge. Du uppmanas inte att ange åtkomstautentiseringsuppgifter. Det här alternativet ansluter till ett mer begränsat runspace jämfört med alternativ 1 och 2.  Några av de uppgifter som är tillgängliga via alternativ 1 som **inte kan* utföras i det här runspace är:
   
   * Återställ till fabriksinställningarna
   * Ändra lösenordet
   * Aktivera eller inaktivera supportåtkomst
   * Tillämpa uppdateringar
   * Installera snabbkorrigeringar

     > [!NOTE]
     > Detta är det alternativ som föredras om du har glömt lösenordet till enhetsadministratören och inte kan ansluta via alternativ 1 eller 2.

4. **Ändra språk** Med det här alternativet kan du ändra visningsspråket i Windows PowerShell-gränssnittet. De språk som stöds är engelska, japanska, ryska, franska, sydkoreanska, spanska, italienska, tyska, kinesiska och brasiliansk portugisiska.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Ansluta på distans till StorSimple med Windows PowerShell för StorSimple

Du kan använda Windows PowerShell-omstyrning för att ansluta till Din StorSimple-enhet. När du ansluter på det här sättet visas ingen meny. (Du ser bara en meny om du använder den seriella konsolen på enheten för att ansluta. När du fjärransluter att fjärransluta till motsvarigheten till "alternativ 1 – full åtkomst" på seriekonsolen.) Med Windows PowerShell-ommotning ansluter du till ett visst runspace. Du kan också ange visningsspråket.

Visningsspråket är oberoende av vilket språk du anger med alternativet **Ändra språk** på menyn seriell konsol. Remote PowerShell hämtar automatiskt språken för den enhet som du ansluter från om ingen har angetts.

> [!NOTE]
> Om du arbetar med virtuella Microsoft Azure-värdar och StorSimple Cloud Appliances kan du använda Windows PowerShell-ommotning och den virtuella värden för att ansluta till molninstallationen. Om du har konfigurerat en delningsplats på värden där du vill spara information från Windows PowerShell-sessionen bör du vara medveten om att huvudmannen _Alla_ endast innehåller autentiserade användare. Om du har ställt in resursen för att tillåta åtkomst för _alla_ och du ansluter utan att ange autentiseringsuppgifter, kommer därför det oautentiserade anonyma huvudmannen att användas och du kommer att se ett fel. För att åtgärda problemet måste du på resursvärden aktivera gästkontot och sedan ge gästkontot fullständig åtkomst till resursen eller ange giltiga autentiseringsuppgifter tillsammans med Windows PowerShell-cmdleten.


Du kan använda HTTP eller HTTPS för att ansluta via Windows PowerShell-ommotning. Använd instruktionerna i följande självstudier:

* [Ansluta på distans med HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Ansluta på distans med HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Säkerhetsaspekter för anslutningar

När du bestämmer hur du ansluter till Windows PowerShell för StorSimple bör du tänka på följande:

* Det är säkert att ansluta direkt till enhetens seriekonsol, men det är inte säkert att ansluta till den seriella konsolen via nätverksväxlar. Var försiktig med säkerhetsrisken när du ansluter till enheten seriella över nätverksväxlar.
* Att ansluta via en HTTP-session kan ge större säkerhet än att ansluta via seriekonsolen via nätverket. Även om detta inte är den säkraste metoden är det acceptabelt i betrodda nätverk.
* Att ansluta via en HTTPS-session är det säkraste och rekommenderade alternativet.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrera Din StorSimple-enhet med Windows PowerShell för StorSimple

I följande tabell visas en sammanfattning av alla vanliga hanteringsuppgifter och komplexa arbetsflöden som kan utföras inom Windows PowerShell-gränssnittet på Din StorSimple-enhet. Om du vill ha mer information om varje arbetsflöde klickar du på lämplig post i tabellen.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell för StorSimple-arbetsflöden

| Om du vill göra detta ... | Använd den här proceduren. |
| --- | --- |
| Registrera din enhet |[Konfigurera och registrera enheten med Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurera webbproxy</br>Visa inställningar för webbproxy |[Konfigurera webbproxy för StorSimple-enheten](storsimple-8000-configure-web-proxy.md) |
| Ändra inställningar för DATA 0-nätverksgränssnitt på enheten |[Ändra DATA 0-nätverksgränssnittet för StorSimple-enheten](storsimple-8000-modify-data-0.md) |
| Stoppa en handkontroll </br> Starta om eller stänga av en styrenhet </br> Stänga av en enhet</br>Återställa enheten till fabriksinställningarna |[Hantera enhetsstyrenheter](storsimple-8000-manage-device-controller.md) |
| Installera uppdateringar och snabbkorrigeringar för underhållsläge |[Uppdatera din enhet](storsimple-update-device.md) |
| Ange underhållsläge </br>Avsluta underhållsläge |[Lägen för StorSimple-enhet](storsimple-8000-device-modes.md) |
| Skapa ett supportpaket</br>Dekryptera och redigera ett supportpaket |[Skapa och hantera ett supportpaket](storsimple-8000-create-manage-support-package.md) |
| Starta en supportsession</br> |[Starta en supportsession i Windows PowerShell för StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Få hjälp i Windows PowerShell för StorSimple

I Windows PowerShell för StorSimple finns cmdlet-hjälpen tillgänglig. Det finns också en onlineversion av den här hjälpen som du kan använda för att uppdatera hjälpen på datorn.

Att få hjälp i det här gränssnittet liknar det i Windows PowerShell, och de flesta hjälprelaterade cmdlets fungerar. Du kan hitta hjälp för Windows PowerShell online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Följande är en kort beskrivning av vilka typer av hjälp för det här Windows PowerShell-gränssnittet, inklusive hur du uppdaterar hjälpen.

### <a name="to-get-help-for-a-cmdlet"></a>För att få hjälp med en cmdlet

* Använd följande kommando om du vill ha hjälp med en cmdlet eller -funktion:`Get-Help <cmdlet-name>`
* Om du vill ha onlinehjälp för alla cmdlet `-Online` använder du den tidigare cmdleten med parametern:`Get-Help <cmdlet-name> -Online`
* För fullständig hjälp kan `–Full` du använda parametern och `–Examples` exempel använda parametern.

### <a name="to-update-help"></a>Så här uppdaterar du hjälpen

Du kan enkelt uppdatera hjälpen i Windows PowerShell-gränssnittet. Gör följande för att uppdatera hjälpen på datorn.

#### <a name="to-update-cmdlet-help"></a>Så här uppdaterar du cmdlet-hjälpen
1. Starta Windows PowerShell med alternativet **Kör som administratör.**
2. Skriv i kommandotolken:`Update-Help`
3. De uppdaterade hjälpfilerna kommer att installeras.
4. När hjälpfilerna har installerats skriver du: `Get-Help Get-Command`. Då visas en lista över cmdletar för vilka hjälp är tillgänglig.

> [!NOTE]
> Om du vill få en lista över alla tillgängliga cmdlets i ett `Get-Command` runspace loggar du in på motsvarande menyalternativ och kör cmdleten.


## <a name="next-steps"></a>Nästa steg

Om du får problem med Din StorSimple-enhet när du utför något av ovanstående arbetsflöden läser du [Verktyg för felsökning av StorSimple-distributioner](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

