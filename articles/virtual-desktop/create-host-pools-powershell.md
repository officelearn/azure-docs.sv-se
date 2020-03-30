---
title: Skapa Windows Virtual Desktop-värdpoolen PowerShell – Azure
description: Så här skapar du en värdpool i Windows Virtual Desktop med PowerShell-cmdletar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246967"
---
# <a name="create-a-host-pool-with-powershell"></a>Skapa en värdpool med PowerShell

Värdpooler är en samling av en eller flera identiska virtuella datorer i Windows Virtual Desktop-klientmiljöer. Varje värdpool kan innehålla en appgrupp som användarna kan interagera med på samma sätt som på ett fysiskt skrivbord.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Använda PowerShell-klienten för att skapa en värdpool

Hämta och importera först [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det.

Kör följande cmdlet för att logga in på Windows Virtual Desktop-miljö

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Kör sedan den här cmdleten för att skapa en ny värdpool i windows virtual desktop-klienten:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Kör nästa cmdlet för att skapa en registreringstoken för att auktorisera en sessionsvärd för att ansluta till värdpoolen och spara den i en ny fil på den lokala datorn. Du kan ange hur länge registreringstoken är giltig med parametern -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Därefter kör du den här cmdleten för att lägga till Azure Active Directory-användare i standardappgruppen för skrivbordsappar för värdpoolen.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Cmdlet för Add-RdsAppGroupUser** stöder inte att lägga till säkerhetsgrupper och lägger bara till en användare i taget i appgruppen. Om du vill lägga till flera användare i appgruppen kör du cmdleten igen med lämpliga användarnamn.

Kör följande cmdlet för att exportera registreringstoken till en variabel, som du kommer att använda senare i [Registrera de virtuella datorerna till värdpoolen för Windows Virtual Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Skapa virtuella datorer för värdpoolen

Nu kan du skapa en virtuell Azure-dator som kan anslutas till din Windows Virtual Desktop-värdpool.

Du kan skapa en virtuell dator på flera sätt:

- [Skapa en virtuell dator från en Azure Gallery-avbildning](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en virtuell dator från en hanterad avbildning](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Skapa en virtuell dator från en ohanterad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Om du distribuerar en virtuell dator med Windows 7 som värdoperator kommer skapande och distributionsprocess att vara lite annorlunda. Mer information finns i [Distribuera en virtuell Windows 7-dator på Windows Virtual Desktop](deploy-windows-7-virtual-machine.md).

När du har skapat din session värd virtuella datorer, [tillämpa en Windows-licens på en session värd VM](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) för att köra din Windows eller Windows Server virtuella datorer utan att betala för en annan licens. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Förbereda virtuella datorer för Windows Virtual Desktop-agentinstallationer

Du måste göra följande saker för att förbereda dina virtuella datorer innan du kan installera Windows Virtual Desktop-agenter och registrera de virtuella datorerna i värdpoolen för Windows Virtual Desktop:

- Du måste domän-ansluta till datorn. På så sätt kan inkommande Windows Virtual Desktop-användare mappas från sitt Azure Active Directory-konto till sitt Active Directory-konto och har tillåtits åtkomst till den virtuella datorn.
- Du måste installera rollen Värd för fjärrskrivbordssession (RDSH) om den virtuella datorn kör ett Windows Server OS. Med rollen RDSH kan Windows Virtual Desktop-agenter installeras korrekt.

Om du vill ansluta domän gör du följande saker på varje virtuell dator:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Starta **Kontrollpanelen** på den virtuella datorn och välj **System**.
3. Välj **Datornamn,** välj **Ändra inställningar**och välj sedan **Ändra...**
4. Välj **Domän** och ange sedan Active Directory-domänen i det virtuella nätverket.
5. Autentisera med ett domänkonto som har behörighet att ansluta datorer för domänkoppling.

    >[!NOTE]
    > Om du ansluter dina virtuella datorer till en Azure Active Directory Domain Services -miljö (Azure AD DS) kontrollerar du att domänanslutningsanvändaren också är medlem i [gruppen AAD DC-administratörer](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrera de virtuella datorerna i värdpoolen för Virtuellt skrivbord i Windows

Det är lika enkelt att registrera de virtuella datorerna i en värdpool för Windows Virtual Desktop som att installera Windows Virtual Desktop-agenter.

Så här registrerar du Windows Virtual Desktop-agenter på varje virtuell dator:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Ladda ned och installera Windows Virtual Desktop Agent.
   - Ladda ned [Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Högerklicka på den hämtade installationsprogrammet, välj **Egenskaper**, välj **Ta bort blockering**och välj sedan **OK**. Detta gör att ditt system att lita på installationsprogrammet.
   - Kör installationsprogrammet. När installationsprogrammet ber dig om registreringstoken anger du värdet du fick från cmdleten **Export-RdsRegistrationInfo.**
3. Ladda ned och installera Starthanteraren för Windows Virtual Desktop Agent.
   - Ladda ner [Windows Virtual Desktop Agent Bootloader](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Högerklicka på den hämtade installationsprogrammet, välj **Egenskaper**, välj **Ta bort blockering**och välj sedan **OK**. Detta gör att ditt system att lita på installationsprogrammet.
   - Kör installationsprogrammet.

>[!IMPORTANT]
>För att skydda din Windows Virtual Desktop-miljö i Azure rekommenderar vi att du inte öppnar inkommande port 3389 på dina virtuella datorer. Windows Virtual Desktop kräver inte en öppen inkommande port 3389 för att användare ska komma åt värdpoolens virtuella datorer. Om du måste öppna port 3389 för felsökning rekommenderar vi att du använder [ny vm-åtkomst](../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har gjort en värdpool kan du fylla den med RemoteApps. Mer information om hur du hanterar appar i Windows Virtual Desktop finns i självstudien Hantera appgrupper.

> [!div class="nextstepaction"]
> [Självstudiekurs för appgrupper](./manage-app-groups.md)
