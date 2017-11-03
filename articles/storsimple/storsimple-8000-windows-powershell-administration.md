---
title: "PowerShell för StorSimple-enhetshantering | Microsoft Docs"
description: "Lär dig hur du använder Windows PowerShell för StorSimple för att hantera din StorSimple-enhet."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/03/2017
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 89e1054117f19e787da5330932021351fb016209
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Använda Windows PowerShell för StorSimple för att administrera din enhet

## <a name="overview"></a>Översikt

Windows PowerShell för StorSimple innehåller ett kommandoradsgränssnitt som du kan använda för att hantera din Microsoft Azure StorSimple-enhet. Som namnet antyder är ett kommandoradsgränssnitt för Windows PowerShell-baserade, som är inbyggd i ett begränsat körningsutrymme. Ur ett användare på kommandoraden visas ett begränsat körningsutrymme som en begränsad version av Windows PowerShell. Det här gränssnittet har ytterligare dedikerad cmdlets som är inriktad på att hantera din Microsoft Azure StorSimple-enhet utan att några av de grundläggande funktionerna i Windows PowerShell.

Den här artikeln beskriver Windows PowerShell för StorSimple-funktioner, inklusive hur du kan ansluta till det här gränssnittet och innehåller länkar till stegvisa anvisningar eller arbetsflöden som du kan utföra med hjälp av det här gränssnittet. Arbetsflöden är hur du registrerar din enhet, konfigurera nätverksgränssnittet på din enhet, installera uppdateringar som kräver att enheten är i underhållsläge, ändra enhetens tillstånd och felsöka eventuella problem som kan uppstå.

När du har läst den här artikeln kommer du att kunna:

* Ansluta till din StorSimple-enhet med Windows PowerShell för StorSimple.
* Administrera din StorSimple-enhet med Windows PowerShell för StorSimple.
* Få hjälp i Windows PowerShell för StorSimple.

> [!NOTE]
> * Windows PowerShell för StorSimple-cmdlets kan du hantera din StorSimple-enhet från en seriekonsolen eller via fjärranslutning via Windows PowerShell-fjärrkommunikation. Mer information om var och en av de enskilda cmdletar som kan användas i det här gränssnittet går du till [cmdlet-referens för Windows PowerShell för StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Azure PowerShell StorSimple-cmdlets är en annan uppsättning cmdlets som gör att du kan automatisera StorSimple servicenivå och uppgifter för migrering från kommandoraden. Mer information om Azure PowerShell-cmdlets för StorSimple, gå till den [cmdlet-referens för Azure StorSimple](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple).


Du kan komma åt Windows PowerShell för StorSimple med någon av följande metoder:

* [Ansluta till StorSimple-enhetens seriekonsol](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Fjärransluta till StorSimple med Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ansluta till Windows PowerShell för StorSimple via enhetens seriekonsol

Du kan [hämta PuTTY](http://www.putty.org/) eller liknande programvara för terminalemulering att ansluta till Windows PowerShell för StorSimple. Du måste konfigurera PuTTY specifikt för att få åtkomst till Microsoft Azure StorSimple-enheten. Följande avsnitt innehåller detaljerade anvisningar om hur du konfigurerar PuTTy och ansluta till enheten. Olika menyalternativen i seriekonsolen beskrivs också.

### <a name="putty-settings"></a>PuTTY-inställningar

Kontrollera att du använder följande inställningar för PuTTY för att ansluta till Windows PowerShell-gränssnittet från seriekonsolen.

#### <a name="to-configure-putty"></a>Så här konfigurerar du PuTTY

1. I PuTTY **omkonfiguration** i dialogrutan den **kategori** väljer **tangentbord**.
2. Kontrollera att följande alternativ är valt (detta är standardinställningarna när du startar en ny session).
   
   | Tangentbord objekt | Välj |
   | --- | --- |
   | BACKSTEG |Kontroll-? (127) |
   | Hem- och nycklar |Standard |
   | Funktionstangenter och tangentbordet |ESC [n ~ |
   | Ursprungligt tillstånd för piltangenterna |Normal |
   | Ursprungligt tillstånd för numeriska tangentbordet |Normal |
   | Aktivera funktioner för extra tangentbord |CTRL + ALT + skiljer sig från AltGr |
   
    ![Putty inställningar som stöds](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klicka på **Använd**.
4. I den **kategori** väljer **översättning**.
5. I den **Remote teckenuppsättningen** väljer **UTF-8**.
6. Under **hantering av raden ritning tecken**väljer **Använd Unicode-kodpunkter för rad ritning**. Följande skärmbild visar alternativen för rätt PuTTY.
   
    ![UTF Putty-inställningar](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klicka på **Använd**.

Du kan nu använda PuTTY för att ansluta till enhetens seriekonsol genom att göra följande steg.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Om seriekonsolen

När du använder Windows PowerShell-gränssnittet på din StorSimple-enhet via seriekonsolen Banderollmeddelandet visas följt av menyalternativen.

Banderollmeddelandet innehåller grundläggande StorSimple enhetsinformation, till exempel modell, namn, version installerad programvara och status för den domänkontrollant som du ansluter till. Följande bild visar ett exempel på Banderollmeddelandet.

![Seriell Banderollmeddelandet](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Du kan använda Banderollmeddelandet för att identifiera om den domänkontrollant som du är ansluten till _Active_ eller _passiva_.

Följande bild visar de olika runspace alternativ som är tillgängliga i menyn för seriekonsolen.

![Registrera din enhet 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Du kan välja mellan följande inställningar:

1. **Logga in med fullständig åtkomst** det här alternativet kan du ansluta (med korrekta autentiseringsuppgifter) till den **SSAdminConsole** runspace på den lokala domänkontrollanten. (Den lokala domänkontrollanten är den domänkontrollant som du för närvarande kommer åt via seriekonsolen av StorSimple-enheten). Det här alternativet kan också användas för att tillåta Microsoft-supporten åtkomstbehörighet till obegränsad runspace (en supportsession) för att felsöka eventuella enhetsproblem som möjligt. När du använder alternativ 1 för att logga in, kan Microsoft Support-tekniker att få åtkomst till obegränsad runspace genom att köra en viss cmdlet. Mer information finns i [starta en session med stöd för](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Logga in till peer-styrenhet med fullständig åtkomst** det här alternativet är samma som alternativ 1, förutom att du kan ansluta (med korrekta autentiseringsuppgifter) till den **SSAdminConsole** runspace på peer-domänkontrollant. Eftersom StorSimple-enheten är en hög tillgänglighet med två domänkontrollanter i en konfiguration för aktivt-passivt refererar peer till andra domänkontrollanter i den enhet som du kommer åt via seriekonsolen).
   Precis som alternativ 1, det här alternativet kan också användas för att Tillåt Microsoft-supporten åtkomstbehörighet till obegränsad runspace på en peer-domänkontrollant.

3. **Ansluta med begränsad åtkomst** det här alternativet används för att komma åt Windows PowerShell-gränssnittet i begränsat läge. Du ombeds inte ange autentiseringsuppgifter. Det här alternativet ansluter till ett mer begränsat körningsutrymme jämfört med alternativ 1 och 2.  Vissa av de uppgifter som är tillgängliga via alternativ 1 som **kan* utföras i den här runspace är:
   
   * Återställa till fabriksinställningarna
   * Ändra lösenord
   * Aktivera eller inaktivera stöd för åtkomst
   * Tillämpa uppdateringar
   * Installera snabbkorrigeringar

    > [!NOTE]
    > Detta är det bästa alternativet om du har glömt administratörslösenord för enheten och kan inte ansluta via alternativ 1 eller 2.

4. **Ändra språk** det här alternativet kan du ändra visningsspråket på Windows PowerShell-gränssnittet. De språk som stöds är engelska, japanska, ryska, franska, koreanska söder, spanska, italienska, tyska, kinesiska och portugisiska (Brasilien).

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Fjärransluta till StorSimple som använder Windows PowerShell för StorSimple

Du kan använda Windows PowerShell-fjärrkommunikation för att ansluta till din StorSimple-enhet. När du ansluter det här sättet kan se du inte en meny. (Du se en meny endast om du använder seriekonsolen på enheten för att ansluta. Fjärranslutning går du direkt till motsvarande ”alternativ 1 – fullständig åtkomst” på seriekonsolen.) Med Windows PowerShell-fjärrkommunikation ansluta till en specifik runspace. Du kan också ange språk för visning.

Visningsspråket är oberoende av det språk som du ställer in med den **ändra språk** alternativ i menyn för seriekonsolen. Fjärr-PowerShell hämtar automatiskt upp de nationella inställningarna på enheten som du ansluter från om inget anges.

> [!NOTE]
> Om du arbetar med virtuella Microsoft Azure-värdar och StorSimple moln apparater, kan du använda Windows PowerShell-fjärrkommunikation och den virtuella värden för att ansluta till molnet-enhet. Om du har ställt in en plats på den värd där du vill spara information från Windows PowerShell-sessionen, bör du vara medveten som den _alla_ huvudnamn innehåller endast autentiserade användare. Därför, om du har lagt upp resursen att tillåta åtkomst av _alla_ och du ansluter utan att ange autentiseringsuppgifter oautentiserade anonym principal används och visas ett felmeddelande. För att åtgärda problemet på resursen värd du måste aktivera gästkontot och ger gästen fullständig åtkomst till resursen du måste ange giltiga autentiseringsuppgifter tillsammans med Windows PowerShell-cmdlet


Du kan använda HTTP eller HTTPS för att ansluta via Windows PowerShell-fjärrkommunikation. Följ instruktionerna i följande kurser:

* [Fjärransluta via HTTP](storsimple-remote-connect.md#connect-through-http)
* [Fjärransluta via HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Säkerhetsaspekter för anslutning

När du bestämmer hur du ansluter till Windows PowerShell för StorSimple, Tänk på följande:

* Ansluta direkt till enhetens seriekonsol är säker och ansluter till seriekonsol via nätverksväxlar inte. Var försiktig för säkerhetsrisken när du ansluter till enheten seriell över nätverksväxlar.
* Ansluter via en HTTP-session kan erbjuda mer säkerhet än att ansluta via seriekonsolen över nätverket. Även om detta inte är den säkraste metoden är är det acceptabelt på betrodda nätverk.
* Ansluter via en HTTPS-session är den säkraste och det rekommenderade alternativet.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrera din StorSimple-enhet med Windows PowerShell för StorSimple

I följande tabell visas en sammanfattning av alla vanliga administrativa uppgifter och komplexa arbetsflöden som kan utföras i Windows PowerShell-gränssnittet för din StorSimple-enhet. Mer information om varje arbetsflöde klickar du på lämplig posten i tabellen.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell för StorSimple-arbetsflöden

| Om du vill göra detta... | Använd den här proceduren. |
| --- | --- |
| Registrera din enhet |[Konfigurera och registrera enheten med Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurera webbproxy</br>Visa web proxy-inställningar |[Konfigurera en proxyserver för din StorSimple-enhet](storsimple-8000-configure-web-proxy.md) |
| Ändra DATA 0 inställningar för nätverksgränssnittet på din enhet |[Ändra DATA 0-nätverksgränssnittet för din StorSimple-enhet](storsimple-8000-modify-data-0.md) |
| Stoppa en domänkontrollant </br> Starta om eller stänga av en domänkontrollant </br> Stänga av en enhet</br>Återställa enheten till fabriksinställningarna |[Hantera styrenheter](storsimple-8000-manage-device-controller.md) |
| Installera Underhåll läge uppdateringar och snabbkorrigeringar |[Uppdatera din enhet](storsimple-update-device.md) |
| Ange underhållsläge </br>Avsluta underhållsläge |[Lägen för StorSimple-enhet](storsimple-8000-device-modes.md) |
| Skapa ett supportpaket</br>Dekryptera och redigera ett supportpaket |[Skapa och hantera ett stödpaket](storsimple-8000-create-manage-support-package.md) |
| Starta en session med stöd</br> |[Starta en session med stöd i Windows PowerShell för StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Få hjälp i Windows PowerShell för StorSimple

I Windows PowerShell för StorSimple är hjälp tillgänglig. En aktuell version av hjälpen finns även, där du kan uppdatera hjälp i systemet.

Få hjälp i det här gränssnittet liknar den i Windows PowerShell och de flesta av hjälp-relaterade cmdlets fungerar. Du kan hitta hjälp för Windows PowerShell online i TechNet-biblioteket: [med Windows PowerShell-skript](http://go.microsoft.com/fwlink/?LinkID=108518).

Följande är en kort beskrivning av typerna av hjälp för det här Windows PowerShell-gränssnittet, inklusive hur du uppdaterar hjälp.

### <a name="to-get-help-for-a-cmdlet"></a>Få hjälp för en cmdlet

* För att få hjälp för cmdleten eller funktion använder du följande kommando:`Get-Help <cmdlet-name>`
* För att få hjälp online för alla cmdletar kan använda föregående cmdlet med den `-Online` parameter:`Get-Help <cmdlet-name> -Online`
* För fullständig hjälp kan du använda den `–Full` parameter, och till exempel använda den `–Examples` parameter.

### <a name="to-update-help"></a>Uppdatera hjälp

Du kan enkelt uppdatera hjälp i Windows PowerShell-gränssnittet. Utför följande steg om du vill uppdatera hjälp i systemet.

#### <a name="to-update-cmdlet-help"></a>Uppdatera cmdlet hjälp
1. Starta Windows PowerShell med den **kör som administratör** alternativet.
2. Skriv följande vid kommandotolken:`Update-Help`
3. De uppdaterade hjälpfilerna kommer att installeras.
4. När hjälpfilerna är installerade, skriver du: `Get-Help Get-Command`. Detta visar en lista över cmdlets som hjälp är tillgänglig.

> [!NOTE]
> Om du vill hämta en lista över tillgängliga cmdlets i en runspace, logga in på motsvarande menyalternativ och kör den `Get-Command` cmdlet.


## <a name="next-steps"></a>Nästa steg

Om du får problem med din StorSimple-enhet när du utför en av de ovanstående arbetsflödena, se [StorSimple distributioner felsökningsverktyg](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

