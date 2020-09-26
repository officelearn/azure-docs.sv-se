---
title: Guide för att styra beteendet för Windows-avstängning i Azure Lab Services | Microsoft Docs
description: Steg för att automatiskt stänga av en virtuell Windows-dator som är inaktiv och ta bort Windows shutdown-kommandot.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3c20bc2bb79faf53c4f3fbd113c18c5c6d923e59
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334029"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guide för att styra avstängnings beteendet i Windows

Azure Lab Services tillhandahåller flera kostnads kontroller för att säkerställa att virtuella Windows-datorer (VM) inte körs utan förvarning:
 - [Ange ett schema](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Ange kvoter för användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Aktivera automatisk avstängning vid frånkoppling](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Även med dessa kostnads kontroller finns det situationer där en virtuell Windows-dator kan fortsätta att köras. Det innebär att dra av student kvoten:

- **RDP-fönstret lämnas öppet**
  
    När en student ansluter till den virtuella datorn med RDP kan de oavsiktligt lämna RDP-fönstret öppet.  Så länge RDP-fönstret förblir öppet börjar inte inställningen **Automatisk avstängning vid från koppling** att gälla eftersom den endast utlöses efter att RDP-sessionen har kopplats från.

- **Windows shutdown-kommandot används för att stänga av den virtuella datorn**
  
    En student kan använda Windows avstängnings kommando eller andra avstängnings metoder som finns i Windows för att stänga av den virtuella datorn i stället för att använda [Azure Lab Services stopp-knappen](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  När detta inträffar kommer den virtuella datorn fortfarande att användas från Azure Lab Services perspektiv.
    
För att hjälpa dig att förhindra dessa situationer, innehåller den här guiden steg för att automatiskt stänga av en inaktiv virtuell Windows-dator och ta bort Windows shutdown-kommandot från **Start** menyn.  

> [!NOTE]
> En virtuell dator kan också dras av från kvoten när studenten startar sin virtuella dator, men ansluter aldrig till den via RDP.  Den här guiden löser för närvarande *inte* det här scenariot.  I stället bör eleverna bli påmind om att omedelbart ansluta till den virtuella datorn med RDP när de har startat den. eller så bör de stoppa den virtuella datorn.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatisk RDP-från koppling och avstängning för inaktiv virtuell dator

Windows tillhandahåller inställningar för **lokala Grupprincip** som du kan använda för att ange en tids gräns för att automatiskt koppla från en RDP-session när den blir inaktiv.  En session bestäms vara inaktiv när det *inte* finns några mouse\keyboard-ininformation.  Alla tids krävande aktiviteter som inte involverar mouse\keyboard-indatamängdar orsakar att den virtuella datorn är i inaktivt läge.  Detta inkluderar körning av en lång fråga, strömmande video, kompilering osv.  Beroende på klassens behov kan du välja att ange tids gränsen för inaktivitet så att den är tillräckligt lång för att hantera dessa typer av aktiviteter.  Du kan till exempel ange tids gränsen för inaktivitet till minst 1 timme om det behövs.

Här är studentens upplevelse när du konfigurerar gränsen för **inaktiva sessioner** i kombination med inställningen [**Automatisk avstängning vid från koppling**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) :
 1. Studenten ansluter till sin virtuella Windows-dator med RDP.
 2. När studenten lämnar sitt RDP-fönster öppet och den virtuella datorn är inaktiv under **gränsen för inaktiva sessioner** som du har angett (till exempel 5 minuter) ser eleven följande dialog ruta:

    ![Dialog rutan gräns för tids gräns för inaktivitet](./media/how-to-windows-shutdown/idle-time-expired.png)

1. Om studenten *inte* klickar på **OK**, kommer RDP-sessionen att kopplas från automatiskt efter 2 minuter.
2. När RDP-sessionen kopplas från när den angivna tids ramen för inställningen **Automatisk avstängning vid från koppling** har nåtts, stängs den virtuella datorn automatiskt av Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Ange tids gräns för RDP-inaktiv session på den virtuella mallen

Om du vill ange tids gräns för RDP-session kan du ansluta till mallen VM och köra PowerShell-skriptet nedan.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Eller så kan du välja att följa dessa manuella steg med mallen VM:

1. Tryck på Windows-tangenten, Skriv **gpedit**och välj sedan **Redigera grup princip (kontroll panelen)**.

1. Gå till **dator konfiguration > Administrativa mallar > Windows-komponenter > Fjärrskrivbordstjänster > värd för värd för fjärrskrivbordssession > tids gränser**för fjärrskrivbordssession.  

    ![Skärm bild som visar "redigeraren för lokal grup princip" med "tids gränser för sessioner" markerade.](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Högerklicka på **Ange tids gräns för aktiva men inaktiva Fjärrskrivbordstjänster sessioner**och klicka på **Redigera**.

1. Ange följande inställningar och klicka sedan på **OK**:
   1. Välj **Aktiverad**.
   1. Under **alternativ**anger du **gränsen för inaktiva sessioner**.

    ![Gräns för inaktiv session](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Om du vill kombinera detta beteende med inställningen **Automatisk avstängning vid från koppling** bör du följa stegen i instruktions artikeln: [Aktivera automatisk avstängning av virtuella datorer vid från koppling](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> När du har konfigurerat den här inställningen med hjälp av PowerShell för att ändra register inställningen direkt eller manuellt med hjälp av grupprincip redigeraren måste du först starta om den virtuella datorn för att inställningarna ska börja gälla.  Om du konfigurerar inställningen med hjälp av registret uppdateras även grupprincip redigeraren så att den återspeglar ändringar i register inställningen. register inställningen börjar dock gälla som förväntat, och RDP-sessionen kopplas från vid inaktivitet under den angivna tids perioden.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Ta bort Windows avstängnings kommando från Start menyn

Med inställningarna för lokala Windows- **Grupprincip** kan du också ta bort shutdown-kommandot från **Start** menyn.

Om du vill ta bort kommandot shutdown kan du ansluta till mallen VM och köra PowerShell-skriptet nedan.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Eller så kan du välja att följa dessa manuella steg med mallen VM:

1. Tryck på Windows-tangenten, Skriv **gpedit**och välj sedan **Redigera grup princip (kontroll panelen)**.

1. Gå till **dator konfiguration > Administrativa mallar > Start-menyn och aktivitets fältet**.  

    ![Redigeraren för lokala grupprinciper](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Högerklicka på **ta bort och förhindra åtkomst till kommandona Stäng av, starta om, ström spar läge och vilo läge**och klicka på **Redigera**.

1. Välj den **aktiverade** inställningen och klicka sedan på **OK**:
 
   ![Avslutnings inställning](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Observera att kommandot shutdown inte längre visas under **Start** menyn i Windows; endast kommandot **från koppling** visas.

    ![Shutdown-kommando](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Nästa steg
Se artikeln om hur du förbereder en virtuell Windows-mall: [Guide för att konfigurera en Windows-mall i Azure Lab Services](how-to-prepare-windows-template.md)