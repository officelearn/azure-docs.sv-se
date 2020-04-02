---
title: Guide för att kontrollera Windows-avstängningsbeteende i Azure Lab Services | Microsoft-dokument
description: Åtgärder för att automatiskt stänga av en inaktiv virtuell dator i Windows och ta bort kommandot Windows shutdown.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522243"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guide till att kontrollera Windows avstängningsbeteende

Azure Lab Services innehåller flera kostnadskontroller för att säkerställa att virtuella Datorer i Windows inte körs oväntat:
 - [Ange ett schema](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Ange kvoter för användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Aktivera automatisk avstängning vid frånkoppling](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Även med dessa kostnadskontroller finns det situationer där en Virtuell Windows-dator oväntat kan fortsätta att köras. och som ett resultat av detta, dra av från studentens kvot:

- **RDP-fönstret lämnas öppet**
  
    När en deltagare ansluter till sin virtuella dator med RDP kan de av misstag lämna RDP-fönstret öppet.  Så länge RDP-fönstret förblir öppet börjar den **automatiska avstängningen vid frånkoppling** aldrig att börja gälla eftersom det bara utlöses efter att RDP-sessionen har kopplats från.

- **Kommandot Windows shutdown används för att stänga av den virtuella datorn**
  
    En elev kan använda Windows-avstängningskommandot eller andra avstängningsmekanismer som finns i Windows för att stänga av den virtuella datorn i stället för att använda [stoppknappen för Azure Lab Services.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)  När detta händer, ur Azure Lab Services, används den virtuella datorn fortfarande.
    
Den här guiden innehåller steg för att automatiskt stänga av en inaktiv Windows-virtuell dator och ta bort kommandot Windows avstängning från **Start-menyn** för att förhindra att dessa situationer inträffar.  

> [!NOTE]
> En virtuell dator kan också oväntat dra av från kvoten när deltagaren startar sin virtuella dator, men ansluter aldrig till den med RDP.  Den här guiden tar för närvarande *inte* upp det här scenariot.  I stället bör eleverna påminnas om att omedelbart ansluta till sin virtuella dator med RDP när de har börjat den. eller bör de stoppa den virtuella datorn.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatisk RDP-frånkoppling och avstängning för inaktiv virtuell dator

Windows tillhandahåller **inställningar för lokala grupprinciper** som du kan använda för att ange en tidsgräns för att automatiskt koppla från en RDP-session när den blir inaktiv.  En session bedöms vara inaktiv när det *inte* finns någon mus-tangentbordsinmatning.  Alla tidskrävande aktiviteter som inte involverar mus-tangentbordsinmatning gör att den virtuella datorn är inaktiv.  Detta inkluderar att köra en lång fråga, strömmande video, kompilering, etc.  Beroende på klassens behov kan du välja att ange inaktiv tidsgräns så att den är tillräckligt lång för att hantera dessa typer av aktiviteter.  Du kan till exempel ange inaktiv tidsgräns till 1 eller fler timmar om det behövs.

Här är deltagarens upplevelse när du konfigurerar **gränsen för inaktiv session** i kombination med den [**automatiska avstängningen vid frånkopplingsinställning:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. Eleven ansluter till sin virtuella Windows-dator med RDP.
 2. När deltagaren lämnar sitt RDP-fönster öppet och den virtuella datorn är inaktiv för den **inaktiva sessionsgräns** som du angav (t.ex. 5 minuter) visas följande dialogruta:

    ![Dialogrutan Förfallen tidsgräns för inaktiv tid](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Om deltagaren *inte* klickar på **OK**kopplas rdp-sessionen automatiskt från efter 2 minuter.
2. När RDP-sessionen har kopplats från stängs den virtuella datorn automatiskt av Azure Lab Services när den angivna tidsramen för den **automatiska avstängningen vid frånkoppling** har nåtts.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Ange tidsgräns för inaktiv session för fjärrskrivbordssession för mallen VM

Om du vill ange den inaktiva tidsgränsen för RDP-sessionen kan du ansluta till mallen VM och köra det under PowerShell-skriptet.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Du kan också välja att följa de här manuella stegen med hjälp av mallen VM:

1. Tryck på Windows-tangenten, skriv **gpedit**och välj sedan **Redigera grupprincip (Kontrollpanelen)**.

1. Gå till **datorkonfiguration > administrativa mallar > Windows-komponenter > fjärrskrivbordstjänster > värd för fjärrskrivbordssession > sessionstidsgränser**.  

    ![Redigeraren för lokala grupprinciper](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Högerklicka på **Ange tidsgräns för aktiva men inaktiva sessioner för fjärrskrivbordstjänster**och klicka på **Redigera**.

1. Ange följande inställningar och klicka sedan på **OK**:
   1. Välj **Aktiverad**.
   1. Under **Alternativ**anger du **gränsen för inaktiv session**.

    ![Gräns för inaktiv session](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Slutligen, för att kombinera detta beteende med den **automatiska avstängningen vid frånkoppling** inställning, bör du följa stegen i how-to artikeln: [Aktivera automatisk avstängning av virtuella datorer på frånkoppling](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Ta bort kommandot Windows-avstängning från Start-menyn

Med windows **lokala grupprincipinställningar** kan du också ta bort avstängningskommandot från **Start-menyn.**

Om du vill ta bort avstängningskommandot kan du ansluta till mallen VM och köra under PowerShell-skriptet.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Du kan också välja att följa de här manuella stegen med hjälp av mallen VM:

1. Tryck på Windows-tangenten, skriv **gpedit**och välj sedan **Redigera grupprincip (Kontrollpanelen)**.

1. Gå till **datorkonfiguration > administrativa mallar > Start-menyn och Aktivitetsfältet**.  

    ![Redigeraren för lokala grupprinciper](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Högerklicka på **Ta bort och förhindra åtkomst till kommandona Stäng av, Starta om, för viloläge och Viloläge**och klicka på **Redigera**.

1. Markera inställningen **Aktiverad** och klicka sedan på **OK:**
 
   ![Inställning för avstängning](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Observera att avstängningskommandot inte längre visas under **Start-menyn i** Windows. endast kommandot **Koppla från** visas.

    ![Kommandot Avstängning](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Nästa steg
Se artikeln om hur du förbereder en VIRTUELL Windows-mall: [Guide för att konfigurera en Windows-malldator i Azure Lab Services](how-to-prepare-windows-template.md)