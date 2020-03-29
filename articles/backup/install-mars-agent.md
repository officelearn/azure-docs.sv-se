---
title: Installera MARS-agenten (Microsoft Azure Recovery Services)
description: Lär dig hur du installerar MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera Windows-datorer.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: b9a6791709d5aff82d11bbf10e5f084fd8c1a000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247766"
---
# <a name="install-the-azure-backup-mars-agent"></a>Installera AZURE Backup MARS-agenten

I den här artikeln beskrivs hur du installerar MARS-agenten (Microsoft Azure Recovery Services). MARS kallas även Azure Backup-agenten.

## <a name="about-the-mars-agent"></a>Om MARS-agenten

Azure Backup använder MARS-agenten för att säkerhetskopiera filer, mappar och systemtillstånd från lokala datorer och virtuella Azure-datorer. Dessa säkerhetskopior lagras i ett Recovery Services-valv i Azure. Du kan köra agenten:

* Direkt på lokala Windows-maskiner. Dessa datorer kan säkerhetskopiera direkt till ett Recovery Services-valv i Azure.
* På virtuella Azure-datorer som kör Windows sida vid sida med tillägget för säkerhetskopiering av Azure VM. Agenten säkerhetskopierar specifika filer och mappar på den virtuella datorn.
* På en MABS-instans (Microsoft Azure Backup Server) eller en DPM-server (System Center Data Protection Manager). I det här fallet säkerhetskopierar datorer och arbetsbelastningar till MABS eller Data Protection Manager. Sedan använder MABS eller Data Protection Manager MARS-agenten för att säkerhetskopiera till ett valv i Azure.

Vilka data som är tillgängliga för säkerhetskopiering beror på var agenten är installerad.

> [!NOTE]
> I allmänhet säkerhetskopierar du en virtuell Azure-dator med hjälp av ett Azure Backup-tillägg på den virtuella datorn. Den här metoden säkerhetskopierar hela den virtuella datorn. Om du vill säkerhetskopiera specifika filer och mappar på den virtuella datorn installerar och använder du MARS-agenten bredvid tillägget. Mer information finns [i Arkitektur för en inbyggd Azure VM-säkerhetskopia](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Steg för säkerhetskopiering](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Innan du börjar

* Lär dig mer om hur [Azure Backup använder MARS-agenten för att säkerhetskopiera Windows-datorer](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Lär dig mer om [säkerhetskopieringsarkitekturen](backup-architecture.md#architecture-back-up-to-dpmmabs) som kör MARS-agenten på en sekundär MABS- eller Data Protection Manager-server.
* Granska [vad som stöds och vad du kan säkerhetskopiera](backup-support-matrix-mars-agent.md) av MARS-agenten.
* Se till att du har ett Azure-konto om du behöver säkerhetskopiera en server eller klient till Azure. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/free/) bara några minuter.
* Verifiera internetåtkomsten på de datorer som du vill säkerhetskopiera.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Ändra lagringsreplikering

Som standard använder valv [geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Om valvet är din primära säkerhetskopieringsmekanism rekommenderar vi att du använder GRS.
* Du kan använda [lokalt redundant lagring (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att minska Azure-lagringskostnader.

Så här ändrar du lagringsreplikeringstypen:

1. Välj **Egenskaper** under avsnittet **Inställningar** i det nya valvet.

1. Välj **Uppdatera**under **Konfiguration av säkerhetskopiering**på sidan **Egenskaper** .

1. Välj lagringsreplikeringstyp och välj **Spara**.

    ![Uppdatera konfiguration för säkerhetskopiering](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Du kan inte ändra lagringsreplikeringstypen när valvet har konfigurerats och innehåller säkerhetskopior. Om du vill göra detta måste du återskapa valvet.
>

### <a name="verify-internet-access"></a>Verifiera internetåtkomst

Om datorn har begränsad internetåtkomst kontrollerar du att brandväggsinställningarna på datorn eller proxyn tillåter följande webbadresser och IP-adresser:

*  URL:er
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP-adresser
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Använda Azure ExpressRoute

Du kan säkerhetskopiera dina data via Azure ExpressRoute med hjälp av offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering. Säkerhetskopiering via privat peering stöds inte.

Om du vill använda offentlig peering säkerställer du först åtkomst till följande domäner och adresser:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Om du vill använda Microsoft-peering väljer du följande tjänster, regioner och relevanta communityvärden:

* Azure Active Directory (12076:5060)
* Azure-regionen, beroende på platsen för ditt Recovery Services-valv
* Azure Storage, beroende på platsen för ditt Recovery Services-valv

Mer information finns i [ExpressRoute-routningskrav](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> Offentlig peering är föråldrad för nya kretsar.

Alla föregående webbadresser och IP-adresser använder HTTPS-protokollet på port 443.

### <a name="private-endpoints"></a>Privata slutpunkter

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Ladda ner MARS-agenten

Hämta MARS-agenten så att du kan installera den på de datorer som du vill säkerhetskopiera.

Om du redan har installerat agenten på alla datorer kontrollerar du att du kör den senaste versionen av agenten. Hitta den senaste versionen i portalen, eller gå direkt till [nedladdningen](https://aka.ms/azurebackup_agent).

1. Välj **Säkerhetskopiering**under **Komma igång**i valvet .

    ![Öppna säkerhetskopieringsmålet](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Under **Var körs din arbetsbelastning?** **On-premises** Välj det här alternativet även om du vill installera MARS-agenten på en virtuell Azure-dator.
1. Under **Vad vill du säkerhetskopiera?** **Files and folders** Du kan också välja **Systemtillstånd**. Många andra alternativ är tillgängliga, men dessa alternativ stöds bara om du kör en sekundär säkerhetskopieringsserver. Välj **Förbered infrastruktur**.

    ![Konfigurera filer och mappar](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. För **Förbereda infrastruktur**, ladda ner MARS-agenten under Install Recovery Services **agent.**

    ![Förbered infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. Välj Spara på **hämtningsmenyn**. Som standard sparas filen *MARSagentinstaller.exe* i mappen för nedladdningar.

1. Välj **Hämta eller använd den senaste Recovery Services-agenten**och hämta autentiseringsuppgifterna för valvet.

    ![Ladda ned autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Välj **Spara**. Filen hämtas till mappen Nedladdningar. Du kan inte öppna autentiseringsfilen för valvet.

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Kör filen *MARSagentinstaller.exe* på de datorer som du vill säkerhetskopiera.
1. Välj **Installationsinställningar i**guiden Konfigurera MARS-agent . Där väljer du var agenten ska installeras och väljer en plats för cacheminnet. Välj sedan **Nästa**.
   * Azure Backup använder cacheminnet för att lagra ögonblicksbilder av data innan de skickas till Azure.
   * Cacheplatsen bör ha ledigt utrymme som motsvarar minst 5 procent av storleken på de data som du säkerhetskopierar.

    ![Välj installationsinställningar i guiden Konfigurera MARS-agent](./media/backup-configure-vault/mars1.png)

1. För **proxykonfiguration**anger du hur agenten som körs på Windows-datorn ska ansluta till internet. Välj sedan **Nästa**.

   * Om du använder en anpassad proxy anger du nödvändiga proxyinställningar och autentiseringsuppgifter.
   * Kom ihåg att agenten behöver åtkomst till [specifika webbadresser](#before-you-start).

    ![Konfigurera internetåtkomst i MARS-guiden](./media/backup-configure-vault/mars2.png)

1. För **installation**går du igenom förutsättningarna och väljer **Installera**.
1. När agenten har installerats väljer du **Fortsätt till registrering**.
1. Bläddra till och välj den autentiseringsfil som du hämtade i **Register Server Wizard** > **Vault Identification.** Välj sedan **Nästa**.

    ![Lägga till autentiseringsuppgifter för valvet med guiden Registrera server](./media/backup-configure-vault/register1.png)

1. På sidan **Krypteringsinställning** anger du en lösenfras som ska användas för att kryptera och dekryptera säkerhetskopior för datorn.

    * Spara lösenfrasen på en säker plats. Du behöver den för att återställa en säkerhetskopia.
    * Om du förlorar eller glömmer lösenfrasen kan Microsoft inte hjälpa dig att återställa säkerhetskopieringsdata.

1. Välj **Slutför**. Agenten är nu installerad och din maskin är registrerad i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar Windows-datorer med hjälp av Azure Backup MARS-agenten](backup-windows-with-mars-agent.md)
