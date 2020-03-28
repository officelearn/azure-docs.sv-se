---
title: Windows Virtual Desktop-värdpool Azure Marketplace – Azure
description: Så här skapar du en värdpool för Windows Virtual Desktop med hjälp av Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238624"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Självstudiekurs: Skapa en värdpool med hjälp av Azure Marketplace

I den här självstudien får du lära dig hur du skapar en värdpool i en Windows Virtual Desktop-klient med hjälp av ett Microsoft Azure Marketplace-erbjudande.

Värdpooler är en samling av en eller flera identiska virtuella datorer i Windows Virtual Desktop-klientmiljöer. Varje värdpool kan innehålla en appgrupp som användarna kan interagera med på samma sätt som på ett fysiskt skrivbord.

Uppgifterna i den här självstudien omfattar:

> [!div class="checklist"]
>
> * Skapa en värdpool i Windows Virtual Desktop.
> * Skapa en resursgrupp med virtuella datorer i en Azure-prenumeration.
> * Gå med i de virtuella datorerna till Active Directory-domänen.
> * Registrera de virtuella datorerna med Windows Virtual Desktop.

## <a name="prerequisites"></a>Krav

* En klient i Virtual Desktop. En tidigare [självstudie har](tenant-setup-azure-active-directory.md) skapat en klientorganisation.
* [Windows Virtual Desktop PowerShell-modul](/powershell/windows-virtual-desktop/overview/).

När du har den här modulen kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Kör Azure Marketplace-erbjudandet för att etablera en ny värdpool

Så här kör du Azure Marketplace-erbjudandet för att etablera en ny värdpool:

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
1. Ange **Virtuellt skrivbord i Windows Virtual Desktop** i sökfönstret på Marketplace.
1. Välj **Windows Virtual Desktop - Etablera en värdpool**och välj sedan **Skapa**.

Därefter följer du instruktionerna i nästa avsnitt för att ange informationen för lämpliga flikar.

### <a name="basics"></a>Grundläggande inställningar

Så här gör du för fliken **Grunderna:**

1. Välj en **prenumeration**.
1. För **resursgrupp**väljer du **Skapa nytt** och ger ett namn för den nya resursgruppen.
1. Välj en **region**.
1. Ange ett namn på värdpoolen som är unik i Windows Virtual Desktop-klienten.
1. Välj **skrivbordstyp**. Om du väljer **Personal**tilldelas varje användare som ansluter till den här värdpoolen permanent till en virtuell dator.
1. Ange användare som kan logga in på Windows Virtual Desktop-klienter och komma åt ett skrivbord. Använd en kommaavgränsad lista. Om du till exempel `user1@contoso.com` vill `user2@contoso.com` tilldela och komma åt anger du*`user1@contoso.com,user2@contoso.com`*
1. För **tjänstmetadataplats**väljer du samma plats som det virtuella nätverket som har anslutning till Active Directory-servern.

   >[!IMPORTANT]
   >Om du använder en ren Azure Active Directory Domain Services (Azure AD DS) och Azure Active Directory (Azure AD) lösning, se till att distribuera din värdpool i samma region som din Azure AD DS för att undvika domän-anslutning och autentiseringsuppgifter fel.

1. Välj **Nästa: Konfigurera virtuella datorer**.

### <a name="configure-virtual-machines"></a>Konfigurera virtuella datorer

För fliken **Konfigurera virtuella datorer:**

1. Antingen acceptera standardinställningarna eller anpassa antalet och storleken på de virtuella datorerna.

    >[!NOTE]
    >Om den specifika storleken på den virtuella datorn du letar efter inte visas i storleksväljaren beror det på att vi inte har tagit ombord den på Azure Marketplace-verktyget ännu. Om du vill begära en storlek skapar du en begäran eller upvote en befintlig begäran i [Windows Virtual Desktop UserVoice-forumet](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Ange ett prefix för namnen på de virtuella datorerna. Om du till exempel anger *prefix*kallas de virtuella datorerna **prefix-0**, **prefix-1**och så vidare.
1. Välj **Nästa: Inställningar för virtuella datorer**.

### <a name="virtual-machine-settings"></a>Inställningar för virtuella datorer

För **fliken Inställningar för virtuella datorer:**

1. För **Bildkälla**väljer du källan och anger lämplig information för hur du hittar den och hur den ska lagras. Alternativen skiljer sig åt för Blob-lagring, Hanterad bild och Galleri.

   Om du väljer att inte använda hanterade diskar väljer du det lagringskonto som innehåller *VHD-filen.*
1. Ange användarens huvudnamn och lösenord. Det här kontot måste vara det domänkonto som ska ansluta de virtuella datorerna till Active Directory-domänen. Samma användarnamn och lösenord kommer att skapas på de virtuella datorerna som ett lokalt konto. Du kan återställa dessa lokala konton senare.

   >[!NOTE]
   > Om du ansluter dina virtuella datorer till en Azure AD DS-miljö kontrollerar du att domänanslutningsanvändaren är medlem i [gruppen AAD DC Administrators](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Kontot måste också vara en del av Azure AD DS-hanterad domän eller Azure AD-klientorganisation. Konton från externa kataloger som är associerade med din Azure AD-klient kan inte autentisera korrekt under domänanslutningsprocessen.

1. Välj det **virtuella nätverk** som har anslutning till Active Directory-servern och välj sedan ett undernät som ska vara värd för de virtuella datorerna.
1. Välj **Nästa: Information om virtuellt skrivbord i Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Information om Windows Virtual Desktop-klientorganisation

Information om **klientinformation för Windows Virtual Desktop:**

1. För **Klientgruppnamnet för Windows Virtual Desktop**anger du namnet på klientgruppen som innehåller din klientorganisation. Lämna det som standard om du inte angav ett specifikt klientgruppnamn.
1. För **Windows Virtual Desktop-klientnamn**anger du namnet på klienten där du ska skapa den här värdpoolen.
1. Ange vilken typ av autentiseringsuppgifter som du vill använda för att autentisera som Windows Virtual Desktop-klient rds-ägare. Ange UPN- eller Tjänstens huvudnamn och ett lösenord.

   Om du har slutfört [huvudnamnen för skapa tjänsten och rolltilldelningar med PowerShell-självstudiekurs](./create-service-principal-role-powershell.md)väljer du **Tjänstens huvudnamn**.

1. För **tjänsthuvudnamn**, för **Azure AD-klient-ID**anger du klientadministratörskontot för Azure AD-instansen som innehåller tjänstens huvudnamn. Endast tjänsthuvudnamn med lösenordsautentiseringstillstånd stöds.
1. Välj **Nästa: Granska + skapa**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Slutför installationen och skapa den virtuella datorn

Granska inställningsinformationen i **Granska och skapa.** Om du behöver ändra något går du tillbaka och gör ändringar. När du är klar väljer du **Skapa** för att distribuera värdpoolen.

Beroende på hur många virtuella datorer du skapar kan den här processen ta 30 minuter eller mer att slutföra.

>[!IMPORTANT]
> För att skydda din Windows Virtual Desktop-miljö i Azure rekommenderar vi att du inte öppnar inkommande port 3389 på dina virtuella datorer. Windows Virtual Desktop kräver inte en öppen inkommande port 3389 för att användare ska komma åt värdpoolens virtuella datorer.
>
> Om du måste öppna port 3389 för felsökning rekommenderar vi att du använder just-in-time-åtkomst. Mer information finns [i Skydda dina hanteringsportar med just-in-time-åtkomst](../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Valfritt) Tilldela ytterligare användare till programgruppen för skrivbord

När Azure Marketplace har skapat poolen kan du tilldela fler användare till skrivbordsprogramgruppen. Om du inte vill lägga till fler hoppar du över det här avsnittet.

Så här tilldelar du användare till programgruppen för skrivbordet:

1. Öppna ett PowerShell-fönster.

1. Kör följande kommando för att logga in på Windows Virtual Desktop-miljö:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Lägg till användare i programgruppen för skrivbordet med det här kommandot:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Användarens UPN ska matcha användarens identitet i Azure AD, till exempel *user1@contoso.com*. Om du vill lägga till flera användare kör du kommandot för varje användare.

Användare som du lägger till i skrivbordsprogramgruppen kan logga in på Windows Virtual Desktop med fjärrskrivbordsklienter som stöds och se en resurs för ett sessionsskrivbord.

Här är de aktuella klienterna som stöds:

* [Fjärrskrivbordsklient för Windows 7 och Windows 10](connect-windows-7-and-10.md)
* [Webbklient för Windows Virtual Desktop](connect-web.md)

## <a name="next-steps"></a>Nästa steg

Du har gjort en värdpool och tilldelat användare åtkomst till skrivbordet. Du kan fylla din värdpool med RemoteApp-program. Mer information om hur du hanterar appar i Windows Virtual Desktop finns i den här självstudien:

> [!div class="nextstepaction"]
> [Självstudiekurs för appgrupper](./manage-app-groups.md)
