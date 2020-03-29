---
title: Skapa ett labb för att lära ut utveckling av mobilapplikationer med Android Studio
titleSuffix: Azure Lab Services
description: Lär dig hur du konfigurerar ett labb för att lära ut utvecklingsklass för datamobilprogram som använder Android Studio.  Artikel kommer också att diskutera justeringar att göra när du använder Android Studio på en virtuell dator i Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849791"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Konfigurera ett labb för att lära ut datamobil applikationsutveckling med Android Studio

Den här artikeln visar hur du ställer in en inledande mobil applikationsutvecklingsklass.  Den här klassen fokuserar på Android-mobilappar som kan publiceras i [Google Play Butik](https://play.google.com/store/apps).  Eleverna lär sig att använda [Android Studio](https://developer.android.com/studio) för att skapa program.  [Visual Studio Emulator för Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) används för att testa programmet lokalt.

## <a name="lab-configuration"></a>Labbkonfiguration

För att konfigurera det här labbet behöver du en Azure-prenumeration och labbkonto för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har fått en Azure-prenumeration kan du skapa ett nytt labbkonto i Azure Lab Services. Mer information om hur du skapar ett nytt labbkonto finns i [självstudiekursen för att skapa ett labbkonto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labbkonto.

Följ [den konfigurerar labbhandledningen i klassrummet](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och använd sedan följande inställningar:

| Storlek för virtuell dator | Bild |
| -------------------- | ----- |
| Medium (kapslad virtualisering) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Konfiguration av mallmaskin

När skapandet av malldatorn är klar [startar du datorn och ansluter till den](how-to-create-manage-template.md#update-a-template-vm) för att utföra följande uppgifter:

1. Lägg till Hyper-V-roll
2. Ladda ner och installera Java.  
3. Ladda ner och installera Visual Studio Emulator för Android.
4. Ladda ner och installera Android Studio.
5. Konfigurera Visual Studio Emulator för Android Studio.

## <a name="add-hyper-v-role"></a>Lägg till Hyper-V-roll

Hyper-V måste aktiveras för en lyckad installation av Visual Studio Emulator för Android.  Följ instruktionerna i [hur du aktiverar kapslad virtualisering i en artikel med virtuella datorer i mallen.](how-to-enable-nested-virtualization-template-vm.md)

## <a name="install-java"></a>Installera Java

Android Studio kräver Java.  Följ stegen nedan för att ladda ner den senaste versionen av Java.

1. Navigera till [nedladdningssidan](https://www.java.com/download/)för Java . Klicka på knappen **Ladda ned Java.**
2. På webbsidan 64-bitars Windows för Java klickar du på knappen **Godkänn och Starta gratis nedladdning**.
3. När **installationsprogrammet för Java** visas klickar du på **Installera**.
4. Vänta tills installationstiteln ändras till **Java-installationen – Slutför**.  Klicka på **Knappen Stäng.**

## <a name="install-visual-studio-emulator-for-android"></a>Installera Visual Studio Emulator för Android

För att testa ett Android-program lokalt måste den använda en virtualiserad version av en Android-enhet.  Det finns några Android emulatorer tillgängliga som gör det möjligt för en utvecklare att testa sin ansökan från sin maskin.  Vi använder Visual Studio Emulator för Android eftersom det är en emulator som stöder kapslad virtualisering.  Eftersom den virtuella datorn för labtjänsten redan är en virtuell dator behöver vi en emulator som stöder kapslad virtualisering.  Den inbyggda emulatorn för Android Studio stöder inte kapslad virtualisering.  Information om vilka emulatorer som stöder kapslad virtualisering finns i [maskinvaruacceleration för emulatorprestanda (Hyper-V & HAXM).](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)

Använd följande instruktioner för att hämta och installera Visual Studio Emulator för Android.

1. Navigera till [Visual Studio Emulator för Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) hemsida.
2. Klicka på knappen **Hämta emulator.**
3. När vs_emulatorsetup.exe hämtas kör du den körbara filen.
4. När dialogrutan Konfigurera Visual Studio visas klickar du på knappen **Installera.**
5. Vänta tills installationsprogrammet är klart.  Klicka på knappen **Starta om nu** om du vill starta om datorn och slutföra installationen.

Starta emulatorn först innan du distribuerar ditt program med Android Studio.  Mer information om Visual Studio Emulator för Android finns i [Visual Studio Emulator för Android-dokumentation](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Installera Android Studio

Följ instruktionerna nedan för att ladda ner och installera [Android Studio](https://developer.android.com/studio).

1. Navigera till [Android Studio nedladdningssida](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer stöds inte av den här webbplatsen.
2. Klicka på det körbara Android Studio-paketet med Windows (64 bitar).
3. Läs de juridiska termer som skrivits i popup-programmet.  När du är redo att fortsätta, kontrollera **att jag har läst och håller med om kryssrutan ovan villkor** och klicka på knappen Ladda ner Android Studio för **Windows.**
4. En gång på Android Studio setup körbar hämtas, kör den körbara.
5. Klicka på **Nästa**på installationssidan **för Välkommen till Android Studio** i **installationsprogrammet för Android Studio.**
6. Klicka på **Nästa**på sidan **Konfigurationsinställningar** .
7. Klicka på **Installera**på sidan **Välj startmenymapp** .
8. Vänta tills installationen är klar.
9. Klicka på **Nästa**på sidan **Slutför installation** .
10. På sidan **Slutför installationen av Android Studio.**  Klicka på **Slutför**.
11. Android Studio startar automatiskt när installationen är klar.
12. I dialogrutan **Importera Android-inställningar från...** väljer du **Importera inte inställningar**. Klicka på **OK**.
13. Klicka på **Nästa**på **välkomstsidan** i **installationsguiden för Android Studio**.
14. Välj **Standard**på sidan **Installationstyp** . Klicka på **Nästa**.
15. Välj önskat tema på sidan **Välj gränssnittstema.** Klicka på **Nästa**.
16. Klicka på **Nästa**på sidan **Verifiera inställningar** .
17. Vänta tills alla komponenter har hämtats på sidan **Hämta komponenter.**  Klicka på **Slutför**.

    > [!IMPORTANT]
    > Det förväntas att HAXM-installationen misslyckas.  HAXM stöder inte kapslad virtualisering, vilket är anledningen till att vi installerade Visual Studio Emulator för Android tidigare i den här artikeln.

18. Dialogrutan **Välkommen till Android Studio** visas när installationsguiden är klar.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Konfigurera Android Studio och Visual Studio Emulator för Android

Android Studio är nästan klar för användning.  Vi måste fortfarande berätta Visual Studio Emulator för Android där Android SDK är installerat.  Detta kommer att göra alla emulatorer som körs i Visual Studio för Android visar som distributionsmål för Android Studio felsökning.

Vi måste ställa in en specifik registernyckel för att berätta Visual Studio Emulator för Android där Android Sdk finns.  Om du vill ange den nödvändiga registernyckeln kör du skriptet nedan.  PowerShell-skriptet nedan förutsätter standardinstallationsplatsen för Android Sdk.  Om du har installerat din Android Sdk `$androidSdkPath` på en annan plats ändrar du värdet innan du kör skriptet.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Starta om Visual Studio Emulator för Android och Android Studio så att den nya inställningen används.

Starta den version du behöver i Visual Studio Emulator.  Det visas som ett distributionsmål för din Android-app i Android-studion.  Den minsta versionen för Android Studio-projektet måste stödja den version som körs i Visual Studio Emulator för Android.  Nu är du redo att skapa och felsöka projekt med Android Studio och Visual Studio Emulator för Android.  Om du har några problem läser du felsökningen av Android-emulator.

## <a name="cost"></a>Kostnad

Om du vill uppskatta kostnaden för det här labbet kan du följa exemplet nedan.
För en klass på 25 elever med 20 timmars schemalagd lektionstid och 10 timmars kvot för läxor eller uppgifter, skulle priset för labbet vara  

25 \* studenter (20 schemalagda + 10 kvoter) timmar * 55 lab enheter * 0,01 USD per timme = 412,5 USD

Mer information om prissättning finns i [Azure Lab Services Prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att konfigurera ett labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Länkar till e-postregistrering till studenter](how-to-configure-student-usage.md#send-invitations-to-users)
