---
title: PowerShell för StorSimple-enhetshantering | Microsoft Docs
description: Lär dig hur du använder Windows PowerShell för StorSimple för att hantera din StorSimple-enhet.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 564c121aa90746498a94022fd0fb8d8529142c91
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698406"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Använda Windows PowerShell för StorSimple för att administrera din enhet

## <a name="overview"></a>Översikt

Windows PowerShell för StorSimple tillhandahåller ett kommandoradsgränssnitt som du kan använda för att hantera din Microsoft Azure StorSimple-enhet. Som namnet antyder är en Windows PowerShell-baserat kommandoradsgränssnitt som är inbyggd i ett begränsat körningsutrymme. Perspektiv för användare på kommandoraden visas ett begränsat körningsutrymme som en begränsad version av Windows PowerShell. Samtidigt några av de grundläggande funktionerna i Windows PowerShell, har det här gränssnittet ytterligare dedicerade cmdlets som är avsedda för att hantera din Microsoft Azure StorSimple-enhet.

Den här artikeln beskriver Windows PowerShell för StorSimple-funktioner, inklusive hur du kan ansluta till det här gränssnittet och innehåller länkar till stegvisa procedurer eller arbetsflöden som du kan utföra med hjälp av det här gränssnittet. Arbetsflöden är bland annat hur du registrerar din enhet, konfigurera nätverksgränssnittet på din enhet, installera uppdateringar som enheten måste vara i underhållsläge, ändra enhetens tillstånd och felsöka eventuella problem som kan uppstå.

När du har läst den här artikeln kommer du att kunna:

* Anslut till din StorSimple-enhet med hjälp av Windows PowerShell för StorSimple.
* Administrera din StorSimple-enhet med hjälp av Windows PowerShell för StorSimple.
* Få hjälp i Windows PowerShell för StorSimple.

> [!NOTE]
> * Windows PowerShell för StorSimple cmdlets kan du hantera din StorSimple-enhet från en seriell konsol eller via en fjärranslutning via Windows PowerShell-fjärrkommunikation. Mer information om var och en av de enskilda cmdletar som kan användas i det här gränssnittet går du till [cmdlet-referens för Windows PowerShell för StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * StorSimple för Azure PowerShell-cmdlets finns en annan uppsättning cmdletar som om du vill automatisera StorSimple tjänstnivå och migreringen från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple går du till den [cmdlet-referens för Azure StorSimple](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Du kan komma åt Windows PowerShell för StorSimple med någon av följande metoder:

* [Ansluta till StorSimple-enhetens seriekonsol](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Fjärransluta till StorSimple med hjälp av Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ansluta till Windows PowerShell för StorSimple via enhetens seriekonsol

Du kan [ladda ned PuTTY](https://www.putty.org/) eller liknande programvara för terminalemulering att ansluta till Windows PowerShell för StorSimple. Du måste konfigurera PuTTY speciellt utformade för att få åtkomst till Microsoft Azure StorSimple-enheten. Följande avsnitt innehåller detaljerade anvisningar om hur du konfigurerar PuTTy och ansluta till enheten. Olika menyalternativen i seriekonsolen beskrivs också.

### <a name="putty-settings"></a>PuTTY-inställningar

Se till att du använder följande inställningar för PuTTY för att ansluta till Windows PowerShell-gränssnittet från seriell konsol.

#### <a name="to-configure-putty"></a>Konfigurera PuTTY

1. I PuTTY **omkonfiguration** i dialogrutan den **kategori** väljer **tangentbord**.
2. Kontrollera att följande alternativ är valt (detta är standardinställningarna när du startar en ny session).
   
   | Tangentbord objekt | Välj |
   | --- | --- |
   | BACKSTEG |Control-? (127) |
   | Start- och slutdatum nycklar |Standard |
   | Funktionstangenter och tangentbordet |ESC[n~ |
   | Ursprungligt tillstånd för markören nycklar |Normal |
   | Ursprungligt tillstånd för numeriska tangentbordet |Normal |
   | Aktivera funktioner för extra tangentbord |CTRL + ALT + skiljer sig från AltGr |
   
    ![Putty-inställningar som stöds](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klicka på **Verkställ**.
4. I den **kategori** väljer **Translation**.
5. I den **Remote teckenuppsättningen** väljer **UTF-8**.
6. Under **hantering av Linjeteckning tecken**väljer **Använd Unicode Linjeteckning kodpunkter**. Följande skärmbild visar rätt PuTTY val.
   
    ![UTF Putty-inställningar](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klicka på **Verkställ**.

Du kan nu använda PuTTY för att ansluta till enhetens seriekonsol genom att göra följande steg.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Om seriekonsolen

När du använder Windows PowerShell-gränssnittet för StorSimple-enheten via seriekonsolen Banderollmeddelandet visas, följt av menyalternativ.

Banderollmeddelandet innehåller grundläggande StorSimple-enhetsinformation som modell, namn, version installerad programvara och status för den domänkontrollant som du ansluter till. Följande bild visar ett exempel på en Banderollmeddelandet.

![Seriell Banderollmeddelandet](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Du kan använda Banderollmeddelandet för att identifiera om den domänkontrollant som du är ansluten till är _Active_ eller _passiva_.

Följande bild visar de olika alternativen för körningsutrymme som är tillgängliga i menyn för seriekonsolen.

![Registrera din enhet 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Du kan välja mellan följande inställningar:

1. **Logga in med fullständig åtkomst** det här alternativet kan du ansluta (med korrekta autentiseringsuppgifter) till den **SSAdminConsole** körningsutrymme på den lokala styrenheten. (Den lokala domänkontrollanten är den styrenhet som du för närvarande kommer åt via seriekonsolen för StorSimple-enheten.) Det här alternativet kan också användas för att tillåta Microsoft Support till obegränsad körningsutrymme (en supportsession) för att felsöka eventuella problem med möjliga enheter. När du använder alternativ 1 för att logga in, kan Microsoft Support-tekniker för att få åtkomst till obegränsad körningsutrymme genom att köra en viss cmdlet. Mer information finns att [starta en supportsession](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Logga in till peer-styrenhet med fullständig åtkomst** det här alternativet är detsamma som alternativ 1, förutom att du kan ansluta (med korrekta autentiseringsuppgifter) till den **SSAdminConsole** körningsutrymme på peer-styrenheten. Eftersom StorSimple-enheten är en hög tillgänglighet med två domänkontrollanter i en aktiv-passiv konfiguration refererar peer till den andra styrenheten i den enhet som du kommer åt via seriekonsolen).
   Liknande alternativ 1, det här alternativet kan också användas för att tillåta Microsoft Support till obegränsad runspace på en peer-domänkontrollant.

3. **Ansluta med begränsad åtkomst** det här alternativet används för att komma åt Windows PowerShell-gränssnittet i begränsat läge. Du ombeds inte ange autentiseringsuppgifter. Det här alternativet ansluter till ett mer begränsat körningsutrymme jämfört med alternativ 1 och 2.  Några av de uppgifter som är tillgängliga via alternativ 1 som **kan* utföras i den här körningsutrymme är:
   
   * Återställa till fabriksinställningarna
   * Ändra lösenordet
   * Aktivera eller inaktivera tillgången till support
   * Tillämpa uppdateringar
   * Installera snabbkorrigeringar

     > [!NOTE]
     > Detta är alternativet som rekommenderas om du har glömt administratörslösenord för enheten och kan inte ansluta via 1 eller 2.

4. **Ändra språk** det här alternativet kan du ändra visningsspråket på Windows PowerShell-gränssnittet. De språk som stöds är engelska, japanska, ryska, franska, Sydkorea, spanska, italienska, tyska, kinesiska och portugisiska (Brasilien).

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Fjärransluta till StorSimple med hjälp av Windows PowerShell för StorSimple

Du kan använda Windows PowerShell-fjärrkommunikation för att ansluta till din StorSimple-enhet. När du ansluter på så sätt visas inte en meny. (Du se en meny bara om du använder seriekonsolen på enheten för att ansluta. Fjärransluta kommer du direkt till motsvarande ”alternativ 1 – fullständig åtkomst” på seriekonsolen.) Med Windows PowerShell-fjärrkommunikation ansluta till en specifik körningsutrymme. Du kan också ange språk för visning.

Visningsspråket är oberoende av det språk som du anger med hjälp av den **ändra språk** alternativ i seriemenyn för konsolen. Fjärr-PowerShell avläsa automatiskt de nationella inställningarna på enheten du ansluter från om inget anges.

> [!NOTE]
> Om du arbetar med Microsoft Azure virtuella värdar och StorSimple Cloud Appliances kan använda du Windows PowerShell-fjärrkommunikation och den virtuella värden för att ansluta till molninstallationen. Om du har konfigurerat en plats på värden som du vill spara informationen från Windows PowerShell-session, bör du vara medveten som den _alla_ huvudnamn innehåller endast autentiserade användare. Därför, om du har lagt upp resursen för att tillåta åtkomst av _alla_ och du ansluta utan att ange autentiseringsuppgifter, oautentiserade anonym huvudnamn används och visas ett felmeddelande. Om du vill åtgärda problemet på resursen värd som du måste aktivera gästkontot och ge sedan gästkontot konto fullständig åtkomst till resursen eller du måste ange giltiga autentiseringsuppgifter tillsammans med Windows PowerShell-cmdleten.


Du kan använda HTTP eller HTTPS för att ansluta via Windows PowerShell-fjärrkommunikation. Följ instruktionerna i följande Självstudier:

* [Anslut via en fjärranslutning med HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Ansluta via en fjärranslutning med hjälp av HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Säkerhetsöverväganden för anslutning

Tänk på följande när du bestämmer hur du ansluter till Windows PowerShell för StorSimple:

* Ansluta direkt till enhetens seriekonsol är säkra, men är ansluta till seriekonsol nätverksväxlar inte. Se upp för säkerhetsrisken när du ansluter till enheten seriell över nätverksväxlar.
* Ansluta via en HTTP-session kan erbjuda mer säkerhet än att ansluta via seriekonsolen över nätverket. Även om detta inte är den säkraste metoden är det acceptabelt på betrodda nätverk.
* Ansluta via en HTTPS-session är den säkraste och det rekommenderade alternativet.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrera din StorSimple-enhet med hjälp av Windows PowerShell för StorSimple

I följande tabell visas en sammanfattning av alla vanliga administrationsuppgifter och komplexa arbetsflöden som kan utföras i Windows PowerShell-gränssnittet för StorSimple-enheten. Mer information om varje arbetsflöde klickar du på lämplig posten i tabellen.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell för StorSimple-arbetsflöden

| Om du vill göra detta... | Använd den här proceduren. |
| --- | --- |
| Registrera din enhet |[Konfigurera och registrera enheten med hjälp av Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurera webbproxy</br>Visa Webbproxyinställningar |[Konfigurera webbproxy för din StorSimple-enhet](storsimple-8000-configure-web-proxy.md) |
| Ändra DATA 0 inställningar för nätverksgränssnittet på din enhet |[Ändra DATA 0-nätverksgränssnittet för din StorSimple-enhet](storsimple-8000-modify-data-0.md) |
| Stoppa en kontrollant </br> Starta om eller stänga av en domänkontrollant </br> Stänga av en enhet</br>Återställa enheten till fabriksinställningarna |[Hantera styrenheter](storsimple-8000-manage-device-controller.md) |
| Installera uppdateringar av underhållsläge och snabbkorrigeringar |[Uppdatera din enhet](storsimple-update-device.md) |
| Ange underhållsläge </br>Avsluta underhållsläget |[Lägen för StorSimple-enhet](storsimple-8000-device-modes.md) |
| Skapa ett supportpaket</br>Dekryptera och redigera ett supportpaket |[Skapa och hantera ett supportpaket](storsimple-8000-create-manage-support-package.md) |
| Starta en supportsession</br> |[Starta en supportsession i Windows PowerShell för StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Få hjälp i Windows PowerShell för StorSimple

I Windows PowerShell för StorSimple är cmdlet hjälp tillgänglig. En aktuell version av den här hjälpen finns även tillgänglig, som du kan använda för att uppdatera hjälp på datorn.

Få hjälp i det här gränssnittet är ungefär som i Windows PowerShell och de flesta av hjälp-relaterade cmdlets fungerar. Du hittar hjälp för Windows PowerShell online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Här följer en kort beskrivning av typerna av hjälp för det här Windows PowerShell-gränssnittet, inklusive hur du uppdaterar hjälp.

### <a name="to-get-help-for-a-cmdlet"></a>Att få hjälp för en cmdlet

* Om du behöver hjälp för cmdleten eller funktionen, använder du följande kommando: `Get-Help <cmdlet-name>`
* Om du vill få hjälp online för alla cmdletar, använder du cmdleten tidigare med den `-Online` parameter: `Get-Help <cmdlet-name> -Online`
* Fullständig hjälp du kan använda den `–Full` parametern och exempel, använder de `–Examples` parametern.

### <a name="to-update-help"></a>Uppdatera hjälp

Du kan enkelt uppdatera hjälp i Windows PowerShell-gränssnittet. Utför följande steg om du vill uppdatera hjälp på datorn.

#### <a name="to-update-cmdlet-help"></a>Uppdatera cmdlet hjälp
1. Starta Windows PowerShell med den **kör som administratör** alternativet.
2. I Kommandotolken skriver du:  `Update-Help`
3. De uppdaterade hjälpfilerna kommer att installeras.
4. När hjälpfilerna är installerade, skriver du: `Get-Help Get-Command`. Detta visar en lista över cmdlets som hjälp är tillgänglig.

> [!NOTE]
> Om du vill hämta en lista över tillgängliga cmdlets i ett körningsutrymme, logga in på motsvarande menyalternativ och köra den `Get-Command` cmdlet.


## <a name="next-steps"></a>Nästa steg

Om du får problem med din StorSimple-enhet när du utför en av de ovanstående arbetsflödena, se [verktyg vid felsökning av StorSimple-distributioner](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

