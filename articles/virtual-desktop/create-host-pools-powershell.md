---
title: Skapa en Windows Virtual Desktop Preview-adresspool med PowerShell – Azure
description: Så här skapar du en adresspool i för hands versionen av Windows Virtual Desktop med PowerShell-cmdletar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: 2fd8934f34b811c0a2532f27e32fc799c2c8186e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141837"
---
# <a name="create-a-host-pool-with-powershell"></a>Skapa en värdpool med PowerShell

Lagringspooler är en samling av en eller flera identiska virtuella datorer i Windows Virtual Desktop Preview-klient miljöer. Varje adresspool kan innehålla en app-grupp som användare kan interagera med på samma sätt som på ett fysiskt skriv bord.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Använda PowerShell-klienten för att skapa en adresspool

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) som ska användas i PowerShell-sessionen om du inte redan gjort det.

Kör följande cmdlet för att logga in på Windows-miljön för virtuella skriv bord

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Kör sedan denna cmdlet för att skapa en ny adresspool i din Windows-klient för virtuella skriv bord:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Kör nästa cmdlet för att skapa en registrerings-token för att auktorisera en sessions värd att ansluta till värddatorn och spara den till en ny fil på din lokala dator. Du kan ange hur länge registrerings-token är giltig med hjälp av parametern-ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Därefter kör du denna cmdlet för att lägga till Azure Active Directory användare till standard gruppen för Skriv bords appar för poolen.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Cmdleten **Add-RdsAppGroupUser** har inte stöd för att lägga till säkerhets grupper och lägger bara till en användare i taget i gruppen app. Om du vill lägga till flera användare i app-gruppen kör du cmdleten igen med rätt huvud namn för användare.

Kör följande cmdlet för att exportera registrerings-token till en variabel, som du kommer att använda senare i [registrera de virtuella datorerna i Windows-poolen för virtuella skriv bord](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Skapa virtuella datorer för den värdbaserade poolen

Nu kan du skapa en virtuell Azure-dator som kan anslutas till din Windows-pool för virtuella skriv bord.

Du kan skapa en virtuell dator på flera sätt:

- [Skapa en virtuell dator från en Azure Gallery-avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Skapa en virtuell dator från en hanterad avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Skapa en virtuell dator från en ohanterad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

När du har skapat en sessions värd för virtuella datorer ska du [tillämpa en Windows-licens på en virtuell dator för en fjärrskrivbordssession](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) för att köra Windows-eller Windows Server-datorer utan att betala någon annan licens. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Förbereda de virtuella datorerna för Windows Virtual Desktop Preview agent-installationer

Du måste göra följande för att förbereda dina virtuella datorer innan du kan installera de virtuella Windows-datorerna och registrera de virtuella datorerna i Windows-poolen för virtuella Skriv bords värdar:

- Du måste ha domän anslutning till datorn. Detta gör att inkommande virtuella Windows-skrivbord kan mappas från sina Azure Active Directory-konton till deras Active Directory konto och beviljas åtkomst till den virtuella datorn.
- Du måste installera RDSH-rollen (Remote Desktop Session Host) om den virtuella datorn kör ett Windows Server-operativsystem. RDSH-rollen tillåter att de virtuella Windows-datorerna installeras på rätt sätt.

För att lyckas med domän koppling gör du följande på varje virtuell dator:

1. [Anslut till den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Starta **kontroll panelen** på den virtuella datorn och välj **system**.
3. Välj **dator namn**, Välj **ändra inställningar**och välj sedan **ändra...**
4. Välj **domän** och ange sedan Active Directory domän i det virtuella nätverket.
5. Autentisera med ett domän konto som har behörighet att ansluta till datorer med domän anslutning.

    >[!NOTE]
    > Om du ansluter dina virtuella datorer till en Azure Active Directory Domain Services (Azure AD DS)-miljö måste du se till att din domän anslutning också är medlem i [Administratörs gruppen för AAD](../active-directory-domain-services/tutorial-create-instance.md#configure-an-administrative-group)-domänkontrollanten.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Registrera de virtuella datorerna på Windows-poolen för för hands version av virtuella skriv bord

Att registrera de virtuella datorerna på en Windows-pool för virtuella skriv bord är lika enkelt som att installera de virtuella Windows-datorerna.

Registrera Windows-agenter för virtuella skriv bord genom att göra följande på varje virtuell dator:

1. [Anslut till den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Ladda ned och installera Windows-agenten för virtuella skriv bord.
   - Hämta Windows-agenten för [virtuella skriv bord](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Högerklicka på det nedladdade installations programmet, Välj **Egenskaper**, Välj avblockera och välj sedan **OK**. Detta gör att systemet kan lita på installations programmet.
   - Kör installations programmet. När du uppmanas att ange registrerings-token anger du det värde som du fick från cmdleten **export-RdsRegistrationInfo** .
3. Ladda ned och installera Windows Virtual Desktop agent start program.
   - Hämta [Start programmet för Windows Virtual Desktop agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Högerklicka på det nedladdade installations programmet, Välj **Egenskaper**, Välj avblockera och välj sedan **OK**. Detta gör att systemet kan lita på installations programmet.
   - Kör installations programmet.

>[!IMPORTANT]
>Vi rekommenderar att du inte öppnar den inkommande port 3389 på dina virtuella datorer för att skydda din Windows-miljö för virtuella skriv bord i Azure. Virtuella Windows-datorer kräver inte en öppen inkommande port 3389 för att användare ska kunna komma åt värddatorns virtuella datorer. Om du måste öppna port 3389 för fel söknings syfte rekommenderar vi att du använder [just-in-Time VM-åtkomst](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en modempool kan du fylla den med RemoteApp-programmet. Mer information om hur du hanterar appar i Windows Virtual Desktop finns i själv studie kursen Hantera app-grupper.

> [!div class="nextstepaction"]
> [Själv studie kurs för hantering av app-grupper](./manage-app-groups.md)
