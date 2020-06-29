---
title: Konfigurera ett labb för att lära mobil program utveckling med Android Studio
titleSuffix: Azure Lab Services
description: Lär dig hur du konfigurerar ett labb för att lära dig att utveckla data i utvecklings klassen för mobil program som använder Android Studio.  Artikeln diskuterar också justeringar som ska utföras när du använder Android Studio på en virtuell dator i Azure.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: ff996a096cddbb85f1e1c84cd051c18a7ab4ad79
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85444989"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Konfigurera ett labb för att lära dig utveckla data i mobil program med Android Studio

Den här artikeln visar hur du konfigurerar en introduktions utvecklings klass för mobil program.  Den här klassen fokuserar på mobila Android-program som kan publiceras till [Google Play Butik](https://play.google.com/store/apps).  Eleverna lär dig hur du använder [Android Studio](https://developer.android.com/studio) för att bygga program.  [Visual Studio-emulator för Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) används för att testa programmet lokalt.

## <a name="lab-configuration"></a>Labb konfiguration

För att kunna konfigurera det här labbet behöver du ett Azure-prenumerations-och labb konto för att komma igång. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labb konto.

Följ [själv studie kursen Konfigurera klass rummet](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och Använd sedan följande inställningar:

| Storlek för virtuell dator | Bild |
| -------------------- | ----- |
| Medium (kapslad virtualisering) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Mall dator konfiguration

När mallens dator har skapats startar du [datorn och ansluter till den](how-to-create-manage-template.md#update-a-template-vm) för att utföra följande uppgifter:

1. Lägg till Hyper-V-roll
2. Ladda ned och installera Java.  
3. Ladda ned och installera Visual Studio-emulator för Android.
4. Hämta och installera Android Studio.
5. Konfigurera Visual Studio-emulatorn för Android Studio.

## <a name="add-hyper-v-role"></a>Lägg till Hyper-V-roll

Hyper-V måste vara aktiverat för att installationen av Visual Studio-emulator för Android ska lyckas.  Följ anvisningarna i artikeln [så här aktiverar du kapslad virtualisering i en mall för virtuella datorer](how-to-enable-nested-virtualization-template-vm.md) .

## <a name="install-java"></a>Installera Java

Android Studio kräver Java.  Följ stegen nedan för att ladda ned den senaste versionen av Java.

1. Gå till [nedladdnings sidan för Java](https://www.java.com/download/). Klicka på knappen **Java-nedladdning** .
2. På sidan 64-bitars Windows för Java-sida klickar du på knappen **Godkänn och starta kostnads fri hämtning**.
3. När **installations** programmet för Java-installationen visas klickar du på **Installera**.
4. Vänta tills installations titeln ändras till **Java-installationen – slutförd**.  Klicka på knappen **Stäng** .

## <a name="install-visual-studio-emulator-for-android"></a>Installera Visual Studio-emulator för Android

Om du vill testa ett Android-program lokalt måste det använda en virtualiserad version av en Android-enhet.  Det finns några Android-emulatorer som gör det möjligt för utvecklare att testa sina program från sina datorer.  Vi använder Visual Studio-emulator för Android eftersom det är en emulator som stöder kapslad virtualisering.  Eftersom labb tjänstens virtuella dator redan är en virtuell dator behöver vi en emulator som stöder kapslad virtualisering.  Den inbyggda emulatorn för Android Studio stöder inte kapslad virtualisering.  Om du vill se vilka emulatorer som stöder kapslad virtualisering, se [maskin varu acceleration för prestanda för emulator (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Använd följande instruktioner för att ladda ned och installera Visual Studio-emulator för Android.

1. Gå till start sidan [för Visual Studio-emulatorn för Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) .
2. Klicka på **Hämta emulator** -knappen.
3. Kör den körbara filen när vs_emulatorsetup.exe har laddats ned.
4. När dialog rutan Visual Studio Setup visas klickar du på knappen **Installera** .
5. Vänta tills installations programmet har slutförts.  Starta om datorn och slutför installationen genom att klicka på knappen **starta om nu** .

Starta emulatorn först innan du distribuerar ditt program med hjälp av Android Studio.  Mer information om Visual Studio-emulatorn för Android finns i [dokumentationen för Visual Studio-emulator för Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Installera Android Studio

Följ anvisningarna nedan för att ladda ned och installera [Android Studio](https://developer.android.com/studio).

1. Gå till [Android Studio hämtnings sida](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer stöds inte av den här platsen.
2. Klicka på det körbara Android Studio paketet Windows (64-bitars).
3. Läs de juridiska villkor som skrivs i popup-fönstret.  När du är redo att fortsätta kontrollerar du att kryss rutan **har lästs och samtycker till ovanstående villkor** och klickar på knappen **Ladda ned Android Studio för Windows** .
4. Kör den körbara filen på Android Studio körbara installations program.
5. Klicka på **Nästa**på sidan **Välkommen till Android Studio installations** program i installations programmet för **Android Studio** .
6. På sidan **konfigurations inställningar** klickar du på **Nästa**.
7. På sidan **Välj Start-menyn mapp** klickar du på **Installera**.
8. Vänta tills installationen har slutförts.
9. På sidan **installationen har slutförts** klickar du på **Nästa**.
10. På sidan **slutför Android Studio installationen** .  Klicka på **Slutför**.
11. Android Studio startas automatiskt när installationen är slutförd.
12. I dialog rutan **Importera Android-inställningar från...** väljer **du importera inte inställningar**. Klicka på **OK**.
13. Klicka på **Nästa**på sidan **Välkommen** i **installations guiden för Android Studio**.
14. På sidan **Installations typ** väljer du **standard**. Klicka på **Nästa**.
15. På sidan **Välj UI-tema** väljer du önskat tema. Klicka på **Nästa**.
16. På sidan **Verifiera inställningar** klickar du på **Nästa**.
17. På sidan **Ladda ned komponenter** väntar du tills alla komponenter har laddats ned.  Klicka på **Slutför**.

    > [!IMPORTANT]
    > Det förväntas att installationen av HAXM Miss lyckas.  HAXM har inte stöd för kapslad virtualisering, vilket är anledningen till att vi har installerat Visual Studio-emulator för Android tidigare i den här artikeln.

18. Dialog rutan **Välkommen till Android Studio** visas när installations guiden är klar.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Konfigurera Android Studio och Visual Studio-emulator för Android

Android Studio är snart klart att använda.  Vi behöver fortfarande meddela Visual Studio-emulatorn för Android där Android SDK är installerat.  Detta gör att alla emulatorer som körs i Visual Studio för Android visas som distributions mål för Android Studio fel sökning.

Vi måste ange en unik register nyckel för att meddela Visual Studio-emulatorn för Android där Android SDK finns.  Om du vill ange den register nyckel som behövs kör du skriptet nedan.  PowerShell-skriptet nedan förutsätter standard installations platsen för Android SDK.  Om du har installerat Android SDK på en annan plats ändrar du värdet för `$androidSdkPath` innan du kör skriptet.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Starta om Visual Studio-emulatorn för Android och Android Studio så att den nya inställningen används.

Starta den version du behöver i Visual Studio-emulatorn.  Den visas som ett distributions mål för din Android-app i Android Studio.  Den lägsta versionen för Android Studio-projektet måste ha stöd för den version som körs i Visual Studio-emulatorn för Android.  Nu är du redo att skapa och felsöka projekt med hjälp av Android Studio och Visual Studio-emulator för Android.  Om du har problem kan du läsa fel sökning av Android-emulator.

## <a name="cost"></a>Kostnad

Om du vill uppskatta kostnaden för det här labbet kan du följa exemplet nedan.
För en klass av 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara  

25 studenter \* (20 schemalagda + 10 kvot) timmar * 55 lab-enheter * 0,01 USD per timme = 412,5 USD

Mer detaljerad information om priser finns [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)
