---
title: "Använda SSH-nycklar med Windows för virtuella Linux-datorer | Microsoft Docs"
description: "Lär dig hur du skapar och använder SSH-nycklar på en Windows-dator för att ansluta till en virtuell Linux-dator på Azure."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: 66837a3a153cda041f5351c52c8ccb1f8ccfea50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Hur man använda SSH-nycklar med Windows på Azure
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

När du ansluter till Linux virtuella datorer (VM) i Azure, bör du använda [offentliga nycklar](https://wikipedia.org/wiki/Public-key_cryptography) att tillhandahålla ett säkrare sätt att logga in till Linux-VM. Den här processen omfattar ett offentliga och privata nyckelutbyte för kommandot secure shell (SSH) för att autentisera dig själv i stället för användarnamn och lösenord. Lösenord är sårbara för brute force-attacker, särskilt på virtuella datorer mot Internet, till exempel webbservrar. Den här artikeln innehåller en översikt över SSH-nycklar och hur du skapar lämpliga nycklar på en Windows-dator.

## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar
Du kan på ett säkert sätt logga in till Linux-VM med hjälp av offentliga och privata nycklar:

* Den **offentliga nyckel** är placerad på ditt Linux-VM eller någon annan tjänst som du vill använda med offentliga nycklar.
* Den **privata nyckel** är vad du presentera för Linux-VM när du loggar in, för att verifiera din identitet. Skydda den privata nyckeln. Dela den inte med andra.

Dessa offentliga och privata nycklar kan användas på flera virtuella datorer och tjänster. Du behöver inte ett nyckelpar för varje virtuell dator eller tjänst som du vill använda. En detaljerad översikt finns [offentliga nycklar](https://wikipedia.org/wiki/Public-key_cryptography).

SSH är en krypterad anslutningsprotokoll som tillåter säker inloggning via oskyddat anslutningar. Det är standardprotokollet för anslutning för virtuella Linux-datorer finns i Azure. Även om SSH själva ger en krypterad anslutning, lämnar med hjälp av lösenord med SSH-anslutningar fortfarande den virtuella datorn sårbar för brute force-attacker eller att gissa lösenord. Det är en säkrare och önskade metod för att ansluta till en virtuell dator med hjälp av SSH med hjälp av offentliga och privata nycklarna, även kallat SSH-nycklar.

Om du inte vill använda SSH-nycklar kan du fortfarande logga in till din virtuella Linux-datorer med ett lösenord. Om den virtuella datorn inte visas för Internet, kan det vara tillräckligt att använda lösenord. Men behöver du fortfarande hantera lösenord för varje Linux-VM och underhålla felfri lösenordsprinciper och praxis som minsta längd på lösenord och regelbundet uppdaterar dem. Användning av SSH-nycklar minskar den för att hantera enskilda autentiseringsuppgifter över flera virtuella datorer.

## <a name="windows-packages-and-ssh-clients"></a>Windows-paket och SSH-klienter
Du ansluter till och hantera virtuella Linux-datorer i Azure med hjälp av en **SSH-klienten**. Windows-datorer har inte normalt en SSH-klienten installerad. Uppdatering av Windows 10 årsdagar läggs Bash för Windows och den senaste uppdateringen av Windows 10-skapare ger ytterligare uppdateringar. Den här Windows-undersystem för Linux kan du köra och åtkomst verktyg, till exempel en SSH-klient internt i ett Bash-gränssnitt. Sedan kan du följa alla Linux-dokument som [hur du skapar SSH-nyckelpar för Linux](mac-create-ssh-keys.md). Bash för Windows är fortfarande under utveckling och betraktas som en betaversion. Mer information om Bash för Windows finns [Bash på Ubuntu på Windows](https://msdn.microsoft.com/commandline/wsl/about).

Om du vill använda något annat än Bash för Windows, vanliga Windows SSH klienter kan du installera ingår i följande paket:

* [Git för Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>Vilka viktiga filer behöver du skapa?
Azure kräver minst 2048-bitars **ssh-rsa** formaterad offentliga och privata nycklar. Om du hanterar Azure-resurser med hjälp av den klassiska distributionsmodellen kan du också behöva skapa en PEM (`.pem` filen).

Här följer scenarier för distribution och vilka typer av filer som används i varje:

1. **SSH-rsa** nycklarna är obligatoriska för några med den [Azure-portalen](https://portal.azure.com), och Resource Manager distributioner med hjälp av den [Azure CLI](../../cli-install-nodejs.md).
   * Nyckeln är oftast alla de flesta användare behöver.
2. En `.pem` filen behövs för att skapa virtuella datorer med hjälp av den klassiska distributionen. De här nycklarna stöds i klassiska distributioner när de [Azure-portalen](https://portal.azure.com) eller [Azure CLI](../../cli-install-nodejs.md).
   * Behöver du bara skapa dessa ytterligare nycklar och certifikat om du hanterar resurser som har skapats med hjälp av den klassiska distributionsmodellen.

## <a name="install-git-for-windows"></a>Installera Git för Windows
I föregående avsnitt visas flera paket som innehåller den `openssl` tool för Windows. Det här verktyget behövs för att skapa offentliga och privata nycklar. I följande exempel innehåller information om hur du installerar och använder **Git för Windows**, men du kan välja det paket som du föredrar. **Git för Windows** ger dig tillgång till vissa ytterligare programvara med öppen källkod ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) verktyg och hjälpmedel som kan vara användbar när du arbetar med virtuella Linux-datorer.

1. Hämta och installera **Git för Windows** från följande plats: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).
2. Acceptera standardalternativen under installationen om du inte specifikt behöver ändra dem.
3. Kör **Git Bash** från den **Start-menyn** > **Git** > **Git Bash**. Konsolen ser ut ungefär så här:

    ![Git för Windows Bash-gränssnitt](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Skapa en privat nyckel
1. I din **Git Bash** fönster, Använd `openssl.exe` att skapa en privat nyckel. I följande exempel skapas en nyckel som heter `myPrivateKey` och certifikatet med namnet `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    Utdata liknar följande exempel:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Om bash rapporterar ett fel, försök att öppna en ny **Git Bash** fönster med förhöjda privilegier. Kör sedan den `openssl` kommando.

2. Besvara anvisningarna för landets namn, plats, organisationsnamn och så vidare.
3. Din nya privata nyckeln och certifikatet skapas i den aktuella arbetskatalogen. Som en säkerhetsåtgärd bör du ange behörigheten på din privata nyckel så att bara du har åtkomst till den:

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. Den [nästa avsnitt](#create-a-private-key-for-putty) information med hjälp av PuTTYgen både visa och använda den offentliga nyckeln och skapa en privat nyckel som är specifika för att använda PuTTY för att SSH till Linux virtuella datorer. Följande kommando skapar en offentlig nyckelfil med namnet `myPublicKey.key` som du kan använda direkt:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Om du behöver hantera klassiska resurser kan du konvertera den `myCert.pem` till `myCert.cer` (DER-kodad X509 certifikat). Det här valfria steget endast utföra om du behöver hantera specifikt äldre klassiska resurser.

    Konvertera certifikatet med hjälp av följande kommando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Skapa en privat nyckel för PuTTY
PuTTY är en gemensam SSH-klient för Windows. Du kan använda SSH-klienten som du vill. Om du vill använda PuTTY måste du skapa ytterligare en typ av nyckel - en PuTTY privata nyckel (PPK). Hoppa över det här avsnittet om du inte vill använda PuTTY.

I följande exempel skapas den här ytterligare privata nyckeln för PuTTY för att använda:

1. Använd **Git Bash** Konvertera din privata nyckel till en privat RSA-nyckel som PuTTYgen kan förstå. I följande exempel skapas en nyckel som heter `myPrivateKey_rsa` från den befintliga nyckeln med namnet `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Som en säkerhetsåtgärd bör du ange behörigheten på din privata nyckel så att bara du har åtkomst till den:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Hämta och kör PuTTYgen från följande plats: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Klicka på menyn: **filen** > **belastningen privat nyckel**
4. Leta upp din privata nyckel (`myPrivateKey_rsa` i föregående exempel). Standardkatalogen när du startar **Git Bash** är `C:\Users\%username%`. Ändra filen filtret för att visa **alla filer (\*.\*)** :

    ![Läsa in befintlig privat nyckel i PuTTYgen](./media/ssh-from-windows/load-private-key.png)
5. Klicka på **öppna**. En uppmaning anger att nyckeln har importerats:

    ![Importerats nyckel till PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. Klicka på **OK** att stänga Kommandotolken.
7. Den offentliga nyckeln visas överst i den **PuTTYgen** fönster. Kopiera och klistra in den här offentliga nyckeln i Azure-portalen eller Azure Resource Manager-mall när du skapar en Linux VM. Du kan också klicka på **spara offentlig nyckel** att spara en kopia på datorn:

    ![Spara PuTTY fil för offentlig nyckel](./media/ssh-from-windows/save-public-key.png)

    I följande exempel visas hur du kan kopiera och klistra in den här offentliga nyckeln i Azure-portalen när du skapar en Linux VM. Den offentliga nyckeln är vanligtvis lagras sedan i `~/.ssh/authorized_keys` på den nya virtuella datorn.

    ![Använda offentliga nyckel när du skapar en virtuell dator i Azure-portalen](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. Tillbaka i **PuTTYgen**, klickar du på **Spara privat nyckel**:

    ![Spara filen med PuTTY privata nyckel](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > En uppmaning som frågar om du vill fortsätta utan att ange en lösenfras för nyckeln. Det är en lösenfras som ett lösenord som är ansluten till din privata nyckel. Även om någon försöker hämta din privata nyckel skulle de fortfarande inte kan autentisera med bara nyckeln. De måste också lösenfrasen. Utan en lösenfras om någon erhåller din privata nyckel, kan de logga in på en virtuell dator eller tjänst som använder nyckeln. Vi rekommenderar att du skapar en lösenfras. Men om du glömmer bort lösenfrasen finns inget sätt att återställa den.
   >
   >

    Om du vill ange en lösenfras klickar du på **nr**, ange en lösenfras i PuTTYgen-fönstret och klicka sedan på **Spara privat nyckel** igen. Annars klickar du på **Ja** fortsätta utan att ange valfri lösenfras.
9. Ange ett namn och en plats att spara PPK-filen.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Använd Putty för att SSH till en Linux-dator
PuTTY är igen, en vanliga SSH-klient för Windows. Du kan använda SSH-klienten som du vill. Följ anvisningarna nedan hur du använder din privata nyckel för att autentisera dina virtuella Azure-datorn via SSH. Stegen är ungefär som i andra SSH key-klienter som behöver läsa in din privata nyckel att autentisera SSH-anslutningen.

1. Hämta och kör putty från följande plats: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Fyll i värdnamn eller IP-adressen för den virtuella datorn från Azure portal:

    ![Öppna ny PuTTY anslutning](./media/ssh-from-windows/putty-new-connection.png)
3. Innan du väljer **öppna**, klickar du på **anslutning** > **SSH** > **Auth** fliken. Bläddra till och markera din privata nyckel:

    ![Välj din PuTTY privata nyckel för autentisering](./media/ssh-from-windows/putty-auth-dialog.png)
4. Klicka på **öppna** att ansluta till den virtuella datorn

## <a name="next-steps"></a>Nästa steg
Du kan också generera offentliga och privata nycklar [använder OS X- och Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Mer information om Bash för Windows och fördelarna med OSS verktyg är tillgänglig på Windows-dator finns [Bash på Ubuntu på Windows](https://msdn.microsoft.com/commandline/wsl/about).

Om du har problem med SSH att ansluta till din virtuella Linux-datorer, se [felsökning av SSH-anslutningar till en Azure Linux VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
