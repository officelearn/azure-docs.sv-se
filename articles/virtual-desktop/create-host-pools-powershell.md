---
title: Skapa en förhandsversion för virtuella skrivbord i Windows-värd-pool med PowerShell – Azure
description: Så här skapar du en värd-pool i förhandsversion för virtuella skrivbord i Windows med PowerShell-cmdletar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 2af9df4771d58f2288820dad8ef8d7ac84deb8ae
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258478"
---
# <a name="create-a-host-pool-with-powershell"></a>Skapa en värdpool med PowerShell

Värd-pooler är en samling av en eller flera identiska virtuella datorer i förhandsversion för virtuella skrivbord i Windows klient-miljöer. Varje värd-pool kan innehålla en appgrupp som användare kan interagera med precis som på en fysisk dator.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Använd PowerShell-klienten för att skapa en värd-pool

Först [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

Kör följande cmdlet för att logga in på den virtuella skrivbordet i Windows-miljön

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Därefter kör du följande cmdlet för att ange kontexten för din klient-grupp. Om du inte har namnet på gruppen klient, sannolikt din klient i ”standard klient gruppen” så att du kan hoppa över denna cmdlet.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Kör denna cmdlet för att skapa en ny pool för värden i din virtuella skrivbordet i Windows-klient:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Kör cmdleten nästa för att skapa en registreringstoken för att auktorisera en värd för fjärrskrivbordssession ska ansluta till värd-poolen och spara den till en ny fil på din lokala dator. Du kan ange hur länge fcm ska vara giltig med hjälp av parametern - ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Efter det att köra denna cmdlet om du vill lägga till Azure Active Directory-användare i gruppen standard skrivbordsapp för värd-poolen.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Den **Lägg till RdsAppGroupUser** cmdlet inte stöd för att lägga till säkerhetsgrupper och endast lägger till en användare i taget till app-gruppen. Om du vill lägga till flera användare i gruppen app kan du köra cmdleten med lämpliga huvudnamn.

Kör följande cmdlet för att exportera fcm till en variabel som du ska använda senare i [registrera virtuella datorer till virtuella Windows-skrivbordet värd poolen](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Skapa virtuella datorer för värd-pool

Nu kan du skapa en Azure-dator som kan kopplas till din virtuella Windows-skrivbordet värd-pool.

Du kan skapa en virtuell dator på flera olika sätt:

- [Skapa en virtuell dator från en avbildning i Azure-galleriet](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Skapa en virtuell dator från en hanterad avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Skapa en virtuell dator från en ohanterad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Förbereda virtuella datorer för Windows Virtual Desktop förhandsversion agentinstallationer

Måste du göra följande för att förbereda dina virtuella datorer innan du kan installera agenter för virtuella Windows-skrivbordet och registrera virtuella datorer till din virtuella Windows-skrivbordet värd pool:

- Du måste domänanslutning datorn. På så sätt kan inkommande virtuella Windows-skrivbordet användare mappas från deras Azure Active Directory-konto till deras Active Directory-konto och att få har tillgång till den virtuella datorn.
- Om den virtuella datorn kör ett Windows Server-Operativsystemet måste du installera rollen fjärråtkomst värd för fjärrskrivbordssession (RDSH). RDSH-rollen kan virtuella Windows-skrivbordet agenterna ska installeras.

Du har domänanslutning, gör du följande på varje virtuell dator:

1. [Ansluta till den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) med de autentiseringsuppgifter som du angav när du skapar den virtuella datorn.
2. På den virtuella datorn, startar **Kontrollpanelen** och välj **System**.
3. Välj **datornamn**väljer **ändra inställningarna för**, och välj sedan **ändringen...**
4. Välj **domän** och ange sedan Active Directory-domänen i det virtuella nätverket.
5. Autentisera med ett domänkonto som har behörighet att domänanslutning datorer.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Registrera virtuella datorer till poolen förhandsversion för virtuella skrivbord i Windows-värd

Registrerar de virtuella datorerna till en pool med virtuella Windows-skrivbordet värden är lika enkelt som att installera agenter för virtuella Windows-skrivbordet.

Gör följande på varje virtuell dator för att registrera virtuellt skrivbord i Windows-agenter:

1. [Ansluta till den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) med de autentiseringsuppgifter som du angav när du skapar den virtuella datorn.
2. Ladda ned och installera Windows-agenten för virtuella skrivbord.
   - Ladda ned den [Windows-agenten för virtuella skrivbord](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Högerklicka på det nedladdade installationsprogrammet, Välj **egenskaper**väljer **avblockera**och välj sedan **OK**. Detta gör att systemet ska lita på installationsprogrammet.
   - Kör installationsprogrammet. När installationsprogrammet frågar du efter registreringstoken, ange det värde som du fick från den **Export RdsRegistrationInfo** cmdlet.
3. Hämta och installera Windows-startprogrammet för agenten av virtuella skrivbord.
   - Ladda ned den [Windows virtuella skrivbord agenten startprogrammet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Högerklicka på det nedladdade installationsprogrammet, Välj **egenskaper**väljer **avblockera**och välj sedan **OK**. Detta gör att systemet ska lita på installationsprogrammet.
   - Kör installationsprogrammet.
4. Installera eller aktivera sida-vid-sida-stack virtuella Windows-skrivbordet. Stegen är olika beroende på vilken operativsystemversion som den virtuella datorn använder.
   - Om den virtuella datorns operativsystem är Windows Server 2016:
     - Ladda ned den [virtuella Windows-skrivbordet sida-vid-sida stack](https://go.microsoft.com/fwlink/?linkid=2084270).
     - Högerklicka på det nedladdade installationsprogrammet, Välj **egenskaper**väljer **avblockera**och välj sedan **OK**. Detta gör att systemet ska lita på installationsprogrammet.
     - Kör installationsprogrammet.
   - Om den virtuella datorns OS är Windows 10 1809 eller senare eller Windows Server 2019 eller senare:
     - Ladda ned den [skriptet](https://go.microsoft.com/fwlink/?linkid=2084268) att aktivera sida-vid-sida-stack.
     - Högerklicka på det hämta skriptet, Välj **egenskaper**väljer **avblockera**och välj sedan **OK**. Detta gör att systemet ska lita på skriptet.
     - Från den **starta** menyn, Sök efter Windows PowerShell ISE, högerklicka på den och välj sedan **kör som administratör**.
     - Välj **filen**, sedan **öppen...** , och leta upp PowerShell-skriptet från hämtade filer och öppna den.
     - Välj grön uppspelningsknappen för att köra skriptet.

>[!IMPORTANT]
>Om du vill att skydda din miljö för virtuella Windows-skrivbordet i Azure, rekommenderar vi du inte öppna inkommande port 3389 på dina virtuella datorer. Virtuella Windows-skrivbordet kräver inte en öppen inkommande port 3389 för användare att komma åt värden poolens virtuella datorer. Om du måste öppna port 3389 för felsökningsändamål kan vi rekommenderar att du använder [åtkomst till Virtuella just-in-time](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Nästa steg

Nu när du har gjort poolen värden, kan du fylla i den med RemoteApps. Mer information om hur du hanterar appar i virtuella Windows-skrivbordet finns i Hantera app grupper självstudien.

> [!div class="nextstepaction"]
> [Hantera grupper självstudier](./manage-app-groups.md)
